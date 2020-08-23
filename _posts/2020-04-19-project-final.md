---
layout: post
title: "Project: Final"
date: 2020-04-19 10:39:10
author: Jane Hyunji Lee
categories: ['SPO600', 'Butteraugli Optimization']
tags: ['SPO600', 'Butteraugli']
permalink: /project-final
---
### Introduction
In this post, I will describe the summary of how I have done each stage. The purpose of this post is for wrapping up this project.
The purpose of the project is the optimization of one open-source project. I need to compile, benchmark, profiling, and optimize the software. The detailed information about the project is described in the [SPO600 winter project][Winter 2020 SPO600 Project]. 


### Previous posts related to this project
The previous posts for this project are listed below. You can read the detailed steps of how I have done each stage.

#### Stage 1
[Project Stage 1 - Compiling and Benchmark Butteraugli][Project Stage1]

#### Stage 2
[Project Stage 2 - Profiling Butteraugli][Project Stage2]
[Project Stage 2: Profiling (x86_64)][Project Stage2: x86-64]

#### The links for the results
[Project: Organize the Results][Project Organize]

#### Stage 3
[Project Stage 3: Optimization][Project Stage3]


### Stage 1
Stage 1 is for building and benchmarking the performance of an open-source software package. There are some requirements for the open-source package I need to consider. Firstly, the time for testing has to be more than 4 minutes. Also, it should be CPU intensive.
I had some struggles to find a proper open-source project to fit those requirements. The open-source package I finally found was [butteraugli][Google Opensource website about Butteraugli] Google created. Google said the Butteraugli is used by teams working on projects requiring delivery of high-quality images to be balanced with bandwidth use. Also, it is used to test the efficacy of visual compression algorithms. As I have to create a test case that takes more than four minutes, I brought 5mb images and 10mb images from the [image sample website][Sample Image website]. When I did benchmarking with 10mb images under the optimization level 0, the time took more than 4 minutes under both Aarch64 and x86_64 architectures. I chose 'Aarchie' and 'Bbetty' servers for Aarch64 architecture testing and 'Xerxes' and 'Matrix' servers for x86_64 architecture testing. I tested other optimization levels such as O1, O2, O3, Og, Ofast, and Os. I found that the time became less than 4 minutes when the optimization level was not O0. The Aarchie is the only exception for this case. I decided to focus on the optimization level 0 to do optimization this open-source package. All the benchmark results can be found in [Project Stage 1: Benchmarking Google Spreadsheet][Project stage1 spreadsheet].


### Stage 2
Stage 2 is for profiling the software to determine which part of the code is doing most of the work. I used [gprof][Gprof] and [perf][Perf] to do profiling. I found that the function named 'Convolution' took the most time to compile this project. Both gprof and perf indicated this function took around 42% of the overall time. All the profiling results can be found in [Project Stage 2: Profiling Google docs][Project stage2 google docs].


### Stage 3
Stage 3 is for identifying optimization opportunities in the software.
One of the advantages of using 'perf' to do profiling is it indicates not only the percentages of each function to execute but what command takes the longest time to execute the function. The profiling results I had done in stage 2 indicated 'ldr' which is for loading the register from the address pointed took the longest time in 'Convolution' function. It meant that if I use better data type to load the register, I can optimize this open-source package. Therefore, I changed the data type 'int' to 'size_t' and made other variables to store the values which called multiple times to reduce the loading time. The code I have changed to optimize can be found in [Project Stage3: Optimization - Code Google docs][Project stage3 optimization code]. After I have changed the code, the time spent on project reduced more than 55% under both Aarch64 and x86_64 architectures. The test results can be found in [Project Stage 3: Optimization Google Spreadsheet][Project stage3 optimization result]. 


### Reflection
All the stages I have done for this project are very interesting and I have lots of fun to do this. Once I chose the project I want to work on, I had an interest in this project because I found an article that 'Butteraugli' is used in another open-source project named Guetzli and 'Butteraugli' makes 'Guetazli' slower. It means that if I can optimize 'Butteraugli', it can lead to optimizing 'Guetzli' as well. Furthermore, as the time difference before optimization and after optimization was huge, I felt proud I created this result and felt I want to do optimization using other projects. 

[Project Stage1]: https://hyunjileetech.github.io/project-stage1-compiling-and-benchmark
[Project Stage2]: https://hyunjileetech.github.io/project-stage2-profiling-butteraugli
[Project Stage2: x86-64]: https://hyunjileetech.github.io/project-stage2-profiling-x86-64
[Project Organize]: https://hyunjileetech.github.io/project-organize-results
[Project Stage3]: https://hyunjileetech.github.io/project-stage3-optimization
[Google Opensource website about Butteraugli]: https://opensource.google/projects/butteraugli
[Github - Butteraugli]: https://github.com/google/butteraugli
[Gprof]: https://en.wikipedia.org/wiki/Gprof
[Perf]: https://perf.wiki.kernel.org/index.php/Tutorial
[Winter 2020 SPO600 Project]: https://wiki.cdot.senecacollege.ca/wiki/Winter_2020_SPO600_Project
[Sample Image website]: https://sample-videos.com/download-sample-png-image.php
[Project stage1 spreadsheet]: https://docs.google.com/spreadsheets/d/16v0DXJpgWMkkCQ9VTcacY08Vy9h3TYHel2-Zj2AQyhY/edit#gid=0
[Project stage2 google docs]: https://docs.google.com/document/d/1l0WeZxfx1SDQxv4vEPuvzq3WQ3HaAqXtrInXGOHRdI4/edit
[Project stage3 optimization code]: https://docs.google.com/document/d/1ueNCXm-0doW-htKc-3vKjwN0UA-tlD16mVwAJNU_ci0/edit
[Project stage3 optimization result]: https://docs.google.com/spreadsheets/d/1jq5ughacyeaeGpcKEdrkz9UbqaGgVxzaRxYtT9eOfcU/edit#gid=0