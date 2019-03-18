---
layout: post
title:  "How to declare component property in TypeScript (Angular)"
date:   2019-02-12 22:43:59
author: Jane Hyunji Lee
categories: Angular
tags:	Angular, Component, Property, Web Programming
cover:  
---

There are 2 ways to declare property in TypeScript.Not declare type.

1. Not declare type

-  Not declare type and value

```
export class AppComponent{
    title;
    deleteHero(){
        this.title = 2; // possible
        this.title = "my 1st App"; // possible
    }
}
```

It can be possible. As data type is not decided and value is not declared at the first time, data type can be changed when it used in other function.

-  Not declare type but declare the value

```
export class AppComponent{
    title = "my 1st App";
    deleteHero(){
        this.title = 2; // error
        this.title = "this is amazing" // possible
    }
}
```

You cannot change the data type. When you declare the variable, you already initialize the value. When you initialize value, the variable’s data type is automatically decided. Therefore, when you declare different data type, ‘Type ‘2’ is not assignable to type ‘String” is occurred.

2. Declare type

```
export class AppComponent{
    title: String;
    deleteHero(){
        this.title = 2; // error
        this.title = "this is amazing" // possible
    }
}
```

export class AppComponent{
     title: String = "my 1st App";
     deleteHero(){
         this.title = 2; // error
         this.title = "this is amazing" // possible
     }
 }
Error TS2322: Type ‘2’ is not assignable to type ‘String’ is occurred.