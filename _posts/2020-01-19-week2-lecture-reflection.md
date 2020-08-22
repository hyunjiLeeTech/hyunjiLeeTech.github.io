---
layout: post
title: "Week 2: Lecture Reflection"
date: 2020-01-19 10:39:10
author: Jane Hyunji Lee
categories: ['SPO600', '6502 Assembly']
tags: ['SPO600']
permalink: /week2-lecture-reflection
---
### What I have learnt this week
#### 1. How the bitmapped display consists

When I look at the notes in a 6502 assembler in a website, 
Pages $02 to $05 is bitmapped dispaly. The first page is $02 and the last one is $05. Each page has 0 - 255 (00 - ff) pixels. The top left is #$0200 and the top right is #$021f. The bottom left is #$05e0 and bottom right is #$05ff. When I store the pages, I should store the page location in a lower address and the page number in a higher address. For example, If you store  #$00 which is page location in $10 address, you should store page number #$02 in $11 address.


#### 2. Status Register
NV-BDIZC
##### (1) N: Negative
When you set the value type unsigned value, from 00 to 7f is positive value and 80 to ff is negative value.

##### (2) V: Overflow
It can be used when you set the value type unsigned. When the value becomes positive value to negative value or negative value to positive value, it will be flagged.

Negative Value to Positive value. Overflow flag is set


Positive value to negative value. Overflow flag is set

##### (3) C: Carry: Carry Flag
Carry flag can be set by using SEC instruction and can be cleared by using CLC (Clear Carry) instruction. One of the representative situations it can be set when overflow is occurred.


### 3. Instructions
#### 3.1. SEC: Set carry
Set carry flag.

#### 3.2. CLC: Clear Carry Flag
Clear Carry Flag

#### 3.3. ADC: Add with carry 
If carry flag is set, ADC instruction adds 1 more. Therefore, usually you should use CLC (clear carry flag) before you use ADC except when you want to add 1 bit more.

Example 1)
code:
```
        lda        #$03
        sec
        adc        #$05
```
result: The result is #$09 because the carry flag is set.

Example 2)
code:
```
        lda        #$03
        sec
        clc
        adc        #$05
```
result: The result is #$08 because the carry flag is cleared. Also, the value of accumulator is added 1 more if carry flag is set


#### 3.4. SBC: Subtract with carry
When you use subtraction, you should set carry flag in case of positive value becomes negative value or negative value becomes positive value.

Example 1)
code:
```
        lda        #$20
        sec
        sbc        #$10
```
result: The value in accumulator will be #$10. 

Example 2)
code:
```
        lda        #$20
        sec
        sbc        #$30
```
result: The value in accumulator will be #$f0.

Example 3)
code:
```
        lda        #$20
        sbc        #$30
```
result: The value in accumulator will be #$ef. As the original value is less than the value you want to subtract, the result will be 1 less value if you do not set carry flag.

#### 3.5. CMP: Compare
There are three cases for this instruction. The flag that will be set will be different in each case so you can you the state of the flag for this instruction.

##### 1) A > B
When the value you want to be compared which is the value in accumulator (A) is greater than the value you want to compare (B), the carry flag will be set.
Example)
code:
```
        lda        #$20
        sta        $10
        lda        #$40
        cmp      $10
```
result:
original status:
NV-BDIZC
00110000

After run the code:
NV-BDIZC
00110001

##### 2) A < B
When the value you want to be compared which is the value in accumulator (A) is less than the value you want to compare (B), the negative flag will be set.
Example)
code:
```
        lda        #$20
        sta        $10
        lda        #$10
        cmp      $10
```
result:
original status:
NV-BDIZC
00110000

After run the code:
NV-BDIZC
10110000

##### 3) A == B
When the value you want to be compared which is the value in accumulator (A) is greater than the value you want to compare (B), the carry flag and Zero flag will be set.
Example)
code:
```
        lda        #$20
        sta        $10
        lda        $20
        cmp      $10
```
result:
original status:
NV-BDIZC
00110000

After run the code:
NV-BDIZC
00110011


### Reflection
In this week, I learnt the basic knowledge of 6502 Assemble Language. At the beginning, it was quite confusing because it is my first time to learn low-level language. However, once I tried to complete Lab 2 'Write a Code' part, I felt this course can be a good challenge to me. I am excited what will I learn from this lecture.