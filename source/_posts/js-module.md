---
layout: post
title: "JS模块化"
subtitle: "浅谈javascript的模块化编程"
date: 2017-05-04
author: "Yuanliang"
catalog: true
tags:
	- 总结
	- JavaScript
	- 学习
	- 2017
---

## 写在前面

1. 模块化开发是前端经常提的，但什么是模块化，怎么做就算是模块化
2. 下文从三个大的方面说明JS的模块化编程，分别是：是什么，为什么，怎么做？

## 为什么

#### 烦恼的命名冲突
##### 一、最原始的写法
 ```javascript
function fun1() {
	//do something;
}

function fun2() {
   //do something
}
 ```
 上面的代码可以说已经是一个模块，但是缺点也显而易见，fun1和fun2是全局可见的，污染了全局变量，当代码量过大时，无法保证不与其他模块间发生命名冲突。

##### 二、对象写法 （Namespace 模式）
```javascript
  var APP = {
  
      _attr: 'i'm an  attribute',
      
      fun1: function{
       	//do something
      },
      
      fun2: function {
      	//do something
      }
  }
  
  APP.fun1();
```
 - 减少了GLOBAL上的变量数目
 - 本质是对象，不安全。比如外部代码可以改写内部计数器的值。

##### 三、匿名闭包 （IIFE:Immediately-Invoked Function Expression）
``` javascript
var APP = (function(){
	var _count = 'i am inner attr';
    
    var fun1 = function() {
    	//do something
        console.log(_count);
    };
    var fun2 = function() {
    	//do somethging
    };
    
    return {
    	fun1: fun1,
        fun2: fun2
    }
})()

APP.fun1(); // i am inner attr;
APP._count  // undefined;
```
- Funtion 是JavaScript的唯一Local Scope
- 使用IIFE解决了命名的问题，但是如果模块需要依赖其他模块的问题并未解决，此时需要下一步

##### 四、匿名闭包加强版

``` javascript
var APP = (function($){
	var _attr = $(DOM); //can user jqeury in this function
    
    var fun1 = function() {
    	console.log(_attr);
    }
    
    return {
    	fun1: fun1
    }

})(Jquery)

APP.fun1();
```
- 这就是模块模式，是现代模块实现的基石！

###### 至此，第一个问题“烦恼的命名冲突”看似已基本解决，但仍有一些问题需要思考：

- demo里只用到了一级命名空间，随着代码量增加，会不会出现，Global.Module1.App.fun1(),这样冗长的命名？
- 保留这个问题看下一个大项

#### 繁琐的文件依赖
先看一段JS代码
```javascript
    <script src="vendor/jquery/jquery-1.9.1.min.js"></script>
    <script src="vendor/jquery/jquery.mobile.event.min.js"></script>
    <script src="vendor/jquery/jquery.cookie.js"></script>
    <script src="vendor/bootstrap/js/bootstrap.min.js"></script>
    <script src="vendor/pagination/jquery.pagination.js"></script>
    <script src="js/mars.js"></script>
    <script src="js/config.js"></script>
    <script src="js/menu.js"></script>
    <script src="js/metis-menu.js"></script>
```

当网页功能简单的时候，我们有时候只需一个JS文件，但是随着功能的提升，我们需要引入更多的文件。以上代码就是现实中的一个例子，它存在着一些问题：
- 加载的时候，浏览器会停止网页渲染，加载文件越多，网页失去响应的时间就会越长
- 由于js文件之间存在依赖关系，因此必须严格保证加载顺序（比如上例的jquery-1.9.1.min.js要在jquery.pagination.js的前面），依赖性最大的模块一定要放到最后加载，当依赖关系很复杂的时候，代码的编写和维护都会变得困难。
- 对于首页的一些页面，有些JS无需全部加载，没有实现按需加载。

** 所以我们急需一个能够解决这些问题的js标准出来，其实以上功能我们完全可以自己写一些js的库区实现，按需加载、依赖、命名空间，但为很么还要需要一个标准出来呢？**
 
 我们为什么需要模块标准？
 >因为有了模块，我们就可以更方便地使用别人的代码，想要什么功能，就加载什么模块。<br>
  但是，这样做有一个前提，那就是大家必须以同样的方式编写模块，否则你有你的写法，我有我的写法，岂不是乱了套！考虑到Javascript模块现在还没有官方规范，这一点就更重要了。



