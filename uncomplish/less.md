---
layout: post
title: "Less的适应场景"
subtitle: "用编程的思维写CSS"
date: 2017-06-16
author: "Yuanliang"
catalog: true
tags:
	- 2017
	- 前端
	- CSS
---

## 什么情况下会用
- 浏览器的兼容
```less
.border-radius(@radius: 10px) {
   -webkit-border-radius: @radius;
   -moz-border-radius: @radius;
   border-radius:@radius;
}

.wrapper {
   width: 200px;
   height: 200px;
   .border-radius(10x);
}
```


## 讲义

- 匹配模式（类似js中的if）
- 嵌套
- 

## title3
