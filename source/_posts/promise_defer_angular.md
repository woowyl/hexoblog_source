---
layout: post
title: "Promise,defer,angular"
subtitle: "单线程的JS如何摆脱回调地狱"
date: 2017-06-10
author: "Yuanliang"
catalog: true
tags:
	- Javascript
	- 2017
	- 前端
	- angular
---

### 一、前端异步地狱
前端单线程异步的特性造成了好多JS特性，其中有一个便是回调的执行方式，这种方式提高了执行效率，和cpu利用率。但同时给我们带来了很多问题，比如多层回调嵌套，当一次ajax请求的结果是另外一个操作的参数时，我们最开始的方法有两种，
1. 将异步操作转为同步（jquery里的 async:false）
2. 把函数写在回调函数里，回调嵌套回调

```javascript
$http.get('/url',param).then(function(data) {
	if(data) {
    	   $http.get('/url2',{param:data.id}).then(function(data2){
           
           	$http.get('url3', {param:data2.result}).then(function(data3){
            		  ......
            	});
            
           })    	
        }    	
},function() {});
```

这两种方法带来的问题也显而易见：
1. 异步转同步会带来卡顿可能导致页面的白屏，如果异步等待时间过长，在这段时间只能等待，浪费时间，效率低下。
2. 回调嵌套回调的方式问题更加明显，代码耦合性很高。以上面代码为例，如果第一次获取的数据（`/url`）是基础数据，如果想复用的话只能再写一次ajax请求，也就是无法复用，而且嵌套层次很深后逻辑混乱。


如遇到一个函数依赖多个异步操作的结果时，用以上两种方式的效率会更加低下。在这种情况下Promise方式应运而生，带着光环来到前端界。


### 二、 ES6中的Promise
不废话，先来看如果用promise解决上述问题的代码实现，然后再上价值和概念：
方法一：
``` javascript
function urlFun(param) {
    return new Promise(function(resolve,reject){
    	$http.get('url',param).then(function(data){
           resolve(data);
        },function(error) {
            reject(error.msg);  //说明失败原因
        })
    })
}


function url2Fun(param) {
   return new Promise(function(resolve,reject){
        var data = urlFun(param); //这步是关键，将之前的操作promise返回
        data.then(function(data){
            $http.get('url2',data).then(function(data2){
               resolve(data2);
            },function(error) {
                reject(error.msg);  //说明失败原因
            })
        })
    	
    })
} 

function url3Fun(param) {
   var p3 = new Promise(function(resolve,reject){
        var data = url2Fun(param); //这步是关键，将之前的操作promise返回
        data.then(function(data3){
            $http.get('url3',data).then(function(data3){
               resolve(data3);
            },function(error) {
                reject(error.msg);  //说明失败原因
            })
        })
    	
    })
    
    return p3;
} 


url3Fun(param); //通过函数直接调用
....

```

方法二：通过链式访问的方式：
``` javascript
function url1Fun(param) {
    return new Promise(function(resolve,reject){
    	$http.get('url1',param).then(function(data){
           resolve(data);
        },function(error) {
            reject(error.msg);  //说明失败原因
        })
    })
}

function url2Fun(param2) {
    return new Promise(function(resolve,reject){
    	$http.get('url2',param2).then(function(data){
           resolve(data);
        },function(error) {
            reject(error.msg);  //说明失败原因
        })
    })
}

function url3Fun(param3) {
    return new Promise(function(resolve,reject){
    	$http.get('url3',param3).then(function(data){
           resolve(data);
        },function(error) {
            reject(error.msg);  //说明失败原因
        })
    })
}

url1Fun(param).then(function(data) {
	return url2Fun(data);
}).then(function(data) {
	rerurn url3Fun(data);
})

```
通过Promise,
1. 将之前嵌套关系解耦(方法一中，url2Fun()任然依赖了url1Fun()，所以方法二链式的方法耦合性更低)作为基础数据可以被其他函数使用。
2. 任然保持了异步操作，不影响代码效率。

当然作为带有光环诞生的神器，其功能肯定不止于此，先看看Promise到底是什么东西，在控制台中输入`consle.dir(Promise)`,得到以下结果：

