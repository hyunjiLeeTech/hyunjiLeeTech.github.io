---
layout: post
title: "Lab 3: Bouncing Graphic (Part1 - Calculate Pointer)"
date: 2020-01-30 10:39:10
author: Jane Hyunji Lee
categories: ['SPO600', '6502 Assembly']
tags: ['SPO600', 'Lab3']
permalink: /lab3-bouncing-graphic-part1-calculate-pointer
---
### Lab 3 Instruction
The option my group choose is bouncing graphic. The instructions of this option are the following:
- Create a simple graphic in a square that is 5*5 or 7*7 pixels in size. Use the colors available in the emulator's bit-mapped display. The graphic could be a ball a happy face, a logo, an emoji, or anything else that you want to use
- Encode that graphic in bytes using DBC (declare constant byte) instructions
- Write code to make the graphic bounce around the screen, reflecting off the edges when it hits
- Make the speed keyboard-adjustable (faster/slower) and perturb the object's path once in a while
This posting focuses on the explanation of the subroutine for calculating pointer.

#### 1.1. Brief Flow Chart: Whole Project

This is the first flow chart I created for this lab.It can be changed or improved in the future posting.

![Lab3 Flow Chart v1](https://lh3.googleusercontent.com/a-KdWse91rdtDW_gsnoKLmHblwWBb1cb5gRn4I7jfpLgFiUbBjWD4HvV-xE14b4_sG3TtVhFpciiFl-AUxLbBVve7ioKmIo31--c1U_CdHdF1TbfOYZuFewHiKWBJqXd_Q7qPj9FuQ)

#### 1.2. Flow Chart: Calculating Pointer

![Lab3 Flow Chart Calculating Pointer](https://lh3.googleusercontent.com/VuoLNIso-a88CXipQpzvDHbTlpiv0Vs2dTmOCHfuYVSQDhdJ6BDsxdbULynmPd44_tPaFFGWZMFVwQIn8yfsjDPYVX4dbLF4gt7HorStyy0QHhquaSGmHxvED9sw3SAPzCgk0GWCBg)


#### 2.2. Define Descriptive Constants
The define descriptive constants for this lab 3 part 1 are the following:
- define POINTER          $10
- define POINTER_H         $11
- define ROW_COUNT $13
- define ROW          $14
- define COLUMN  $15
- define ROW_STORE $16

# 2.3. Calculating Pointer Code
```
define POINTER          $10
define POINTER_H         $11
define ROW          $14
define COLUMN  $15
define ROW_COUNT $13
define ROW_STORE $16

 lda #$00  ; create a pointer at $10
 sta POINTER
 lda #$02
 sta POINTER_H

 lda  #$1f          ; Set a row value 1f
 sta ROW
 lda #$00  ; Set a column value 5
 sta COLUMN

cal_ptr:lda  ROW
 sta ROW_STORE ; Store the ROW value into ROW_STORE vlaue
 cmp #$18   ; compare ROW value with #$18. If it is greater or equal to #$18, it means it is on the 4th page

 lda POINTER_H
 adc #$00
 sta POINTER_H ; If the ROW value is greater or equal to #$18, add 1 to POINTER_H

 lda ROW  ; compare ROW value with #$10
 cmp #$10  ; If ROW value is greater or equal to #$10, it means it is on the 4th or 3rd page

 lda POINTER_H
 adc #$00
 sta POINTER_H ; If the ROW value is greater or equal to #$10, add 1 to POINTER_H

 lda ROW  ; compare ROW value with #$08
 cmp #$08

 lda POINTER_H
 adc #$00
 sta POINTER_H ;If ROW value is greater or equal to #$08, add 1 to POINTER_H

 lda POINTER_H

 cmp #$03  ; compare POINTER_H value with #$03. If it is equal, it means it is on the 2nd page
 beq page2  ; If POINTER_H is #$03, go to page2 subroutine

 cmp #$04  ; compare POINTER_H value with #$04. If it is equal, it means it is on the 3rd page
 beq page3  ; If POINTER_H is #$04, go to page3 subroutine
 
 cmp #$05  ; compare POINTER_H value with #$05. If it is equal, it means it is on the 4th page
 beq page4  ; If POINTER_H is #$05, go to page4 subroutine

 bne count_initialize

;===== page 2 =======
; Subtract #$08 to ROW value to count the location in the second page
page2: lda ROW
 sec
 sbc #$08
 cmp ROW
 beq count_initialize

;===== page 3 =======
; Subtract #$10 to ROW value to count the location in the third page
page3: lda ROW
 sec
 sbc #$10
 cmp ROW
 beq count_initialize

;===== page 4 =======
; Subtract #$18 to ROW value to count the location in the fourth page
page4: lda ROW
 sec
 sbc #$18
 cmp ROW
 beq count_initialize


;===== count_initialize======
; initialize ROW_COUNT for row counting
count_initialize:
 lda #$00
 sta ROW_COUNT
 pha

 cmp #$00
 beq row_count


;===== row_count ======
; Add #$20 until ROW value and ROW_COUNT value is equal
row_count:    
 lda ROW
 cmp ROW_COUNT
 beq column_count

 pla
 clc
 adc #$20
 pha
 inc ROW_COUNT
 lda ROW
 cmp ROW_COUNT
 bne row_count

;===== column_count =====
column_count:
 pla  
 clc
 adc COLUMN

;===== store the value to pointer (calculation done) =====
 sta POINTER

;===== return the original ROW value from ROW_STORE
 lda ROW_STORE
 sta ROW

;===== draw a dot on the pointer location ======
 lda #$07

 ldy #$00

 sta (POINTER), y
```

### 3. What I have learned from this lab
In this lab, I have learned how I can calculate the pointer using the row and column. In the beginning, I tried to get the pointer position using page number and page position, not row and column. When I tried to do that, I was stuck at some points how can I distinguish the graphic counted the last line and how the graphic moves diagonally. However, when I have learned I make a row and column variable to do this lab, I felt it makes this lab easier than when I thought I should use the page number and page position. I and my team still feel struggle to do this lab. On the other hand, my team got some ideas about how to do this lab.