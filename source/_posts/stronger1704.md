---
layout: post
title: "一道数组排序的面试题 -201704"
subtitle: "每天一个知识点/一句话"
date: 2017-04-6
author: "Yuanliang"
catalog: true
tags:
	- 总结
	- 学习
	- 2017
---

From 《论语》
> 一瓢饮，一箪食，在陋巷，人不堪其忧，回也不改其乐。贤哉回也！


### 2017
#### 4月6日 
>《不第后赋诗菊》
> 唐 · 黄巢
>待到秋来九月八，我花开后百花杀。
>冲天香阵透长安，满城尽带黄金甲。
   		
#### 4月7日 移动端调试工具总结

#### 4月11日 H5 replaceState 与 pushState

#### 4月14日 Array.sort()
今天遇到了一个数组排序的问题
> var arr = [
> {a:5,b:3},
> {a:1,b:3},
> {a:2,b:3},
> {a:4,b:3},
> {a:3,b:3}
> ]
>请按照数组对象中，a的大小对数组进行排序。

刚看到这个需求，我首先想到的是angular的
`$filter('orderBy')(arr,'-a',reverse);`
当然这是最简单的方法。
但如果没有angular引入呢？
``` javascript
    arr.sort(funciton(x,y){
        return x.a-y.a;
    	//return x.a > y.a ? 1 : 0;//返回1表示交换位置，返回0表示不交换
    })
```
最后又思考 如果不使用sort()还有其他方法吗？换个说法，就是说如何实现sort()方法。
这里就涉及到了排序算法，从阮老师的博客里看到这个[动画演示](http://jsdo.it/norahiko/oxIy/fullscreen),下面这段代码就是快速排序的实现方式

``` javascript
    var quickSort = function(arr) {
　　if (arr.length <= 1) { return arr; }
　　var pivotIndex = Math.floor(arr.length / 2);
　　var pivot = arr.splice(pivotIndex, 1)[0];
　　var left = [];
　　var right = [];
　　for (var i = 0; i < arr.length; i++){
　　　　if (arr[i] < pivot) {
　　　　　　left.push(arr[i]);
　　　　} else {
　　　　　　right.push(arr[i]);
　　　　}
　　}
　　return quickSort(left).concat([pivot], quickSort(right));
};
```

#### 4月24日  
最难的不是失去你最爱的人，而是看他们受苦。











