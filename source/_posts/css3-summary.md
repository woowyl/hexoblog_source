---
layout:     post
title:      "CSS3 总结"
subtitle:   "CSS3,日常总结"
date:       2015-11-08
author:     "Yuanliang"
catalog:    true
header-img: "post-bg-js-module.jpg"
tags:
    - CSS
    - 总结
---

<p class="lead">日常总结 不断更新</p>


## 动画

### 1、transform：  <font size = 3>定义变化，没有动画！！！</font>
##### 要想让变化有动画必须结合 transition和animation 常用 scale(缩放) rotate（选转） translate(移动)

      eg: .wrap{
                    transform: scale(1);
               }

### 2、transition：   <font size = 3>定义属性在变化时的动画,如hover </font>
##### transition: property duration timing-function delay;
	eg: .wrap {
          width:100px;
          transition:width 2s ease 1s;
     }
     
     .wrap:hover {
          width:200px;
     }


### 3、animation：  <font size = 3>自定义组合动画 使用@keyframe定义名称 @keyframe中多通过transform</font>
##### animation: name duration timing-function delay iteration-count direction;

eg: animation: hideShow 1.5s infinte ease;
      animation: hideShow 1.5s linear .5s 10 alternate[动画轮流反向播放];

## before 与 content counter的结合 实现自动添加章节号
          
     html:
          <dl>
            <dt>标题已</dt>
            <dd>内容劳动力</dd>
            <dd>顶顶顶顶</dd>
            <dd>广告公司大概</dd>

            <dt>标题已</dt>
            <dd>内容劳动力</dd>
            <dd>顶顶顶顶</dd>
            <dd>广告公司大概</dd> 

            <dt>标题已</dt>
            <dd>内容劳动力</dd>
            <dd>顶顶顶顶</dd>
            <dd>广告公司大概</dd>              
        </dl>

    css:
          dt:before {
            content: counter(dt)'.';
           }
           dt {
            counter-increment: dt;
            counter-reset: dd;
           }

           dd:before {
            content: counter(dt)'-'counter(dd)'.';
           }
           dd {
            counter-increment: dd;
           }
    result:

   ![](http://p1.bpimg.com/567571/586c54d5dbb125bb.jpg)
   






