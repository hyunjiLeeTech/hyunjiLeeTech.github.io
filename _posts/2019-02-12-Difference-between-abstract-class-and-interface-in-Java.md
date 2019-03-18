---
layout: post
title:  "Difference Between Abstract class and Interface in Java"
date:   2019-02-12 20:43:59
author: Jane Hyunji Lee
categories: Java
tags:	Abstract class, Abstraction, Interface, Java
cover:  
---

### Abstract Class

- It can conclude both concrete method and abstract method
- All of the abstract methods in the abstract class have to be overridden.
- Child class do not need to override all of the abstract methods. If there are two abstract methods (method ‘A’ and method ‘B’), it can be possible one child class named ‘childA’ override method ‘A’ and the other child class named ‘childB’ override method ‘B’. Eventually, all of the abstract method should be overridden.

### Interface

- It is good that one interface has one method.
- If you want to make two method in an interface, you can include interface in the interface. However, it can make the code complex.
- Interface contains only constants and abstract methods.
- Abstract method is public and static by default.
- Fields are public, static, and final by default.
- All members are public by default.
