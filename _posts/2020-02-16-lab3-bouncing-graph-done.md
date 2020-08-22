---
layout: post
title: "Lab 3: Bouncing Graph - Done"
date: 2020-02-16 10:39:10
author: Jane Hyunji Lee
categories: ['SPO600', '6502 Assembly']
tags: ['SPO600', 'Lab3']
permalink: /lab3-bouncing-graph-done
---
### Introduction
In this post, I will show how to do the lab 3: bouncing graphic. The code can be upgraded when I discover an error but the bouncing and moving a ball and applying keyboard part have done. You can understand more this posting if you read my previous postings: [Lab 3: Bouncing Graphic (Part 1 - Calculate Pointer)][lab3-Bouncing graphic part1] and [Lab 3: Bouncing Graph - Drawing and Moving Graph in the Pointer Location][lab3-Bouncing graphic part2]. The detailed lab 3 instruction is written below.

### Lab 3 Instruction
The option my group choose is bouncing graphic. The instructions of this option are the following:
- Create a simple graphic in a square that is 5*5 or 7*7 pixels in size. Use the colors available in the emulator's bit-mapped display. The graphic could be a ball a happy face, a logo, an emoji, or anything else that you want to use
- Encode that graphic in bytes using DBC (declare constant byte) instructions
- Write code to make the graphic bounce around the screen, reflecting off the edges when it hits
- Make the speed keyboard-adjustable (faster/slower) and perturb the object's path once in a while

#### 1. Think about the Steps

##### 1.1. Pseudo Code
1) Initialize ROW and COLUMN in a certain position (in this posting, #$04 for ROW and #$04 for COLUMN)
2) Calculate the pointer
3) Draw the graphic on the bit-mapped output (in this posting, the graphic is a ball)
4) Check the key. If the key is up, down, right, or left, change the value of ROW or COLUMN
5) Check the pointer is on the last line. If it is the last line, flip the flag for ROW or COLUMN so it can bounce
6) Clear the screen the redraw the graphic
7) Go back to the step 2

##### 1.2. Flow Chart

