---
layout: post
title: "Why sometimes thread is slower than sequential method in Java"
date: 2019-03-26 23:55:10
author: Jane Hyunji Lee
categories: Java
tags: Java, Sequential method, Thread, Parallel Method 
---

There is a sizable amount of overhead when establishing threads. That said, if your sample data set is too small, the amount of time spend spinning up and tearing down the threads will be greater than the actual running time performance of your code.

Java thread creation is expensive because there is a fair bit of work involved.
- A large block of memory has to be allocated and initialized for the thread stack.
- System calls need to be made to create / register the native thread with the host OS.
- Descriptors needs to be created, initialized and added to JVM internal data stuctures.

Above information is from below links.  
[First Paragraph][Overhead]  
[Second Paragraph][ThreadExpensive]

[Overhead]:         https://stackoverflow.com/questions/20696213/addition-of-integer-2d-array-elements-using-multi-threading-in-java-slower-than  
[ThreadExpensive]:  https://stackoverflow.com/questions/5483047/why-is-creating-a-thread-said-to-be-expensive