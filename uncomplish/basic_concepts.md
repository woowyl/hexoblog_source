---
layout: post
title: "基础概念小节"
subtitle: "开发中的你要知道的一些概念"
date: 2017-05-12
author: "Yuanliang"
catalog: true
tags:
	- tag1
	- tag2
	- tag3
---

## 编译与链接
### 编译
要区分两个概念，解释性语言和非解释性语言


### l链接

## 语法糖

#### 定义
>指计算机语言中 ***添加*** 的某种语法，这种语法对语言的功能并没有影响，但是更方便程序员使用。通常来说使用语法糖能够增加程序的可读性，从而减少程序代码出错的机会。

#### 举例说明
angularJS中的配置为例，
语法糖写法：
```javascript
angular.module('myApp', [])
     .factory('myFactory', function(){
  		var service = {}; 11
   		eturn service;
	})
	.directive('myDirective', function(){
		return {
			template: '<button>Click me</button>'
   	}
})
```
等同于下面的写法：

```javascript
angular.module('myApp', [])
.config(function($provide ,$compileProvider) {
    $provide.factory('myFactory', function() {
        var service = {};
        return service;
    });
    $compileProvider.directive('myDirective', function() {
        return {
            template: '<button>Click me</button>'
		}; 
    });
});

```


## title3
