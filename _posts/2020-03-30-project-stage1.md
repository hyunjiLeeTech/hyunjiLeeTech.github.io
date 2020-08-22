---
layout: post
title: "Project Stage 1 - Compiling and Benchmark Butteraugli"
date: 2020-03-30 10:39:10
author: Jane Hyunji Lee
categories: ['SPO600', 'Butteraugli Optimization']
tags: ['SPO600']
permalink: /project-stage1-compiling-and-benchmark
---
### Introduction
This post is about the project I am working on the SPO600 class. I will benchmark one open-source, profile it, and optimize the project. The requirements for this project are the following:
* The running time has to be more than 4 minutes
* The open-source software package has to include a CPU-intensive function or method
* The open source software package has to implement in a language that compiles to machine code (such as C, C++, or Assembler)
The detailed project instruction can be found on the [Winter 2020 SPO600 Project][Winter 2020 SPO600 Project], I had the struggle to find a proper open-source with the requirement. I narrowed down the type of software I want to work on and found the open-source that is proper for this project. I will describe what is a project I selected, the ways I build and compile the open-source, how to benchmark the project.


### The Project I Selected: Butteraugli
The Butteraugli is the project Google created. It estimates the psychovisual similarity of two images. The detailed information about this project is written on the [Google Opensource website about the Butteraugli][Google Opensource website about Butteraugli]. The source code for this project is located here: [GitHub - Butteraugli][Github - Butteraugli].

### Build and Compile the Open Source Package
##### 1. Clone the repository into my machine. 
The command is the following:
```
      git clone https://github.com/google/butteraugli.git
```


##### 2. Compile the software
Move to the repository which contains the 'Makefile' and enter 'make' to compile the project. I found the 'Makefile' into the 'butteraugli' repository so I moved to the repository and enter the command.
```
      cd butteraugli
      make
```

##### 3. Store the images for this project
The purpose of this project is comparing two images. Before I compared two images, I copied two similar-size images from my Seneca matrix account. The website I got the sample images is [this][Sample Image website]. To get the time for more than 4 minutes, I used the 5mb and 10mb images.
```
      scp mysAccount@matrix.senecacollege.ca:~/the/path/image/stored/test10mb1.png .
      scp mysAccount@matrix.senecacollege.ca:~/the/path/image/stored/test10mb2.png .
```

##### 4. Run the software
After the images are prepared, run the tool to compare two images. As I need to get how much time it takes to run the tool, I put 'time' in front of the running command.
      time ./butteraugli test5mb.png sample5mb.png
It shows the real, user, and sys time to run the tool.

### Apply Various Optimization Level
There 7 different optimization levels.
* -O0: disables most (but not all) optimizations
* -O1: enables basic optimizations that can be performed quickly
* -O2: enables almost all safe optimizations
* -O3: enables aggressive optimization including optimizations which may not always be safe for all code
* -Os: optimizes for small binary and runtime size, possibly at the expense of speed
* -Ofast: optimizes for fast execution, possibly at the expense of size
* -Og: optimizes for debugging. It applies optimizations that can be performed quickly and avoids optimizations which convoluted the code

The information above is from [Winter 2020 SPO600 Weekly Week4][Winter2020 SPO600 Weekly Week4]. You can get more detailed information on this website.

To apply different optimization levels, I need to modify the 'Makefile'.
##### 1. Open the 'Makefile' to modify the file.
```
    vi Makefile
```

##### 2. Modify the file to apply optimization level O2
The way to check the original content when you clone the repository is the following.
```
    cd butterguali
    make
```


You should put the optimization level in the 'CSSFLAGS' part. For example, if you want to apply -O2, you should enter
```
    CXXFLAGS += -std=c++11 -l.. **-O2**
```

##### 3. Recompile the software
If you have not compiled yet, enter 'make' let you compile the software. However, if you already have compiled, it does not let you clear (command: make clean)or recompiled (command: 'make' or 'make all') the software even though you enter 'make clean'. 

In this case, there is one way you can recompile this project. After you remove the generated files you got when you compiled the software, it allows you to recompile the software. There are two generated files named 'butteraugli_main.o' and 'butteraugli.o'. The command to remove those files and compile the software are the following:
```
   rm butteraugli_main.o butteraugli.o
   make
```

