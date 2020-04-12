---
title: 关于View的三大流程与自定义一些方法的总结 ##文章标题
date: 2018-2-6 00:19:37
## updated: 2017-3-14 13:19:37
comments: true ##开启评论 false关闭
tags:
    - android
    - View三大流程
categories:
    - android
    - View三大流程
---

# View树图
![](http://ww1.sinaimg.cn/large/006tKfTcly1fo6oxez6q4j30t40keq35.jpg)

<!-- more -->

# 简易流程总结
![](http://ww1.sinaimg.cn/large/006tKfTcly1fo6ozil4d5j30u40ms0th.jpg)
# View的三大流程
![](http://ww4.sinaimg.cn/large/006tKfTcly1fo6p2fnfzbj31kw0khjtk.jpg)
## request与ViewRootImpl的关系
![](http://ww3.sinaimg.cn/large/006tKfTcly1fo6p11ggfxj30qw0rmq3n.jpg)
## invalidate与ViewRootImpl的关系
![](http://ww3.sinaimg.cn/large/006tKfTcly1fo6p1vadyjj31720ycq5b.jpg)

# 测量与布局的详细了解

## MeasureSpec的了解
![](http://ww2.sinaimg.cn/large/006tKfTcly1fo6p38tlodj30t809yaa8.jpg)
## ViewGroup的测量
![](http://ww2.sinaimg.cn/large/006tKfTcly1fo6p448q78j31dy0osgns.jpg)
## onLayout的了解
![](http://ww3.sinaimg.cn/large/006tKfTcly1fo6p4y9wv6j31800aqaas.jpg)
## 测量相关方法
![](http://ww2.sinaimg.cn/large/006tKfTcly1fo6p6ln1i8j314g0j8gnw.jpg)
![](http://ww3.sinaimg.cn/large/006tKfTcly1fo6p682yp8j31060k8wgz.jpg)
![](http://ww2.sinaimg.cn/large/006tKfTcly1fo6p7bs37yj31fi0h6dh3.jpg)
##Padding与Margin的概念 通过FrameLayout研究padding matchParent和weight的实现
![](http://ww4.sinaimg.cn/large/006tKfTcly1fo6p7uvww5j31hi10an2v.jpg)

最后附上上面所有的流程图
https://www.processon.com/view/link/5a742abce4b0615ac048b7f5

# Reference&Thanks：

Android开发艺术探索(书)
Android View 测量流程(Measure)完全解析 http://blog.csdn.net/a553181867/article/details/51494058
Android View 布局流程(Layout)完全解析 http://blog.csdn.net/a553181867/article/details/51524527
Android View 绘制流程(Draw) 完全解析 http://blog.csdn.net/a553181867/article/details/51570854
Android View 深度分析requestLayout、invalidate与postInvalidate http://blog.csdn.net/a553181867/article/details/51583060