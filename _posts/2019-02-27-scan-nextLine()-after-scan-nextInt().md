---
layout: post
title:  "scan.nextLine() after scan.nextInt() in Java"
date:   2019-02-25 22:43:59
author: Jane Hyunji Lee
categories: Java
tags:	Java, nextInt(), nextLine(), Scanner
cover:  
---

If you use the nextLine() method immediately following the nextInt() method, recall that nextInt() reads integer tokens; because of this, the last newline character for that line of integer input is still queued in the input buffer and the nextLine() will be reading the remainder of the integer line (which is empty)

For example, if the input is

`23
23.5
23.5 is double and 23 is integar number.
`
```
import java.util.Scanner;
public class Solution{
  public static void main(String[] args){
    Scanner scan = new Scanner(System.in);
    int i = scan.nextInt();
    double d = scan.nextDouble();
    String s = scan.nextLine();

    System.out.println("String: " + s);
    System.out.println("Double: " + d);
    System.out.println("Int: " + i); 
  }
}
```
The output of above code will be
`
String: 
Double: 23.5
Int: 23
`
```
import java.util.Scanner;
public class Solution{
  public static void main(String[] args){
    Scanner scan = new Scanner(System.in);
    int i = scan.nextInt();
    double d = scan.nextDouble();
    scan.nextLine();
    String s = scan.nextLine();

    System.out.println("String: " + s);
    System.out.println("Double: " + d);
    System.out.println("Int: " + i); 
  }
}
```
The output of above code will be
`
String: 23.5 is double and 23 is integar number.
Double: 23.5
Int: 23
`
scan.nextLine() get ‘\n’ so String s can take whole sentence.