## 是什么
 #### 1. 模块化（Module）：一组特定的方法
 #### 2. 模块化的历史
 > 根据上文，在编码中遇到的问题，模块化可以说呼之欲出。其实模块化并不是新名词，在其他语言中，模块化已存在已久，但是在JavaScript中一直没有确定的标准，直到服务器端JS的不断发展，COMMONJS规范的出现...


 #####  COMMONJS
 2009年，美国程序员Ryan Dahl创造了node.js项目，将javascript语言用于服务器端编程。
 这标志"Javascript模块化编程"正式诞生。
 在浏览器端，代码规模毕竟比较小，但是当JS运行在服务器端，代码规模会非常大，一定要有模块，与操作系统和其他应用程序互动，否则根本没法编程。NodeJs的系统模块就是通过CommonJS规范项目实现的。
 
 *注：CommonJS只是一个标准规范，Node.js是这种规范的实现。*
 ``` javascript
 // server.js
var http = require("http"),
    PORT = 8000;

http.createServer(function(req, res){
    res.end("Hello World");
}).listen(PORT);

console.log("listenning to " + PORT);
 ```
 
>OK，那么既然有了规范，那么我们就全部按照COMMONJS进行编程，皆大欢喜，但问题出现了。COMMONJS是针对服务器端的规范，在模块化的加载中，采用了同步阻塞加载方式，也就是说，当遇到require的时候，代码会等到拿到require的结果之后，再进行下一步的操作，这在服务器端无可厚非，因为模块会提前下载到本地磁盘或者内存中，文件加载的时间就是***读取本地文件***的时间，几乎可以忽略不计。但在浏览器端，文件的加载对网络的依赖性很高，如果采取COMMONJS的同步阻塞加载机制，很明显是不可行的。


![](IO.jpeg)

由此，浏览器端的模块化标准应运而生，主要是由两个类型组成：
- CMD (Common Module Definition)
- AMD (Async Module Definition)


 ##### CMD (sea.js)
 
 ```javascript
 define(function(require, export, module){
    var mod1 = require('../mod1');
    mod1.doSomething();
    var mod2 = require('../mo2');
    mod2.doSomething();
 })
 
 ```
 
 ##### AMD (require.js)
  
 ```javascript
 define(['./mod1', './mod2'],function(mod1, mod2){
    var fun1 = mod1.fun1;
    var fun2 = mod1.fun1;
 });
 ```
 
 ##### 两者区别
 - 除了写法上的不同外，两者最大的区别在于，CMD是懒执行，AMD是先执行,看一面这个例

###### 首页
 ```html
 <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>AMD-CMD</title>
</head>
<body>
    <div>
        requireJS和sea.js加载和执行顺序
    </div>
</body>

<!-- AMD-require 引入-->
<script src = "require.js" data-main = "main"></script>

<!-- CMD-sea 引入-->
<script src="sea.js"></script>
<script>
  seajs.use('app', function(app) {
    console.log('i am main');
  });
  
</script>
</html>
 ```
 
 ###### main.js
 ```javascript
 require(['app'],function(){
    console.log('i am main');
});
 ```
 ###### app.js
  ```javascript
//AMD-require

define(['module1', 'module2'], function() {
    console.log("i am app");
});


//CMD-sea

define(function(require, exports, module){
    console.log('i am app');
    require('module1');
    require('module2');
})
 ```
 
 ###### module1.js
  ```javascript
 
//AMD-require

define(function(){
    console.log("i am module1");
});


//CMD-sea

define(function(require, exports,module){
    console.log('i am module1');
})
 ```
 
 ##### module2.js
  ```javascript

//AMD-require

define(function(){
    console.log("i am module2");
});


//CMD-sea

define(function(require, exports,module){
    console.log('i am module2');
})
 ```
 
 可以看到两者的定义上，除了语法不一样外，其他完全保持一致，那首先看一下他们两个的文件加载顺序：
 
 ![](cmd_load.jpeg)
 (CMD加载顺序)
 ![](amd_load.jpeg)
 (AMD 加载顺序)
 
 除了sea.js将main写在html里没有加载可忽略外，其他**加载顺序完全一样**，再看执行顺序
 
 ![](cmd_exec.jpeg)
 （CMD执行顺序）
 ![](amd_exec.jpeg)
 （AMD执行顺序）
 
这是他们的主要差异,require会直接在加载完成后就立马执行，所以module1和module2会在app之前，而sea虽然也加载了module1和module2这两个文件，但是并未立马执行，而是在app.js **requie的地方**才执行，相当于在加载时，他会扫描一遍app.js的所有require加载里面的文件，但并不执行，而是等到在require的地方再执行。

>个人感觉两种方法在效率上并没有太大差别，对浏览器端来说，最大的问题在加载，而两者的加载顺序完全相同。至于执行顺序，CMD可能更符合程序思维，但执行时间分散在了程序执行中，会有局部卡顿现象；而AMD的时间花在开始，在完全加载完成后，后续不需花时间在执行上。

>使用上，我更偏向requrie，可能是先入为主，也可能是JS的回调理解后，require的这种方式，也很容易理解，而且他在命名和变量使用上，感觉更胜一筹。
 
 
 
 
 #### webpack 的时代
 
 #### ES6 module javascirpt模块时代到来

## 怎么做

 
