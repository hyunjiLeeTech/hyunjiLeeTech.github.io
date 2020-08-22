---
layout: post
title: "Lab 2: 6502 Assembly Language"
date: 2020-01-16 10:39:10
author: Jane Hyunji Lee
categories: ['SPO600', '6502 Assembly']
tags: ['SPO600', 'Lab2']
permalink: /lab2-6502-assembly-language
---

In this posting, it is going to show some basic 6502 Assembly code examples and how do they work. It contains a description of some commands like TYA and LSR. Finally, it shows how you can draw a green line across the top of the bitmap screen and a blue line across the bottom of the page. Also, it is going to show how you can draw a yellow line down the left side of the screen and a purple line down the right side of the page.

### Bitmap Code
There are four main sections: the base code example I will show, using TYA, using LSR, and using ASL

- # The base code and the description

```
         lda #$00         ; set a pointer at $40 to point to $0200
         sta $40
         lda #$02
         sta $41

         lda #$07         ; color

         ldy #$00         ; set index to 0

loop: sta ($40), y     ; set pixel

         iny                  ; increment index
         bne loop         ; continue until done the page

         inc $41            ; increment the page
         ldx $41            ; get the page
         cpx #$06         ; compare with 6
         bne loop          ; continue until done all pages
```
This code paints the whole pages yellow. By increasing the index which helps to indicate the location of the page and painting that bit yellow, it paints the whole pages yellow at the end.

- # Command TYA
When you add a command ‘TYA’ in between the ‘loop:’ and ‘sta ($40), y’, the result has changed.

```
         lda #$00         ; set a pointer at $40 to point to $0200
         sta $40
         lda #$02
         sta $41

         lda #$07         ; color
         ldy #$00         ; set index to 0

loop: tya
         sta ($40), y         ; set pixel
         iny                  ; increment index
         bne loop         ; continue until done the page

         inc $41         ; increment the page
         ldx $41         ; get the page
         cpx #$06         ; compare with 6
         bne loop         ; continue until done all pages
```

TYA means ‘Transfer Y to A’. Every time the value in a Y register is incremented, the value in the Y register is incremented as well. As the value in the A register takes responsibility to decide what color is painted to the bit, the color has changed for each bit.
There are 16 different colors you can use that can be represented from 0000 to 1111. When the number is over 1111, the above number will be neglected. For example, if the color is 10000, the first number 1 will be ignored and the color displays in the emulator will be 0000.
As the number of bit of each line is 32, a bunch of all different colors is repeatedly painted twice for each line. As a result, the emulator will be painted horizontally.

- # Command LSA
When you add a command ‘LSA’ after the command ‘TYA’, the width of each color is going to be twice.

         lda #$00         ; set a pointer at $40 to point to $0200
         sta $40
         lda #$02
         sta $41

         lda #$07         ; color

         ldy #$00         ; set index to 0

loop: tya
         lsr
         sta ($40), y         ; set pixel
         iny                  ; increment index
         vne loop         ; continue until done the page

         inc $41         ; increment the page
         ldx $41         ; get the page
         cpx #$06         ; compare with 6
         bne loop         ; continue until done all pages

The command ‘LSR’ means ‘Logically shift right’. It shifts the color bit right. For example, if the number stored in resistor A is 1111, it is going to be 0111. Therefore, the width of the color is going to be painted twice. Therefore, the width of the color becomes wider.

|  | Register Y | Register A | Colour |
| --- | ----------- |---|---|
| loop1 | 0000 | 0000 | Black |
| loop2 | 0001 | 0000 | Black |
| loop3 | 0010 | 0001 | White |
| loop4 | 0011 | 0001 | White |
| loop5 | 0100 | 0010 | Red |
| loop6 | 0101 | 0010 | Red |
| loop7 | 0110 | 0011 | Cyan |
| loop8 | 0111 | 0011 | Cyan |
| loop9 | 1000 | 0100 | Purple |
| loop10 | 1001 | 0100 | Purple |

When you write LSR twice, it means register A value logically shifted right twice. Therefore, each color is painted 2*2 times.

