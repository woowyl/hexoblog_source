---
layout: post
title: "JavaScript中的递归"
subtitle: "recursion in JavaScript"
date: 2017-06-17
author: "Yuanliang"
catalog: true
tags:
	- 2017
	- 前端
	- JavaScript
---

## 递归

### 定义与概念
在函数中程序直接或者间接得调用自己的情况
>在数学和计算机科学中，递归指由一种（或多种）简单的基本情况定义的一类对象或方法，并规定其他所有情况都能被还原为其基本情况。

#### 关键
- 找出所求函数是否有循环，比如在计算下一项时，是否和之前项目有相同的计算规则或者相同的操作;
- 这个规则或者操作有边界,且循环最终会进入这个边界而停止。有边界代表着这个循环不会一直持续下去。

找出foo(n-1) 和 foo(n)之间的关系，如果更复杂的情况可以是foo(n-2) foo(n-1) foo(n)。
## 递归的示例demo

### 求1-100的和sum(n)
分析:
> sum(1) = 1;   //有边界
sum(2) = 1 + 2 = sum(1) + 2;
sum(3) = 1 + 2 +3 = sum(2) + 3;
... ...
sum(n-1) = 1 + 2 + 3 + 4 + ···+ n-2 + n-1 = sum(n-2) + n-1;
sum(n) = 1 + 2 + 3 + 4 + ···+ n-1 + n = sum(n-1) + n;

得出函数：
``` javascript
   function sum(n) {
      //边界函数
      return n==1 ? 1 : sum(n-1) + n;
   }
```

### 求等差数列 1， 3， 5，7， 9  ···的前n项和，从1开始。
分析：
> sum(1) = 1；
  sum(2) = 1 + 3 = sum(1) + 3;
  sum(3) = 1 + 3 + 5 = sum(2) + 5;
  ... ...
sum(n-1) = 1 + 3 + 5 + 7 + ···+ 2n-5 + 2n-3 = sum(n-2) + 2n-1;
sum(n) = 1 + 3 + 5 + 7 + ···+ 2n-3 + 2n-1 = sum(n-1) + 2n-1;

``` javascript
    function sum(n) {
      return n==1 ? 1 : sum(n-1) + sum(n-1) + 2n-1;
    }
```

***这两个示例其实都是比较简单的示例，1-100也是等差数列的一种，只不过等差值为1而已。所以在找sum(n)和sum(n-1)的关系时，相对来说更清晰简单，因为第n项的值和n的关系很直观。下面这个示例则有些不同***

### 求斐波那契数列1，1，2，3，5，8，13，21，34，···的第n项的值和前n项的和
#### 第n项的值
分析：
> f(1) = 1
> f(2) = 1
> f(3) = 2 = f(1) + f(2)
> f(4) = 3 = f(2) + f(3)
> ... ...
> f(n-1) = f(n-3) + f(n-2)
> f(n) = f(n-2) + f(n-1)

``` javascript
   function Fibonacci(n) {
       if (n == 1 || n == 2) {
           return 1
       } else {
           return Fibonacci(n-1) + Fibonacci(n-2);
       }
   } 

```

#### 前n项的和
分析：
>sum(1) = 1;
>sum(2) = 1 + 1 = 2;
>sum(3) = 1 + 1 + 2 = sum(2) + 2;
>sum(4) = 1 + 1 + 2 + 3 = sum(3) + 3;
>sum(5) = 1 + 1 + 2 + 3 + 5 = sum(4) + 5 = sum(4) + (sum(4) - sum(3)) + (sum(3) - sum(2)) = 2sum(4) - sum(2)
>... ...
>sum(n) = sum(n-1) + f(n) = sum(n-1) + (sum(n-1) - sum(n-2) + sum(n-2)-sum(n-3)) = 2sum(n-1)-sum(n-3) 

``` javascript
   function sum(n) {
      if (n == 1) 
         return 1;
      if (n ==2) 
         return 2;
      if (n == 3)
         return 4;
      if (n > 3) {
         return 2sum(n-1) - sum(n-3)
      }
   }

```

## 数组的对象的深拷贝
递归不仅仅在计算中会使用到，在一些重复操作中也非常的常见，比如数组和对象的深拷贝。

代码实现:
``` javascript 
   function deepCopy(source, destination) {
      if(!source) return;
       var destination = destination || (Array.isArray(source) ? [] :{}) ;
       for (key in source) {
           if (typeof source[key] == "object") 
                destination[key] = deepCopy(source[key],destination[key])
             else
                 destination[key] = source[key]
       }
       return destination;
    }
```



## 如何避免长递归导致的堆栈溢出
当使用递归时，我们常会遇到这样的问题 —堆栈溢出
![](overflow.jpeg)

#### 为什么会有溢出
在 OS / 浏览器 / ... 等平台运行程序的时候，每一次对函数的调用前，都会将当前的运行环境保存起来，以便于函数调用完毕后，恢复现场。这些数据，通常保存在堆栈里。一般在具体实现的时候，这个空间并不是无限的。
也正是因此，函数的最大调用深度也是有限的。    附上网上随便找的一个，关于最大调用深度的测试结果：[各个浏览器调用堆栈(Call Stack)的最大深度]()   由于递归函数的特点，导致如果边界检查存在缺陷，那么就可能导致超过这个最大深度，从而超出堆栈的存储能力，也就是一般所说的“溢出”。    
理论上来说，如果够无聊的话，手工书写 “一层套一层” 的函数调用代码，也是有可能做到堆栈溢出的。不过按上面链接中的那个测试结果来说，需要写几千次甚至上万次罢了。

#### 如何优化
- 递归改循环


- 使用[尾递归](http://www.jianshu.com/p/269ba1ba1644)


参考文章：
[怎样避免JavaScript中过长递归导致的堆栈溢出？](https://www.zhihu.com/question/30078697)
[JS中的递归](http://www.cnblogs.com/liu666/p/5745301.html)




















