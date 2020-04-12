---
title: Handler、Looper、Message、MessageQueue基础流程分析图解 ##文章标题
date: 2017-3-15 20:19:37
## updated: 2017-3-15 13:19:37
comments: true ##开启评论 false关闭
tags:
    - android

categories:
    - 源码分析
    - android
---

# 图解

![](http://ww3.sinaimg.cn/large/006tKfTcgy1fdnifh0enlj30to0phmxn.jpg)

<!-- more -->

[图片项目地址 可以复制下来 进行修改](https://www.processon.com/view/link/58c8dffde4b01c1b4ab8b2ad)

> ThreadLocal定义：不同线程存取　不会相互影响　而且用到的是一个变量！！
> ThreadLocal：解释了为什么 线程里用的时候要prepare()和loop()
> 还有Activity创建的时候已经默认调用了prepare()与loop() 所以不需要你去弄.
        
# 源码分析：
老司机们都知道，Android的线程间通信就靠Handler、Looper、Message、MessageQueue这四个麻瓜兄弟了，那么，他们是怎么运作的呢？下面做一个基于主要源代码的大学生水平的分析。 [原文链接](http://anangryant.leanote.com/post/Handler%E3%80%81Looper%E3%80%81Message%E3%80%81MessageQueue%E5%88%86%E6%9E%90)

# Reference&Thanks：

http://anangryant.leanote.com/post/Handler%E3%80%81Looper%E3%80%81Message%E3%80%81MessageQueue%E5%88%86%E6%9E%90