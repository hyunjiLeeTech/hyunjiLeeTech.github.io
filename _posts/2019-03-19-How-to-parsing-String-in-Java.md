---
layout: post
title: "How to parse String in Java"
date: 2019-03-19 14:09:10
author: Jane Hyunji Lee
categories: Java
tags: Java, Parse String
---

There are 2 ways to parse String
- Using Tokenizer
By using this way, you can parse String which consists of word and spaces (include tab)

```
//sould import 'import java.util.StringTokenizer
String line = "This\tis\texample\tsentence";
StringTokenizer token = new StringTokenizer(line);

while(token.hasMoreToken()){
    System.out.println(token.nextToken());
}

```
The output is  
`This`  
`is`  
`example`  
`sentence`  

- Using Scanner
By using this way, you can parse String by uisng certain delimeter.

```
//sould import 'import java.util.Scanner;'
String input = "This delimiter is delimiter example delimiter sentence";

Scanner s = new Scanner(input).useDelimiter("\\s*delimiter\\s*");
	 
while(s.hasNext()) {
    System.out.println(s.next());
}
```
The output is  
`This`  
`is`  
`example`  
`sentence`  