|  | Register Y | Register A | Colour |
| --- | ----------- |---|---|
| loop1 | 0000 | 0000 | Black |
| loop2 | 0001 | 0000 | Black |
| loop3 | 0010 | 0000 | Black |
| loop4 | 0011 | 0000 | Black |
| loop5 | 0100 | 0001 | White |
| loop6 | 0101 | 0001 | White |
| loop7 | 0110 | 0001 | White |
| loop8 | 0111 | 0001 | White |
| loop9 | 1000 | 0010 | Red |
| loop10 | 1001 | 0010 | Red |

 ```        
         lda #$00            ; set a pointer at $40 to point to $0200
         sta $40
         lda #$02
         sta $41

         lda #$07

         ldy #$00

loop: tya
         lsr
         lsr
         sta ($40), y         ; set pixel
         iny
         bne loop             ; continue until done the page

         inc $41               ; increment the page
         ldx $41               ; get the page
         cpx #$06            ; compare with 6
         bne loop             ; continue until done all page   
```

Each time you use LSR in this code, the width of the color is going to be wider 2^(the number of LSR you use).


- # Command ASL
The command ASL means ‘Arithmetic shift left’. When you add ASL in between the ‘TYA’ and ‘STA ($40), y’ command, it shifts the value in register A shift left.

|  | Register Y | Register A | Colour |
| --- | ----------- |---|---|
| loop1 | 0000 | 0000 | Black |
| loop2 | 0001 | 0010 | Red |
| loop3 | 0010 | 0100 | Purple |
| loop4 | 0011 | 0110 | Blue |
| loop5 | 0100 | 1000 | Orange |
| loop6 | 0101 | 1010 | Light Red |
| loop7 | 0110 | 1100 | Grey |
| loop8 | 0111 | 1110 | Light Blue |
| loop9 | 1000 | 0000 | Black |
| loop10 | 1001 | 0010 | Red |

```
         lda #$00            ; set a pointer at $40 to point to $0200
         sta $40
         lda #$02
         sta $41

         lda #$07

         ldy #$00

loop: tya
         asl
         sta ($40), y         ; set pixel
         iny
         bne loop             ; continue until done the page

         inc $41               ; increment the page
         ldx $41               ; get the page
         cpx #$06            ; compare with 6

         bne loop             ; continue until done all page   
```

Therefore, half of the number of colors is painted for each bit. The number located in even value number is painted.

When you ass ASL twice, it shifts the value in the register A twice.

|  | Register Y | Register A | Colour |
| --- | ----------- |---|---|
| loop1 | 0000 | 0000 | Black |
| loop2 | 0001 | 0100 | Purple |
| loop3 | 0010 | 1000 | Orange |
| loop4 | 0011 | 1100 | Grey |
| loop5 | 0100 | 0000 | Black |
| loop6 | 0101 | 0100 | Purple |
| loop7 | 0110 | 1000 | Orange |
| loop8 | 0111 | 1100 | Grey |
| loop9 | 1000 | 0000 | Black |
| loop10 | 1001 | 0100 | Purple |

```
         lda #$00            ; set a pointer at $40 to point to $0200
         sta $40
         lda #$02
         sta $41

         lda #$07

         ldy #$00

loop: tya
         asl
         asl
         sta ($40), y         ; set pixel
         iny
         bne loop             ; continue until done the page

         inc $41               ; increment the page
         ldx $41               ; get the page
         cpx #$06            ; compare with 6


         bne loop             ; continue until done all page  
```
The quarter number of colors are painted for each bit. The number located in even value number is painted.
Each time you use ASL in this code, the number of the color is going to shorten 2^(1/the number of ASL you use).

- # Include more INY in the original code
1. # Include INY odd times

```
         lda #$00            ; set a pointer at $40 to point to $0200
         sta $40
         lda #$02
         sta $41

         lda #$07

         ldy #$00

loop: sta ($40), y         ; set pixel
         iny                      ; increment index
         iny
         iny
         bne loop             ; continue until done the page

         inc $41               ; increment the page
         ldx $41               ; get the page
         cpx #$06            ; compare with 6



         bne loop             ; continue until done all page  
```
When you include INY odd times, it paints all bits yellow but seems like a checkerboard animation. The animation becomes slower if you use more INY commands. It is because when you use more INY, index y increases with a bigger gap until it reaches 0 so the loop ends. Since the gap between the previous index y and the recent index y becomes bigger when you use more INY, you can easily see the process it paints each bit.