Now the optimization level is successfully applied. You can repeat these steps when you want to change the optimization level.

### Benchmark the Performance of the Current Implementation
For the benchmark the performance, I used 'Aarchie' and 'Betty' servers for the 'Aarch64' system benchmark. I used 'xerxes' and 'matrix' servers for the 'x86_64' system benchmark. I applied -O0, -O1, -O2, -O3, -Os, -Ofast, -Og optimization levels for the benchmark.

##### 1. -O0 Benchmark
1) Image size: 10mb
|  | Real | User | Sys |
| --- | ----------- |---|---|
| x86-64 |  | |  |
| Xerxes | 4m55.886s | 4m54.960s | 0m0.507s |
| Matrix | 5m31.791s | 5m31.031s | 0m0.621s |
| Aarch64 |  |  |  |
| Aarchie | 38m5.503s | 37m55.260s | 0m4.081s |
| Bbetty | 11m23.640s | 11m21.539s | 0m1.368s |

2) Image size: 5mb
|  | Real | User | Sys |
| --- | ----------- |---|---|
| x86-64 |  |  |  |
| Xerxes | 0m58.467s | 0m58.231s | 0m0.158s |
| Matrix | 1m4.663s | 1m4.380s | 0m0.196s |
| Aarch64 |  |  |  |
| Aarchie | 7m30.448s | 7.28.171s | 9m1.146s |
| Bbetty | 2m15.280s | 2m14.616s | 0m0.507s |

##### 2. -O1 Benchmark
1) Image size: 10mb
|  | Real | User | Sys |
| --- | ----------- |---|---|
| x86-64 | | | |
| Xerxes | 0m14.044s |	0m13.509s |	0m0.502s |
| Matrix | 0m24.166s |	0m23.569s |	0m0.569s |
| Aarch64 |  |  |  |		
| Aarchie | 4m33.219s |	4m28.738s |	0m3.302s |
| Bbetty | 0m58.511s |	0m57.020s |	0m1.377s |


2) Image size: 5mb
|  | Real | User | Sys |
| --- | ----------- |---|---|
| x86-64 | | | |
| Xerxes | 0m2.911s |	0m2.749s | 0m0.155s |
| Matrix | 0m4.998s |	0m4.788s |	0m0.200s |
| Aarch64 |  |  |  |		
| Aarchie | 0m54.436s |	0m53.015s |	0m1.184s |
| Bbetty | 0m11.655s |	0m11.270s |	0m0.359s |



##### 3. -O2 Benchmark
1) Image size: 10mb
|  | Real | User | Sys |
| --- | ----------- |---|---|
| x86-64 | | | |
|Xerxes | 0m12.758s |	0m12.233s |	0m0.494s |
|Matrix | 0m21.189s |	0m20.559s |	0m0.579s |
| Aarch64 |  |  |  |		
| Aarchie | 2m49.022s |	2m44.905s |	0m3.322s |
| Bbetty | 0m53.380s |	0m51.954s |	0m1.317s |


2) Image size: 5mb
|  | Real | User | Sys |
| --- | ----------- |---|---|
| x86-64 | | | |		
| Xerxes | 0m2.986s |	0m2.816s |	0m0.164s |
| Matrix | 0m4.415s |	0m5.207s |	0m0.191s |
| Aarch64 |  |  |  |		
| Aarchie | 0m33.822s |	0m32.649s |	0m1.015s |
| Bbetty | 0m10.598s |	0m10.135s |	0m0.439s |

##### 4. -O3 Benchmark
1) Image size: 10mb
|  | Real | User | Sys |
| --- | ----------- |---|---|
| x86-64 | | | |
| Xerxes | 0m12.758s |	0m12.233s |	0m0.494s |
| Matrix | 0m4.343s |	0m4.132s |	0m0.196s |
| Aarch64 |  |  |  |		
| Aarchie | 3m4.395s |	2m59.721s |	0m3.840s |
| Bbetty | 0m49.620s |	0m48.111s |	0m1.406s |


