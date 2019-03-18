---
layout: post
title:  "Difference between let and var in JavaScript"
date:   2019-02-17 22:43:59
author: Jane Hyunji Lee
categories: JavaScript
tags:	dataType, JavaScript, let, var
cover:  
---

Main difference between let and var is scope.

let: limited to the block, statement, or expression on which it is used

var: current execution context, which is either the enclosing function or, for variables declared outside any function and global.

```
for(var i = 0; i < 2; i++){
   //...
}
```

```
for(let j = 0; j < 2; j++{
   //...
}
```

console.log("i: " + i);// i: 2
console.log("j: " + j);// ReferenceError: j is not defined