---
layout: post
title: "Project Stage 3: Optimization"
date: 2020-04-18 10:39:10
author: Jane Hyunji Lee
categories: ['SPO600', 'Butteraugli Optimization']
tags: ['SPO600', 'Butteraugli']
permalink: /project-stage3-optimization
---
### Introduction
This is a report for the project stage 3, optimization. Before you read this post, it is vital to read [stage 1][Project Stage1] (compile and benchmark) and stage 2 reports (profiling under [Aarch64][Project Stage2] and [x86_64][Project Stage2: x86-64] architecture) because those are the previous steps for this report. If the results images are not shown on the reports, [this][Project Organize] is the additional post for this blog which contains the links for each result.

### The Project I Selected: Butteraugli
The Butteraugli is the project Google created. It estimates the psychovisual similarity of two images. The detailed information about this project is described on the [Google Opensource website about the Butteraugli][Google Opensource website about Butteraugli]. The source code for this project is located here: [GitHub - Butteraugli][Github - Butteraugli].

### Discover the Points of the Optimization
In stage 2, the results of the profiling show that butteraugli::Convolution is the function that takes the most of the time for compiling. Also, the command perf discovers 'ldr' is the code that takes the most time. Based on the [Aarch64 document][Aarch64 document] from the arm and the course document for the [Aarch64][Course-Aarch64] and the [x86_64][Course-x86-64], the 'ldr' is for loading register from the address pointed to somewhere. It means that if I can reduce the number of loading or the data type in this function, the project can be optimized. I tried to find the function 'Convolution' to look at the code and the function is found in the ['butteraugli.cc' file][butteraugli.cc]. Also, I looked at the function named 'ConvolveBorder' as well because this function is called inside the 'Convolution' function.

When I read the functions named 'Convolution' and 'ConvolveBorder', I have found two main optimization points.
#### 1. Change the implementation
I have found that it used 'int' to load the size of the kernel or the values which will never become the negative value. It can be inefficient in point of the memory because the unsigned value will never be stored for those variables. Therefore, using 'size_t' instead of 'int' can be more efficient for them.

#### 2. Store the values Instead of Calling Multiple Time
I have found that some of the command are executed multiple times. For example, 'in.xsize()' are called multiple times. Therefore, I create another variable to store the in.xsize() and in.ysize() rather than call them multiple times.

### The Code after Optimization
#### Convolution function
![](https://lh3.googleusercontent.com/4np0CN9Y2WX6FKS945GSMcDOtkcHQpQGL2OVkN60PN7V1dgS2IHfUS3OhXMq2p07T1K74L_C99O7Y5hA4u4m2KPIKjSqsplAy7HfGOsBV-4iPfRBFAs520r0obMDi9i-l3Na3fNDvg)

#### ConvolveBorderColumn Function
![](https://lh3.googleusercontent.com/MVQ6fsWkoA2UEGA2gE344icUQMWoFZ3V0Dg-BwJSewKi5HqrxsngfeT4sWtMwMVfcfp5O0siSA9B7JzXnanMQpPdWn1QjSVDo6I25ksKaJkxp9qXgQAkYhNkmN_STu8sq61GjpetYg)

If the images are now shown, the code after optimization can be found [here][Code afer optimization]. I commented 'fixed' or 'added' to indicate what part I have changed.

### The Result of the Optimization
As one of the servers named Bbetty I used to use in both stage 1 and stage 2 is down, I used the Aarchie server for Aarch64 architecture test. The servers I used for the x86_64 architecture test are xerxes and matrix. I used optimization level 0. It is easier to observe how much it is optimized by using optimization level 0 as it took the longest time to compile the project so the time difference can be easily discovered.
![](https://lh3.googleusercontent.com/WmaWU3Sk8JKC5A6SLDsjFxX-Ym3C73Hp05vmQ-Cr2-mCvloFg2AoEksVf89nytxSrmYdC36Pibdk4erI-8mxkpakDUTQeqGsorvq8UWcK259y9P28cbGprfg41O8Px52q2pS-vbvpQ)

If the image is not shown, the link for the test result is [here][Test result].

This optimization makes a huge impact on compiling under optimization level 0. It reduces the time to compile 71% under xerxes server, 63.5% under matrix, and 55% under Aarchie.
The profiling before the optimization can be found [here][Before optimization] and the profiling after this optimization can find [here][After optimization]. The Convolution function is still the function that takes the longest time for this project and the percentage it took is similar. However, when I go inside the function to see what part is the longest one to take the time, it is not 'ldr' anymore.

### Reflection
I have fun to optimize this software. I usually use 'int' when I write codes. However, using 'int' instead of 'size_t' can make a big difference when the optimization level is low such as optimization level 0. This discovery is very interesting to me. I have tested the code I have optimized by using other optimization levels such as optimization level 1 or level 2 but in this case, I cannot find a reasonable difference. I guess that is because the program optimizes the project if the optimization level is high or because the time spent is already short so the time can be affected under various situations.

[Project Stage1]: https://hyunjileetech.github.io/project-stage1-compiling-and-benchmark
[Project Stage2]: https://hyunjileetech.github.io/project-stage2-profiling-butteraugli
[Project Stage2: x86-64]: https://hyunjileetech.github.io/project-stage2-profiling-x86-64
[Project Organize]: https://hyunjileetech.github.io/project-organize-results
[Google Opensource website about Butteraugli]: https://opensource.google/projects/butteraugli
[Github - Butteraugli]: https://github.com/google/butteraugli
[Aarch64 document]: https://developer.arm.com/documentation/dui0802/a/CIHGJHED
[Course-Aarch64]: https://wiki.cdot.senecacollege.ca/wiki/AArch64_Register_and_Instruction_Quick_Start
[Course-x86-64]: https://wiki.cdot.senecacollege.ca/wiki/AArch64_Register_and_Instruction_Quick_Start
[butteraugli.cc]: https://github.com/google/butteraugli/blob/master/butteraugli/butteraugli.cc
[Code afer optimization]: https://docs.google.com/document/d/1ueNCXm-0doW-htKc-3vKjwN0UA-tlD16mVwAJNU_ci0/edit
[Test result]: https://docs.google.com/spreadsheets/d/1jq5ughacyeaeGpcKEdrkz9UbqaGgVxzaRxYtT9eOfcU/edit#gid=0
[Before optimization]: https://docs.google.com/document/d/1l0WeZxfx1SDQxv4vEPuvzq3WQ3HaAqXtrInXGOHRdI4/edit
[After optimization]: https://docs.google.com/document/d/1pQbdXO4F_fHHzP-L7-5eqfz_LAtCETifCq20Ud3L7kE/edit