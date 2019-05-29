---
layout: post
title: "路径理解：绝对、相对、根路径"
subtitle: "/ ./ ../"
date: 2017-04-17
author: "Yuanliang"
catalog: true
tags:
	- 总结
	- 学习
	- 2017
	- 基础
---

#### 绝对路径

  绝对路径最好理解，就是一个完成的路径地址
  - 本地文件绝对路径
   `E:\Data\Mysite\index.html` 
  - 网络web绝对路径
  	`http://www.wangyuanliang.com/` 
    
#### 相对路径

   相对路径，*相对* 即*相对* 与 **当前** 目录的路径
   ![](route.jpeg)
   以上图的b.html为基准，也就是在b.html中写跳转链接
   1.使用同一目录下的文件 
   `<a href="c.html">同目录下文件间互相链接</a>` OR 
   `<a href="./c.html">同目录下文件间互相链接</a>`
   2.使用上级目录的文件
   `<a href="../index.html">使用上级目录的文件</a>`
   3.使用下级目录的文件 (同级文件夹下的文件)
   `<a href="relarive/a.html">使用上级目录的文件</a>` OR
   `<a href="./relarive/a.html">使用上级目录的文件</a>`
#### 根路径

   以`/`开头，表示从路径的根路径开始，
   举例说明定义 /route/index.html 的文件在本地的D盘,无论定义这个文件在哪个层级，
   它对应的本地文件路径为： `D:\route\index.html`
  

#### 其他知识点

 ##### 反斜线`\` 和正斜线`/`的区别
- 网络路径使用的是正斜线 如：`https://github.com/woowyl/woowyl.github.io`
- 在本地系统文件中：
	- Windows 约定使用反斜线 (`\`) 作为路径中的分隔符 `D:\Data\Final\Infrastructure.gdb\EastValley\powerlines`
    - UNIX 系统使用正斜线 (`/`) 如：`/Users/wangyuanliang/workspace/com-petkit-chain-mobile-web/src/settle/views`

#### 在实际使用中的问题

写这篇博客是因为最近项目里，使用http发送了两个api请求：

1. /api/web/inventory/check/repositories
	这个请求,所在的项目路径为：
    - 本地：localhost:8095/<font color=red>check</font>/#!/count/4
    - 测试：sandbox.xxxx.com/<font color=red>check</font>/#!/count/4
    - 正式：xxxx.petkit.com/<font color=red>check</font>/#!/count/4
    
2. /api/web/chauffeur/shuttle/list
   这个请求,所在的项目路径为：
    - 本地：localhost:8095/<font color=red>settle</font>/#!/list
    - 测试：sandbox.xxxx.petkit.com/<font color=red>settle</font>/#!/list
    - 正式：xxxx.petkit.com/<font color=red>settle</font>/#!/list

两个接口在一个项目里，但是二级目录并不同，分别是/settle 和 /check

##### 起初以为这两个接口会分别分发为（以正式为例）：
- 正式：
      xxxx.petkit.com/<font color=red>check</font>/api/web/inventory/check/repositories
	  xxxx.petkit.com/<font color=red>settle</font>/api/web/chauffeur/shuttle/list
      
但接口地址为：
      xxxx.petkit.com/api/web/inventory/check/repositories
	  xxxx.petkit.com/api/web/chauffeur/shuttle/list
      
如果是这种情况就需要在nginx里配置 ：/api/check/和 /api/settle/的路由指向均为 /api/

##### --划重点！！！

但是实际情况是，使用了**根路径**
/api/web/inventory/check/repositories
所以地址指向直接就为：
xxxx.petkit.com/api/web/inventory/check/repositories

##### 那什么情况会出现带目录的情况呢？
使用 api/web/inventory/check/repositories (api前没有使用`/`表示使用当前目录)
才会出现：xxxx.petkit.com/<font color=red>check</font>/api/web/inventory/check/repositories

#### 写在最后

所以
`route/index.html`
`/route/index.html`
`./route/index.html`
`../route/index.html`
`xxx.petkit.com/route/html`
要理解清楚


      
 
   