2) Image size: 5mb
|  | Real | User | Sys |
| --- | ----------- |---|---|
| x86-64 | | | |
| Xerxes | 0m2.642s |	0m2.474s |	0m0.162s |
| Matrix | 0m4.343s |	0m4.132s |	0m0.196s |
| Aarch64 |  |  |  |		
| Aarchie | 0m36.754s |	0m35.430s |	0m1.143s |
| Bbetty | 0m9.917s |	0m9.466s |	0m0.429s |

##### 5. -Os Benchmark
1) Image size: 10mb	
|  | Real | User | Sys |
| --- | ----------- |---|---|
| x86-64 | | | |
| Xerxes | 0m36.138s |	0m35.592s |	0m0.477s |
| Matrix | 0m42.977s |	0m42.362s |	0m0.557s |
| Aarch64 |  |  |  |		
| Aarchie | 5m30.822s |	5m25.934s |	0m3.571s |
| Bbetty | 1m32.866s |	1m31.332s |	0m1.367s |


2) Image size: 5mb
|  | Real | User | Sys |
| --- | ----------- |---|---|
| x86-64 | | | |		
| Xerxes | 0m7.314s |	0m7.144s |	0m0.157s |
| Matrix | 0m8.304s |	0m8.066s |	0m0.204s |
| Aarch64 |  |  |  |		
| Aarchie | 1m4.491s |	1m3.186s |	0m1.035s |
| Bbetty | 0m18.369s |	0m17.976s |	0m0.360s |

##### 6. -Ofast Benchmark
1) Image size: 10mb
|  | Real | User | Sys |
| --- | ----------- |---|---|
| x86-64 | | | |
| Xerxes | 0m9.657s |	0m9.129s |	0m0.504s |
| Matrix | 0m20.425s |	0m19.816s |	0m0.588s |
| Aarch64 |  |  |  |		
| Aarchie | 2m10.033s |	2m5.358s |	0m3.997s |
| Bbetty | 0m36.789s |	0m35.360s |	0m1.327s |


2) Image size: 5mb
|  | Real | User | Sys |
| --- | ----------- |---|---|	
| x86-64 | | | |	
| Xerxes | 0m2.146s |	0m1.974s |	0m0.142s |
| Matrix | 0m3.860s |	0m3.659s |	0m0.189s |
| Aarch64 |  |  |  |		
| Aarchie | 0m25.823s |	0m24.599s |	0m1.095s |
| Bbetty | 0m7.170s |	0m6.722s |	0m0.430s |



##### 7. -Og Benchmark
1) Image size: 10mb
|  | Real | User | Sys |
| --- | ----------- |---|---|
| x86-64 | | | |		
| Xerxes | 0m17.598s | 0m17.060s |	0m0.498s |
| Matrix  | 0m28.235s |	0m27.669s |	0m0.546s |
| Aarch64 |  |  |  |		
| Aarchie | 6m1.176s |	5m55.262s |	0m4.496s |
| Bbetty | 1m6.673s |	1m5.081s |	0m1.467s |

2) Image size: 5mb
|  | Real | User | Sys |	
| --- | ----------- |---|---|	
| x86-64 | | | |
| Xerxes | 0m3.639s | 0m3.482s | 0m3.150s |
| Matrix | 0m5.795s	| 0m5.583s | 0m0.195s |
| Aarch64 |  |  |  |		
| Aarchie | 1m11.800s |	1m10.439s |	0m1.095s |
| Bbetty | 0m13.213s |	0m12.708s |	0m0.479s |

### Reflection
The most interest part I have learned during stage 1 is the performance depending on the optimization level. The performance under -O0 and is 9 times slower than the performance under -O1 optimization level. It means if I apply -O1 manually on the source code, I can reduce a lot of time using -O. In the next posts, I will profile the software to find what functions take a long time to perform and optimize them.

[Winter 2020 SPO600 Project]: https://wiki.cdot.senecacollege.ca/wiki/Winter_2020_SPO600_Project
[Google Opensource website about Butteraugli]: https://opensource.google/projects/butteraugli
[Github - Butteraugli]: https://github.com/google/butteraugli
[Sample Image website]: https://sample-videos.com/download-sample-png-image.php
[Winter2020 SPO600 Weekly Week4]: https://wiki.cdot.senecacollege.ca/wiki/Winter_2020_SPO600_Weekly_Schedule#Week_4_-_Class_II