![](console_promise.jpeg)

可以看Promise是一个函数，具有有构造函数，可以new出新对象，依次看一下对象方法有：
##### 2.1 all()
Promise的all方法提供了**并行执行异步操作**的能力，并且在所有异步操作执行完后才执行回调。这个方法就成功搞定我们之前的困扰—当一个函数的执行依赖多项异步回调返回结果的问题：
假如：url5Fun的执行需要依赖异步url4Fun和异步url3Fun的结果
此时就可以通过all()方法解决：
``` javascript
Promise.all([url4Fun(), url3Fun()])
.then(function(results){
    console.log(results); //result里有url4Fun()和url3Fun()的结果，只有两个均执行成功后才会执行这个方法。
    url5Fun(result);
});

```

##### 2.2 race()
all方法的效果实际上是「谁跑的慢，以谁为准执行回调」，那么相对的就有另一个方法「谁跑的快，以谁为准执行回调」，这就是race方法，这个词本来就是赛跑的意思。通俗点讲就是，有一个方法执行成功就执行回调。rece()中的函数都会执行，但是回调只执行一次。

##### 2.3 resolve() 与 reject()
Promise的构造函数接收一个参数，是函数，并且传入两个参数：resolve，reject，分别表示异步操作执行成功后的回调函数和异步操作执行失败后的回调函数。
```javascript
    var p = new Promise(function(resolve, reject){
    	resolve('成功回调');
        reject('失败返回');
    });
```
其实这里用“成功”和“失败”来描述并不准确，按照标准来讲，Promise，resolve是将Promise的状态置为fullfiled，reject是将Promise的状态置为rejected。

##### 2.4 then() 与 catch()
then()相当于去获取到异步promise的结果；
catch()是在then()执行出现错误后执行的方法，使代码不会停滞，继续执行、

```javascript	
var p = new Promise(function(resolve, reject){
    var a = Math.random();
    if(a>0.5){
      resolve('成功回调');
    }else{
      reject('失败返回');
    }
});
    
p.then(function(data) {
    console.log(data);//当随机数大于0.5时返回：成功回调，小于0.5时：失败返回
    conaole.log(test);
}).catch(function(error){
    //这里会返回test is undefined
});
```

### 三、Angular的Promise实现— $q

当对Promise有了解之后，$q服务便不难理解，$q是angular中promise的一种实现方式。它的方法命名与Promise高度相似或者说一致，只不过比ES6 Promise相比，它的兼容性更好。

##### 3.1 $q
$q是Angular的一种内置服务，它可以使你异步地执行函数，并且当函数执行完成时它允许你使用函数的返回值（或异常）

##### 3.2 $q.defer()
defer的字面意思是延迟，$q.defer() 可以创建一个deferred实例（延迟对象实例）。

##### 3.3 promise
``` javascript
  var deferred = $q.defer();  //通过$q服务注册一个延迟对象 deferred
  var promise = deferred.promise;  //通过deferred延迟对象，可以得到一个承诺promise，而promise会返回当前任务的完成结果
```



##### 3.4 $q 中的实例
``` javascript
   function deferFun() {
        var defer = $q.defer();
        setTimeout(function(){
            var random = Math.random(); 
            $scope.random = random;
            defer.resolve(random);
        },5000)
        return defer.promise;
    }


    function deferFun2() {
        var defer = $q.defer();
        var result = deferFun();
        result.then(function(data){
            if(data > 0.5) {
                return  defer.resolve(true);
            }else{
                return defer.resolve(false);
            }
        });

        return defer.promise;
    }

    $scope.clickFun = function() {

        var result = deferFun2();
       
        result.then(function(data) {
            if(data) {
                alert('这是一个大于0.5的随机数');
            } else {
                alert('这是一个小于0.5的随机数');
            }
        });
    }
    
    $scope.clickFun();
```
执行代码后，会在5秒之后alert操作结果。

### 四、ES7中异步的实现 
[体验异步的终极解决方案-ES7的Async/Await](http://www.tuicool.com/articles/QbABJzq)

[参考文章-大白话讲解Promise（一）](http://www.cnblogs.com/lvdabao/p/es6-promise-1.html)