2. # Include INY even times
```
         lda #$00            ; set a pointer at $40 to point to $0200
         sta $40
         lda #$02
         sta $41

         lda #$07

         ldy #$00

loop: sta ($40), y         ; set pixel
         iny                      ; increment index
         iny
         bne loop             ; continue until done the page

         inc $41               ; increment the page
         ldx $41               ; get the page
         cpx #$06            ; compare with 6



         bne loop             ; continue until done all page

         lda #$00            ; set a pointer at $40 to point to $0200
         sta $40
         lda #$02
         sta $41

         lda #$07

         ldy #$00

loop: sta ($40), y         ; set pixel
         iny                      ; increment index
         iny
         iny
         iny
         bne loop             ; continue until done the page

         inc $41               ; increment the page
         ldx $41               ; get the page
         cpx #$06            ; compare with 6




         bne loop             ; continue until done all page
```
When you include INY even times, the column located in even number field is painted yellow so it eventually displays a stripe pattern. When you include more INY, the gap between each stripe becomes wider because the loop ends when the value of index Y becomes 0.



### Write a Code
## Write a Code #1
Write code to draw a green line across the top of the bitmap screen and a blue line across the bottom
```
              lda #$00             ; set a pointer at $40 to point to $0200
              sta $40
              lda #$02
              sta $41

              lda #$05             ; set color green

              ldy #$00             ; set index to 0

top:        sta ($40), y         ; set pixel

              iny                      ; increment index

              cpy #$20            ; compare y value with 20. If the index is 20, end the loop

              bne top

              lda #$05
              sta $41               ; point the last page

              lda #$06             ; set color blue

              ldy #$e0             ; set the index to indicate the last line

bottom:  sta ($40), y      ; set pixel
              iny                      ; increment index
              bne bottom         ; continue until done the line
```

## Write a code #2
Extend the previous code to draw a yellow line down the left side of the screen and a purple line down the right side
: Add below code next to the 'Write a Code #1' code (after 'bne bottom').
```
right_left: lda #$07         ; set index to 0
        
              sta ($40), y          ; set the color yellow

              tax                       ; transfer a to x
              tya                       ; transfer y to a
              clc                       ; clear carry flag to do ADC (add with carry)
              adc #$1f              ; add with #$1f so it indicates the last line

              tay                       ; transfer a to y
              txa                       ; transfer x to a

              lda #$04              ; set the color purple

              sta ($40), y          ; set pixel
              iny                       ; increment index
              bne right_left       ; continue until done the page

              inc $41                ; increment the page
              ldx $41                ; get the page
              cpx #$06             ; compare with 6 (the last page is 05 so if it is 06, it means all pages are already painted)
              bne right_left       ; continue until done all page
```

## All code for write a code #2
```
              lda #$00             ; set a pointer at $40 to point to $0200
              sta $40
              lda #$02
              sta $41
     
              lda #$05             ; set color green

              ldy #$00             ; set index to 0

top:        sta ($40), y         ; set pixel
     
              iny                      ; increment index

              cpy #$20            ; compare y value with 20. If the index is 20, end the loop

              bne top

              lda #$05
              sta $41               ; point the last page

              lda #$06             ; set color blue
     
              ldy #$e0             ; set the index to indicate the last line

bottom:  sta ($40), y      ; set pixel
              iny                      ; increment index
              bne bottom         ; continue until done the line

              lda #$00             ; set a pointer at $40 to point to $0200
              sta $40
              lda #$02
              sta $41


right_left: lda #$07         ; set index to 0
        
              sta ($40), y          ; set the color yellow

              tax                       ; transfer a to x
              tya                       ; transfer y to a
              clc                       ; clear carry flag to do ADC (add with carry)
              adc #$1f              ; add with #$1f so it indicates the last line

              tay                       ; transfer a to y
              txa                       ; transfer x to a

              lda #$04              ; set the color purple
     
              sta ($40), y          ; set pixel
              iny                       ; increment index
              bne right_left       ; continue until done the page

              inc $41                ; increment the page
              ldx $41                ; get the page
              cpx #$06             ; compare with 6 (the last page is 05 so if it is 06, it means all pages are already painted)
              bne right_left       ; continue until done all page
```

### Impressions of the Assembly Language
As most of the languages I learned are high-level languages such as C++ and Java programming, it is new to learn a low-level language. I have learned Assembly Language is the world of bits and bytes. When I want to write code, I should think every number as a binary. The commands are simple and clear but as it is simple, I should consider what commands should I apply for the result I want to generate.