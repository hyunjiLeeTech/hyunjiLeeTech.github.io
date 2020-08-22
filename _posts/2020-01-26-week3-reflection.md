---
layout: post
title: "Week 3: Reflection"
date: 2020-01-26 10:39:10
author: Jane Hyunji Lee
categories: ['SPO600', '6502 Assembly']
tags: ['SPO600']
permalink: /week3-reflection
---
### What I have learned this week
#### 1. Learning the example code: Place a Graphic on the screen

 1-1. Code Learning
 ```
     define WIDTH     4     ; width of graph
     define HEIGHT   4     ; height of graph

               lda          #$25          ; create a pointer at $10
               sta          $10            ;   which points to where
               lda          #$02          ;   the graphic should be drawn
               sta          $11            ;    the starting point: 0225 (first page, location 25)

               lda          #$00          ; number of rows we've drawn
               sta          $12

               ldx          #$00          ; index for data
               ldy          #$00          ; index for screen column  

     draw: lda          data, x
               sta          ($10), y
               inx                            ; increment index x
               iny                            ; increment index y
               cpy         #WIDTH    ; compare index y with WIDTH
               bne         draw          ; if index y is not equal to WIDTH, go back to the first line of draw

               inc          $12            ; increment row counter

               lda         #HEIGHT   ; load HEIGHT in accumulator
               cmp       $12             ; compare $12 value to accumulator value (HEIGHT vs row counter)             

               beq        done         ; when HEIGHT and row counter is equal which means all data is drawn, go to the subroutine done

               lda          $10            ; load pointer
               clc                            ; clear carry flag for adc instruction
               adc         #$20          ; add 32 to drop one row
               sta          $10            ; store $10 value to accumulator
               lda          $11            ; load $11 value to accumulator
               adc         #$00          ; in this case, it does not use 'clc' instruction before use 'adc' instruction because the purpose of this line is checking whether overflow is occurred or not
               sta          $11            ; store the value on accumulator on $11

               ldy          #$00          ; set the value 0 on the index y
               beq         draw          ; if the value of index y is equal to 0, execute subroutine draw

   done:   brk                            ; stop when it is done

   data:
   dcb 00,03,03,00
   dcb 07,00,00,07
   dcb 07,00,00,07
   dcb 00,03,03,00
```
 1-2. If you want to change the size of graph from 4*4 to 5*5, you have to change the following:
'define WIDTH 4' to 'define WIDTH 5'
'define HEIGHT 4' to 'define HEIGHT 5'
Increase data set
1-3. Generally, if you want to use the instruction 'ADC (Add with carry)', you should use 'CLC (Clear Carry)' before you use ADC. However, sometimes there are some situations you need to use Carry flag. This example code is one situation you need to apply carry flag.

   When the overflow happens, the page number have to be increased.

#### 2. Instructions
2-1. Difference between ASL and ROL
ASL (Arithmetic shift left)

If overflow occurs, carry flag will set up. The last bit will always be 0.

ROL (Rotate Left)

If overflow occurs, carry flag will set up. The last bit will be 0 or 1 depend on whether carry flag was set. 
* Set carry flag > Set up the last bit > Check the overflow > Set up carry flag


2-2. Difference between LSR and ROR
LSR (Logically shift right)

If overflow occurs, carry flag will set up. The first bit will always be 0.

ROR (Rotate Right)

If overflow occurs, carry flag will set up. The first bill will be 0 or 1 depend on whether carry flag was set.
* Set carry flag > Set up the first bit > Check the overflow > Set up carry flag

#### 3. Reflection
This week, I have learned more instructions and code examples. I felt I should take more time for learning Assembly language. Learning low-level language becomes more challenging but I will get over this.
My team picked the option 2: bouncing ball for lab 3. I have successfully done to vertically move the ball but it stops when it executed 16 times. I will draw the logic of my code and try it again.