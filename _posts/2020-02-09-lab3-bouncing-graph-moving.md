---
layout: post
title: "Lab 3: Bouncing Graph - Drawing and Moving Graph in the Pointer Location"
date: 2020-02-09 10:39:10
author: Jane Hyunji Lee
categories: ['SPO600', '6502 Assembly']
tags: ['SPO600', 'Lab3']
permalink: /lab3-bouncing-graph-drawing-and-moving-graph
---

### Introduction
In this post, I will show how can I write a code to draw and move graphic (in this posting, it is a ball. You can change the shape by changing the subroutine ball's dcb). This is the previous step of the lab 3 (the instruction of Lab 3 is written below). You can understand more about this posting if you read my previous posting [Lab 3: Bouncing Graphic (Part 1 - Calculate Pointer)][lab3-Bouncing graphic part1] before you read it. The improvement part of this posting is related to the previous posting. 

### Lab 3 Instruction
The option my group chooses is the bouncing graphic. The instructions for this option are the following:
Create a simple graphic in a square that is 5*5 or 7*7 pixels in size. Use the colors available in the emulator's bit-mapped display. The graphic could be a ball a happy face, a logo, an emoji, or anything else that you want to use
Encode that graphic in bytes using DBC (declare constant byte) instructions
Write code to make the graphic bounce around the screen, reflecting off the edges when it hits
Make the speed keyboard-adjustable (faster/slower) and perturb the object's path once in a while

#### 1. Improvement - Calculating Pointer
##### 1.1. Flow Chart for Previous Calculating Pointer

![lab3 - Flow Chart](https://lh3.googleusercontent.com/TQsn2mk13lPSooQhpten_SH6o5b3WQCedKefQxQ_w96lvoN-OEKCBFlslLxFOQC-k6O5CN5JYlSLB4qE3aWT9V-KsmLLXUy7xMCX9euajsNY9Sq5iktDj6W_O40oq04HxlAgTxQvbQ)
By following this way, there are four cases to calculating pointer: page 1, page 2, page 3, and page 4. When the row is bigger than #$17, it means the pointer is located on page 4. When the row is bigger than #$0f and smaller or equal to #$17, it means the pointer is located on page 3. When the row is bigger than #$07 and smaller or equal to #$0f, it means the pointer is located on page 2. When the row is within #$00 and #$07 range, the pointer is located on page 1. Therefore, this flow chart calculates the pointer's position by subtracting a certain value for each case. I found this process is inefficient compared to another way I will describe below.

##### 1.2. Flow Chart for Improved Calculating Pointer 
![lab3-flow chart calculating](https://lh3.googleusercontent.com/Z5stIjEk10F7ssG6FSf1Syasj06jKdIvWI1I2JPOHRVr89MrbfeM6KXxwKTLnLI7D0T3724icui2JcJ-dpbFqpC7r2v0s2Z2RFaOMgIegIxQCk3t0xiXXOPX1vxY-Uz4rUsQPtVRmQ)
The new way is simple. Multiply #$20 to the ROW value. When there the carry flag is set because the value is overflowed, add the carry flag to the page value. By using this way, the complicated steps to check what page is the pointer located and what value should be subtracted from the row depending on what page is not necessary.

#### 2. Further Step - Moving Graphic
In this posting, it will show the code of how the graphic is moving. The graphic only moves diagonally and it does not stop when the line is the last line. The code will be improved to check the last line in the next posting.
2.1. Pseudo Code
Calculate the pointer using the row and column value
draw the graphic on the pointer position
increase 1 for both row and column to move diagonally
go back to step 1 and repeat it.

#### 3. Implementation
##### 3.1. Whole Code
```
define  POINTER         $10 
define  POINTER_H $11
define  ROW_COUNT  $13
define  ROW           $14
define  COLUMN   $15 
define PTR_CALCULATE $19

define WIDTH  5
define HEIGHT  5

  lda   #$1a           ; Set a row value 1f
  sta  ROW
  lda  #$1a  ; Set a column value 5
  sta  COLUMN

; ====== Calculation ======
; ====== Initialize for calculation ======
cal_initialize:

  lda  #$00    ; create a pointer at $10
  sta  POINTER
  lda  #$02
  sta  POINTER_H
 
 lda #$00 
 sta ROW_COUNT

 lda #$00 
 sta PTR_CALCULATE

 cmp ROW
 beq column_count
 bne row_count

;====== row count =======
row_count:
 lda PTR_CALCULATE
 clc
 adc #$20
 sta PTR_CALCULATE
 
 lda POINTER_H
 adc #$00
 sta  POINTER_H

 inc ROW_COUNT
 
 lda ROW
 cmp ROW_COUNT
 bne row_count
 beq column_count

;===== column_count =====
column_count:
  lda PTR_CALCULATE
  clc
  adc  COLUMN
 sta PTR_CALCULATE

;===== store the value to pointer (calculation done) =====
  sta  POINTER

;===== draw graphic =====
; initializing for drawing graph
 lda #$00
 sta ROW_COUNT

 ldx #$00  ; index for data
 ldy #$00  ; index for screen column

; draw graph
draw: lda ball,x

 sta (POINTER),y

 inx
 iny
 cpy #WIDTH
 bne draw

 inc ROW_COUNT

 lda #HEIGHT
 cmp ROW_COUNT
 beq move

 lda POINTER
 clc
 adc #$20
 sta POINTER
 lda POINTER_H
 adc #$00
 sta POINTER_H 

 ldy #$00
 beq draw

; ======= move the graph =======
move: inc ROW
 inc COLUMN

; ======= clear the screen before redraw the graph =====
clear: lda ball
 sta POINTER
 lda #$02
 sta POINTER_H
 
 ldy #$00
 tya

clear_loop:
 sta (POINTER),y
 iny
 bne clear_loop

 inc POINTER_H
 ldx POINTER_H
 cpx #$06
 bne clear_loop

 jsr  cal_initialize

; data constant byte
ball:
 dcb 00,03,03,03,00
 dcb 07,00,00,00,07
 dcb 07,00,00,00,07
 dcb 07,00,00,00,07
 dcb 00,03,03,03,00
```

#### 4. Next Step
The following error happens at this point:
- When the ball is counted to the last line, it does not stop and move the next line. It occurs the error because there is no place to draw the graphic anymore. I will fix it on the next posting

The following functions will be applied to the next code:
- Make the graphic bounce around the screen, reflecting off the edges when it hits
- Perturb the object's path once in a while using the keyboard

#### 5. What I have learned from this lab
I have learned the power of teamwork again through this lab. When I have done the calculating pointer part in the previous posting, I showed my code and explained how it works for my team. A few weeks later, one of the team members completed to move and bounce the graphic part. When I looked at his code, I felt the calculating part was not a big deal if I follow his code. That is a more efficient way to get the position so I followed his way to do it in this posting. There are lots of ways to get a certain result. I have learned I should not have stuck in one way.

[lab3-Bouncing graphic part1]: https://hyunjileetech.github.io/lab3-bouncing-graphic-part1-calculate-pointer