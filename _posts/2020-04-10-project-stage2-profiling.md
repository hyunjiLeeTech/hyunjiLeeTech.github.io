---
layout: post
title: "Project Stage 2: Profiling (x86-64)"
date: 2020-04-10 10:39:10
author: Jane Hyunji Lee
categories: ['SPO600', 'Butteraugli Optimization']
tags: ['SPO600', 'Butteraugli']
permalink: /project-stage2-profiling-x86-64
---
### Introduction
As the profiling I have done on the [previous blog][Project Stage2] was using Aarch64 architecture, I will do profiling in x86_64 architecture and describe the result. I recommend to take a look at the previous blogs related to this project: [Project Stage 1 - Compining and Benchmarking Butteraugli][Project Stage1] and [Project Stage 2 - Profiling Butteraugli][Project Stage2]

### The Project I Selected: Butteraugli
The Butteraugli is the project Google created. It estimates the psychovisual similarity of two images. The detailed information about this project is described on the [Google Opensource website about the Butteraugli][Google Opensource website about Butteraugli]. The source code for this project is located here: [GitHub - Butteraugli][Github - Butteraugli].

### How to use gprof and perf
The detailed process of how to use gprof and perf for Butteraugli profiling is described in the [Project Stage 2 - Profiling Butteraugli][Project Stage2]. 

### Result of the prof in x86_64 architecture
#### Image size: 5mb
![](https://lh3.googleusercontent.com/ZwV2s-SOMozsSyX73Z5pwW1VheOuKsYBKwoCGqgYVShWs7fk5Vx4YMQSbomGQHvjPGYCcKjBRMW-LoZWMODUj_gwLVP_VfdYuhlM4uyoz4lGF1WNQS3CEJHG42jB1CFxpIMlQnDLJg)

#### Image size: 10mb
![](https://lh3.googleusercontent.com/fU5mdP0wePky84AQgylH6fmq8OB9s4wrUS23nHFlbXPqOJYbQTShayFW0FDR6SB4AjTUVC9Gzt9hKZg1ape1XHTNDmGPrGKqoWBSI_RfqlOvjcHIu7QNDYSbEZdvijyDte8O5KQZOw)

### Result of the perf in x86_64 architecture
#### Image size: 5mb
![](https://lh3.googleusercontent.com/W_Zry9jzBos5ou5tmrUgYO29LB874qLQ6OVSEkUQyO1eJsxU5isWJoNrkEW_T_lT7sPSyQXZ0aWsPVeDtqfHvwRg7_mvDOioZ_XjMVeiN6_LMNM9przd_zYtIsJ8ERUr2tp4-dbinQ)

#### Image size: 10mb
![](https://photos.app.goo.gl/GHptoTB1TDpEsb247)

### Conclusion
I have checked the major functions takes the most time in this project are **butteraugli::Convolution(butteraugli::Image<float> const&, std::vector<float>**, **std::vector<float, std::allocator<float>> const&, float)** and **std::vector<float, std::allocator<float> >::operator[](unsigned long)** in both the Aarch64 and x86_64 architectures. Therefore, If I optimize those functions, this project can be faster in both Aarch64 and x86_64 architecture.

### Reflection
As I have already done profiling in Aarch64 architecture, profiling in x86_64 architecture is not difficult. I have fun profiling this project. I will focus on two functions take the most time for this project, optimize them, and blog the result on the stage 3 post.


[Google Opensource website about Butteraugli]: https://opensource.google/projects/butteraugli
[Github - Butteraugli]: https://github.com/google/butteraugli
[Project Stage1]: https://hyunjileetech.github.io/project-stage1-compiling-and-benchmark
[Project Stage2]: https://hyunjileetech.github.io/project-stage2-profiling-butteraugli