<!-- ![lab3-flow chart v3](https://lh3.googleusercontent.com/8PZTSkZG93GKQuEfHP5EJnFe1Djnw6S_Ydog3lLpisOsk6be41C9y-HIMPEs27O-frhSZ3VOZQqWGi8X8ylULlro5H7xWuv0W3NiiNi-Wz4EO2PeHC_dzzzAVTsFDjyURwx2w2P6cw){ height="50%" width="50%" } -->
[<img alt="lab3-flow chart v3" src="https://lh3.googleusercontent.com/8PZTSkZG93GKQuEfHP5EJnFe1Djnw6S_Ydog3lLpisOsk6be41C9y-HIMPEs27O-frhSZ3VOZQqWGi8X8ylULlro5H7xWuv0W3NiiNi-Wz4EO2PeHC_dzzzAVTsFDjyURwx2w2P6cw" width="50%" height="50%">](https://lh3.googleusercontent.com/8PZTSkZG93GKQuEfHP5EJnFe1Djnw6S_Ydog3lLpisOsk6be41C9y-HIMPEs27O-frhSZ3VOZQqWGi8X8ylULlro5H7xWuv0W3NiiNi-Wz4EO2PeHC_dzzzAVTsFDjyURwx2w2P6cw)


#### 2. Implementation 
##### 2.1. Define Descriptive Constants
The define descriptive constants for the lab 3 are the following:
- define  POINTER          $10     
- define  POINTER_H $11
- define  ROW_COUNT  $13
- define  ROW           $14
- define  COLUMN   $15
- define CHECK_LINE $16
- define ROWFLIP_FLAG $17
- define COLFLIP_FLAG $18 
- define PTR_CALCULATE $19
- define DATA_INDEX $20
- define SCREENCOL_INDEX $21
- define KEY  $22

##### 2.2. Main Functions
Main functions for the lab 3 are the following:
- Calculate Pointer
- Draw the Graphic
- Key Check and Apply the Key
- Check the graphic is on the top, bottom, right, or left
- Clear the Screen

##### 2.3. Whole Code
```
define  POINTER         $10      
define  POINTER_H $11
define  ROW_COUNT  $13 
define  ROW           $14 
define  COLUMN   $15
define CHECK_LINE $16 
define ROWFLIP_FLAG $17
define COLFLIP_FLAG $18  
define PTR_CALCULATE $19
define DATA_INDEX $20
define SCREENCOL_INDEX $21
define KEY  $22

define WIDTH  5 
define HEIGHT  5

  lda   #$04           ; Set a row value 1f
  sta  ROW
  lda  #$04  ; Set a column value 5
  sta  COLUMN

 lda #$00
 sta ROWFLIP_FLAG
 sta COLFLIP_FLAG 

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
 beq getkey_initialize
  
 lda POINTER
 clc
 adc #$20 
 sta POINTER 
 lda POINTER_H 
 adc #$00
 sta POINTER_H  

 ldy #$00 
 beq draw

; ======= get key =======
getkey_initialize:
 txa     
 sta DATA_INDEX
 tya 
 sta SCREENCOL_INDEX   
getkey: lda  $ff  ; get a keystroke 

 ldx #$00 ; clear out the key buffer
 stx $ff


  cmp  #$80 ; if not a cursor key, ignore
  bmi  getkey_done
  cmp  #$84
  bpl  getkey_done 

  cmp  #$80  ; check key == up
  bne  rightKey_check

  dec  ROW  ; ... if yes, decrement ROW
  jmp  getkey_done

 rightKey_check: 
 cmp  #$81  ; check key == right
  bne  downKey_check

  inc  COLUMN   ; ... if yes, increment COL
  jmp  getkey_done

 downKey_check: 
 cmp  #$82  ; check if key == down 
  bne  leftKey_check

  inc  ROW  ; ... if yes, increment ROW
  jmp  getkey_done
  
 leftKey_check: 
 cmp  #$83  ; check if key == left
  bne  getkey_done

  dec  COLUMN   ; ... if yes, decrement COL
  clc
  bcc  getkey_done 

getkey_done:
 ldx DATA_INDEX
 ldy SCREENCOL_INDEX
 jmp check_location

; ======= Check the location ======
check_location: 
 jsr  check_top
 jsr check_bottom
 jsr check_right 
 jsr  check_left
 jsr move_pointer

check_initialize: 
 lda #$00
 sta CHECK_LINE
 rts

check_top: 
 jsr check_initialize 
 lda ROW  
 cmp #$01
 lda CHECK_LINE
 adc #$00
 cmp #$00  
 beq flip_rowFlag
 rts

check_bottom: 
 jsr check_initialize
 lda ROW
 cmp #$1b
 lda CHECK_LINE
 adc #$00
 cmp #$01
 beq flip_rowFlag 
 rts

check_left:
 jsr check_initialize
 lda COLUMN
 cmp #$01
 lda CHECK_LINE
 adc #$00
 cmp #$00
 beq flip_colFlag
 rts

check_right:
 jsr check_initialize
 lda COLUMN
 cmp #$1b
 lda CHECK_LINE
 adc #$00
 cmp #$01
 beq flip_colFlag
 rts

; ======= Flip Row Flag ====== 
flip_rowFlag:
 lda ROWFLIP_FLAG
 cmp #$00 
 beq inc_rowFlag
 bne dec_rowFlag
 rts

inc_rowFlag:
 inc ROWFLIP_FLAG
 rts

dec_rowFlag:
 dec ROWFLIP_FLAG
 rts


; ======= Flip Col Flag ======
flip_colFlag:
 lda COLFLIP_FLAG
 cmp #$00
 beq inc_colFlag
 bne dec_colFlag
 rts

inc_colFlag:
 inc COLFLIP_FLAG
 rts

dec_colFlag:
 dec COLFLIP_FLAG
 rts

; ======= move the graph ======== 
move_pointer:
 jsr row_check
 jsr col_check
 jmp clear

row_check:
 lda ROWFLIP_FLAG
 cmp #$01
 beq dec_row
 bne inc_row

col_check:
 lda COLFLIP_FLAG
 cmp #$01
 beq dec_col
 bne inc_col

inc_row:
 inc ROW
 rts

dec_row:
 dec ROW
 rts

inc_col:
 inc COLUMN
 rts

dec_col:
 dec COLUMN 
 rts

; ======= move the graph ======= 
;move: inc ROW
; inc COLUMN

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
 dcb 03,07,07,07,03
 dcb 03,07,07,07,03
 dcb 03,07,07,07,03
 dcb 00,03,03,03,00
```

#### 3. What I have learned from this lab
When I have completed this lab, I felt this lab was not that much difficult. This is because now I know how to write the following functions in 6502 assembler: 
- how to calculate the position using the row and column value
- how to draw the graphic in the bit-mapped screen
- how to move the graph
- how to get whether the graph is on the last line or not
- how to get key
- how to move the ball using the key user input

Before I have known how to write codes to do those functions, I was struggled to do this lab. I feel proud I have completed this lab.

[lab3-Bouncing graphic part1]: https://hyunjileetech.github.io/lab3-bouncing-graphic-part1-calculate-pointer
[lab3-Bouncing graphic part2]: https://hyunjileetech.github.io/lab3-bouncing-graph-drawing-and-moving-graph