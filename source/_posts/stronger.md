---
layout: post
title: "每天多一点/堆、栈/全局和局部变量"
subtitle: "每天一个知识点/一句话"
date: 2017-03-27
author: "Yuanliang"
catalog: true
tags:
	- 总结
	- 学习
	- 2017
---

From 梁启超
> 男儿志兮天下事，但有进兮不有止，言志已酬便无志！


### 2017
#### 3月27日 堆和栈
   - 栈是用来存值的，堆是用来存引用的
   - JavaScript的类型分为基本类型（bool,string,null,undefined,number）和引用类型（object,array）
   - 基本类型保持在栈中，引用类型保存在堆中
   ![stack-heap](stackHeap.jpg)
   
   
__为什么会有栈内存和堆内存之分？__
>通常与垃圾回收机制有关。为了使程序运行时占用的内存最小。
>
>当一个方法执行时，每个方法都会建立自己的内存栈，在这个方法内定义的变量将会逐个放入这块栈内存里，随着方法的执行结束，这个方法的内存栈也将自然销毁了。因此，所有在方法中定义的变量都是放在栈内存中的；
>
>当我们在程序中创建一个对象时，这个对象将被保存到运行时数据区中，以便反复利用（因为对象的创建成本通常较大），这个运行时数据区就是堆内存。堆内存中的对象不会随方法的结束而销毁，即使方法结束后，这个对象还可能被另一个引用变量所引用（方法的参数传递时很常见），则这个对象依然不会被销毁，只有当一个对象没有任何引用变量引用它时，系统的垃圾回收机制才会在核实的时候回收它。

#### 3月28日 深拷贝和浅拷贝
> 和3月27日的内容想结合。在JS中引用类型在栈中保存的是地址。
``` javascript
	var obj = { a:1, arr: [2,3] };
	var shadowObj = shadowCopy(obj);//浅拷贝
    var obj2 = deepCopy(obj)//深拷贝
```
- 深拷贝和浅拷贝只针对这些复杂引用类型
- 浅拷贝只将对象的各个属性进行复制，不会进行递归复制 
  >_var obj1= obj不是浅拷贝，只是单纯的指向同一地址，浅拷贝中简单属性不会相互影响，体会与shadowObj的不同_
  >![copy](copy.png)
- 深拷贝会将对象下属性进行递归复制，对引用类型不会直接使用原地址，而是new了一个新对象，改变复制后对象中的属性，不会对原对象造成影响。
  >![deepCopy](deepCopy.jpg)

#### 3月29日 全局变量和局部变量
- 全局和局部变量对应的是全局和局部作用域（函数作用域）
- 局部作用域的分割是用function,而不是{}
- 在全局作用域声明的变量为全局变量，在局部作用域内声明的变量（使用var）为局部变量
```javascript
  <script> 
     var a = 'global';        //这是全局变量
     function partFun() {
        a1 = 'global2';       // 全局变量，函数作用域内 没有使用var声明的变量仍然是全局变量
        var a3 = 'partial';   //局部变量
     }
  </script>
```
- 全局变量可在局部作用域中使用，局部变量只可在其局部作用域内使用
- 全局变量和局部变量重名时，局部变量覆盖全局（可通过 window.全局变量，访问到全局变量），作用域链！！！
``` javascript
    var a = 'global';
    function partFun() {
        var a = 'partial';
        console.log(a);           //partial 产生了覆盖
    	console.log(window.a);    //global
    }
    partFun();
```

- 局部变量的声明会提前(建议局部变量什么放在代码头)
```javascript
   <script>
      var a = 'global';
      function partFun() {
        console.log(a);
        var a = 'partial';
        console.log(a);
      }
      partFun();
   </script>
   
   
   输出结果为 undefined partial  而不是 global partial  
   局部变量声明提前，以上代码等价为：
   
   
   <script>
      var a = 'global';
      function partFun() {
         var a;
         console.log(a);
         a = 'partial';
         console.log(a);
      }
      
      partFun();
   </script>
```
- 局部变量在函数执行完后销毁，全局变量在页面关闭后销毁

#### 3月30日 如何做到鼠标选中后的背景色改变？

在css 代码中加入如下代码：
``` css
::selection{background:blue; color:white;}  
::-moz-selection{background:blue; color:white;}  
::-webkit-selection{background:blue; color:white;}  
```
- 注意区分伪类和伪元素   [伪类和伪元素](http://www.cnblogs.com/zhangruiping/p/4296548.html)
- css3将 `:`用于定义伪类(:hover,:first-chidl())， `::`用于定义伪元素（::before,::after）
- CSS 伪类用于向某些选择器添加特殊的效果
- CSS 伪元素用于将特殊的效果添加到某些选择器
- 伪类偏向于元素的动作行为，伪元素偏向于元素的属性

#### 3月31日  三月的最后一天 HB

> 人们声称最美好的岁月其实都是最痛苦的，只是事后回忆起来的时候才那么幸福 。--柴静《看见》
















