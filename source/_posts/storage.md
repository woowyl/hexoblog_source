---
layout: post
title: "浏览器端的数据存储"
subtitle: "cookie session与Localstorage"
date: 2017-06-15
author: "Yuanliang"
catalog: true
tags:
	- 2017
	- 基础
	- 前端
---

## 提纲
1. cookie 与 session
2. sessionStorage 与 LocalStorage


#### 1. cookie 与 session

##### 1.1 定义
session：是一个***抽象的概念***，开发者为了实现中断和继续等操作，将user agent和server之间一对一的交互，抽象为“会话”，进而衍生出“会话状态”，也就是session的概念。所以session 并不保存数据，它是个概念，注意和sessionStorage区分。

cookie：是一个***实际存在的东西***，http协议中定义在header中的字段，可以认为是session的一种后端无状态实现，

[几个关键字段](http://blog.csdn.net/lijing198997/article/details/9378047)：
>Set-Cookie: name = VALUE;  //用于设置键值对
expires = DATE;             //设置过期时间
path = PATH;//设置域名下的路径 指明了在发Cookie消息头之前必须在请求资源中存在一个URL路径
domain = DOMAIN_NAME; //设置域名 指示cookie将要发送到哪个域或那些域中。默认情况下，domain会被设置为创建该cookie的页面所在的域名。


以知乎的cookie为例：
![](zhihu_cookie.jpeg)
##### 1.2 存在的理由
HTTP是无状态的的协议，所以服务器端需要记录用户的状态时，就需要某种机制来识别具体的用户，这个机制就是Session。比如小佩现在系统中的sessionToken,调用接口传入token就可以告知后台，用户的身份信息。这个token是保存在服务端的，有一个唯一的标识。有了token，如何在服务端和用户端传递？这时cookie便排上了用场。

每次HTTP请求的时候，客户端都会发送相应的Cookie信息到服务端，比如我们再调用接口时，都会加入一个默认的头部,比如angular中的
``` javascript
	$http.defaults.headers.common['F-Session'] = '10eeb17e-c983-4a3c-a17b-e31a4ac8e3c9';
```
jquery中的：
``` javascript
	$.ajaxSetup({
	 headers:{
		 "F-Session" : $.cookie("sessionToken")
	 }
	});
```
sessionToken一般都是有时间有效性和唯一性，所以可以用于实现单点登录，即同一账号，如果在一个浏览器登录后，如果在其他浏览器里登录，会刷新token。旧的token失效，早先打开的浏览器就可以提示用户，token过期并退出登录。

有人问，如果客户端的浏览器禁用了 Cookie 怎么办？一般这种情况下，会使用一种叫做URL重写的技术来进行会话跟踪，即每次HTTP交互，URL后面都会被附加上一个诸如 sid=xxxxx 这样的参数，服务端据此来识别用户。

另有一种说法，“现在很多大流量网站都是secret cookie当session”

#### 1.3两者关系
可以把cookie理解为session的一种实现，但在实际场景中也常常把cookie当做存储数据的工具，比如：存储用户名和密码（免登陆），存储页面间公用数据（为了不用全局变量）。这种使用常常不太合理，比如，保存在cookie里的数据都将会被http发送到后台，但是除了session功能，我们只是用到存储的话完全没必要将这些信息发送给服务端。而且cookie的存储空间有限，一般为4k。且每个域名下的cookie数量也有限制。所以sessionStorage和localStorage应运而生。



#### 2.  sessionStorage 与 localstorage

##### 1.1定义
sessionStorage与localStorage是H5中定义的方法，一定程度上缓解了cookie存储的压力。


##### 1.2  sessionStorage 、localStorage 和 cookie 之间的区别
共同点：都是保存在浏览器端，且同源的。

区别：cookie数据始终在同源的http请求中携带（即使不需要），即cookie在浏览器和服务器间来回传递；cookie数据还有路径（path）的概念，可以限制cookie只属于某个路径下。存储大小限制也不同，cookie数据不能超过4k，同时因为每次http请求都会携带cookie，所以cookie只适合保存很小的数据，如会话标识。

而sessionStorage和localStorage不会自动把数据发给服务器，仅在本地保存。sessionStorage和localStorage 虽然也有存储大小的限制，但比cookie大得多，可以达到5M或更大。

数据有效期不同，sessionStorage：仅在当前浏览器窗口关闭前有效，自然也就不可能持久保持；localStorage：始终有效，窗口或浏览器关闭也一直保存，因此用作持久数据；cookie只在设置的cookie过期时间之前一直有效，即使窗口或浏览器关闭。

作用域不同，sessionStorage不在不同的浏览器窗口中共享，即使是同一个页面；localStorage 在所有同源窗口中都是共享的；cookie也是在所有同源窗口中都是共享的。Web Storage 支持事件通知机制，可以将数据更新的通知发送给监听者。Web Storage 的 api 接口使用更方便。

|特性|Cookie|localStorage|sessionStorage|
|--|--|--|--|
|数据的生命期|可设置失效时间，默认是关闭浏览器后失效|除非被清除，否则永久保存|仅在当前会话下有效，关闭页面或浏览器后被清除|
|存放数据大小|4K左右|一般为5MB|一般为5MB|
|与服务器端通信|每次都会携带在HTTP头中，如果使用cookie保存过多数据会带来性能问题|仅在客户端（即浏览器）中保存，不参与和服务器的通信|仅在客户端（即浏览器）中保存，不参与和服务器的通信|
|易用性|需要程序员自己封装，源生的Cookie接口不友好|源生接口可以接受，亦可再次封装来对Object和Array有更好的支持|源生接口可以接受，亦可再次封装来对Object和Array有更好的支持|


##### 1.3 应用场景
有了对上面这些差别的直观理解，我们就可以讨论三者的应用场景了。

因为考虑到每个 HTTP 请求都会带着 Cookie 的信息，所以 Cookie 当然是能精简就精简啦，比较常用的一个应用场景就是判断用户是否登录。针对登录过的用户，服务器端会在他登录时往 Cookie 中插入一段加密过的唯一辨识单一用户的辨识码，下次只要读取这个值就可以判断当前用户是否登录啦。曾经还使用 Cookie 来保存用户在电商网站的购物车信息，如今有了 localStorage，似乎在这个方面也可以给 Cookie 放个假了~

而另一方面 localStorage 接替了 Cookie 管理购物车的工作，同时也能胜任其他一些工作。比如HTML5游戏通常会产生一些本地数据，localStorage 也是非常适用的。如果遇到一些内容特别多的表单，为了优化用户体验，我们可能要把表单页面拆分成多个子页面，然后按步骤引导用户填写。这时候 sessionStorage 的作用就发挥出来了。


参考文章：
[知乎：COOKIE和SESSION有什么区别？](https://www.zhihu.com/question/19786827)
[segmentfault:cookie localstorage sessionstorage](https://segmentfault.com/a/1190000002723469)