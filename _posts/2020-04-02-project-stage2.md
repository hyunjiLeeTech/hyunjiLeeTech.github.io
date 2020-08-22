---
layout: post
title: "Project Stage 2 - Profiling Butteraugli"
date: 2020-04-02 10:39:10
author: Jane Hyunji Lee
categories: ['SPO600', 'Butteraugli Optimization']
tags: ['SPO600', 'Butteraugli']
permalink: /project-stage2-profiling-butteraugli
---
### Introduction
This post is about the [project][Winter 2020 SPO600 Project] I am working on in the SPO600 class. The project is about the optimization an open-source software. I need to build, benchmark, profile, and optimize selected open-source software. The software I have selected is named 'Butteraugli'. The 'Butteraugli' is the project Google created that estimates the psychovisual similarity of two images. The detailed information about this project is written on the [Google Opensource website about the Butteraugli][Google Opensource website about Butteraugli]. The way to compile this project and the result of benchmarking this project are written in my previous post: [Project Stage 1 - Compiling and Benchmarking Butteraugli][Project Stage1]. I recommend you to read this previous post to understand this post.

### Profiling Options
There are two representative ways to profile the software: gprof and perf. The [gprof][Gprof] is a performance analysis tool for Unix application. It used a hybrid of both instrumentation and sampling. The [perf][Perf] is a profiler tool for Linux 2.6+ based systems that abstracts away CPU hardware differences in Linux performance measurements and presents a simple command line interface. There are some advantages and disadvantages to use gprof or perf. I will describe them in terms of using perf by referencing the [professor's class][Lecture].

##### The advantages of using perf
* You can get a top-quality, accurate call graph.
* You can get which function uses which functions and how many times the function called.
* You can get the breakdown of how much time was spent in the various functions and that comes from sampling.
* You do not have to change the program


##### The disadvantages of using perf
* You have got to build a special version of your program that includes the instrumentation code the profile generating code
* You cannot just use a regular copy of the program

### How to use gprof and the result of the gprof
#### How to use gprof
I have learned how to used gprof by researching Linux gprof command and watching the professor's class. 

##### 1. Modify the Makefile.
First, you need to add '-pg' into the Makefile. In the case of the butteraugli software, you need to add the code on the CXXFLAGS.
![](https://lh3.googleusercontent.com/LEx0Kka_ORie9rE1ZRiNCCiG1qpgBMEqcKfC8cFAn5jprKS-QitWR4NK6FIecXILBOwtha8ZON8eVyb0RTv0KJ7YlyFwjCjAdHBL1vNMjZnAHp3xfGjTSm6RvF0RarPtz5jFG_-K5w)


##### 2. Rebuild the software
Enter the command 'make clean' and then 'make all'. If it does not work, remove the output files building generated and then enter 'make'.
![](https://lh3.googleusercontent.com/K167JZWlFNMwI_LFD0X-RVmiT0rvJuDjM4SKbujW1B9rDSJLdIXr3eQQe5bvpM8Fer1oOlA2EP_0B_FyW8lwjkbCzaC6PjUkXObThn3_qbd2Kjj4zt4TkxhVCQR7PIQ0GWis0pqwFA)


##### 3. Execute the software
Execute the software using the following command:
```
./butteraugli image1.png image2.png
```
As I used both 5mb size images comparison and 10mb size image comparison for stage 1, I will do both 5mb and 10mb.


##### 4. Open the "gmon.out" file
When the executing is done, the file named 'gmon.out' will be generated. Take a look at using the following command:
```
gprof ./butterauli | less
```
You can also generate a text file using the command 
```
gprof -p butteraugli gmon.out > result.txt 
```

#### Result of the prof
##### Image size: 5mb
![](https://lh3.googleusercontent.com/ysvWWWrg2NGh_g_T_Tr58ZlO3P2db-2o_JlIJDHB08NZ3K7sfGYpXYV4FcwqQU7lQq3PUh9qDquYWU3GtVmRoN7brp8dmejy-_6vGjq5ZG0V3IhMRIYVnkn0cGC8opYmI4ntmm-OFA)


##### Image size: 10mb
![](https://lh3.googleusercontent.com/4vfrbEf0QiG2WJQeHLJzkE6rhKuAVJPXtpu9fK4vAi64cUODALk_eDeJVixXSFcM41K8fy4vVPYcteDIG4D2Kwt5GpHkbB1y5Va_YIfgOi8M80DVtLUdrGiFEpKtsinygDP8TJFNkA)


### How to use perf and the result of the perf
#### How to use perf
##### 1. execute the program using perf report command.
You do not need to change the Makefile if you want to use perf command. Enter the following command: 
```
perf record ./butteraugli image1.png image2.png
```

##### 2. Check the result
Enter perf report to have a look at the result.
```
    perf report
```

#### Result of the perf
##### Image size: 5mb
![](https://lh3.googleusercontent.com/iQDTCQueYLpXN6q7aJYpOLUiQWj5qNL5Xinr7Z8vjwAGpcTd4GQxSdINtocRG--GgdRX6K5dgtNGo-kLmhzygJsWpEl74_uOn221SpWJNu_zIRX073QcmrdsDGANggGOoVmMrXFHBw)

##### Image size: 5mb - Convolution function
![](https://lh3.googleusercontent.com/4gTKizxsUoD5Sq5TEsSPPGiH5wGXfe0VbRXur6rJLJWtgN1fYP__THL4JAb6lrAmML3LfzvZSBGhv8l0P8DzHEiGr6od8zgf-5jZTvxq3rOkKcmAwSFoY5a2lc_73Guig7Nmp4Vqaw)

##### Image size: 10mb
![](https://lh3.googleusercontent.com/Wi5nFO9dzUzI8nthNfmmKXGkAcwlAmhouxyT43O4tApclepOF1bpp72JXj34-8tH9WLdq4NdmmA24YtbplQwEKbLVfuA-n9ZjnlkzdF7Dhxw5X2dwXXDYHVfXbvwhvu4aQGaWkIAaw)

##### Image size: 10mb - Convolution function
![](https://lh3.googleusercontent.com/5XaN52Cg87BHPMVEnhhFNlVTfTrZXQjOp2jQwObk3D_amN0TMJ0smA7vLrKZRA9tEtjhd-eRoUxsDsgDzT_rEVS9XN5hrM5RTTe7JGYxeYTSkTusCLHs85qq7yYy7rm28ToeV06bGg)


#### Caution
Don't forget to erase '-pg' on the Makefile if you do the perf command after you use gprof. It will make the profiling result different.


If you use perf with the '-pg' setting, '_mcount@@GLIBC_2.18' function will be the hottest function which is not correct.

### Conclusion
The hottest functions in the butteraugli software are 'butteraugli::Convolution' and 'std::vector<float, std::allocator<float>>::operator[]'. They take more than 50% time of the total executing time. Therefore, if I optimize those functions, the execution time will be reduced. I will take a look at the code and try to optimize this software. The result of the optimization will be posted on the next stage, Project Stage 3 - Optimization.

### What I have learned from the project stage 2
The most interesting part of this stage is getting the way to profile. I always wonder is there any way to get how long certain function takes to execute the software and how much percentage of time for the overall time. I have fun geting the ways using gprof and perf. Also, both gprof and perf have an amazing part. I can get how many times certain function calls using gprof. By using perf, I can get what part of the code takes a long time. This information is specific and useful. I will look around this function more and try to apply it to the other software.

[Winter 2020 SPO600 Project]: https://wiki.cdot.senecacollege.ca/wiki/Winter_2020_SPO600_Project
[Google Opensource website about Butteraugli]: https://opensource.google/projects/butteraugli
[Github - Butteraugli]: https://github.com/google/butteraugli
[Sample Image website]: https://sample-videos.com/download-sample-png-image.php
[Winter2020 SPO600 Weekly Week4]: https://wiki.cdot.senecacollege.ca/wiki/Winter_2020_SPO600_Weekly_Schedule#Week_4_-_Class_II
[Project Stage1]: https://hyunjileetech.github.io/project-stage1-compiling-and-benchmark
[Gprof]: https://en.wikipedia.org/wiki/Gprof
[Perf]: https://perf.wiki.kernel.org/index.php/Tutorial
[Lecture]: https://www.youtube.com/watch?v=Hip1KtYZKE0&feature=youtu.be