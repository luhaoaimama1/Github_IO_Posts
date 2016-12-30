---
title: Android-NestedScrolling Child与Parent的实例学习  ##文章标题
date: 2016-10-26 13:19:37
## updated: 2016-11-26 13:19:37
comments: true ##开启评论 false关闭
tags:
    - 事件传递

categories:
    - android
---
遵守约定：就是google写了一套约定规范 这样大家都照规定写就可以兼容了；
**实例demo**:[zone-sdk项目](https://github.com/luhaoaimama1/zone-sdk)运行后:->onTouch事件传递与其辅助类的研究->即可看到;

<!-- more -->

# 初步理解
>[如果不是很了解请先看此](https://segmentfault.com/a/1190000002873657)

>方法对应：子view会 触发 父view方法。一般是子view发起调用，父view接受回调。

| 子view | 父view  |
| :------------: |:---------------:| 
| startNestedScroll | onStartNestedScroll、onNestedScrollAccepted | 
| dispatchNestedPreScroll(child消耗前传递给 parent) | onNestedPreScroll(子view消耗前) | 
| dispatchNestedScroll(child消耗后传递给 parent) | onNestedScroll(子view消耗后) | 
| stopNestedScroll   | onStopNestedScroll | 
>fling和 Scroll 类似 就不说了

# Parent
[`parent流程接入`](http://fromwiz.com/share/s/3Hsjaq1-lQ9Q2SChN02Hkyvk2vEyiC22IktY2lqqNl1d1jF9)   [`parent代码实例`](https://github.com/luhaoaimama1/zone-sdk/blob/master/Android_Zone_Test/src/com/example/mylib_test/activity/touch/NestedScrollingActivity_hongParent.java)

```
在写具体的实现前，先对需要用到的上述方法做一下简单的介绍：

onStartNestedScroll该方法，一定要按照自己的需求返回true，该方法决定了当前控件是否能接收到其内部View(非并非是直接子View)
滑动时的参数；假设你只涉及到纵向滑动，这里可以根据nestedScrollAxes这个参数，进行纵向判断。

onNestedPreScroll该方法的会传入内部View移动的dx,dy，如果你需要消耗一定的dx,dy，就通过最后一个参数consumed进行指定，
例如我要消耗一半的dy，就可以写consumed[1]=dy/2；（消耗后别以为完事了 需要自己调用scroller）

onNestedScroll这个不常用 主要是子view滑动后的剩下的位移交给NestedParent处理

onNestedPreFling你可以捕获对内部View的fling事件，如果return true则表示拦截掉内部View的事件。
（消耗后别以为完事了 需要自己调用scroller的fling）

onNestedFling：同onNestedScroll不咋常用
```
>摘自*HongYang*: http://blog.csdn.net/lmj623565791/article/details/52204039



# Child
 
[`child流程接入`](http://fromwiz.com/share/s/3Hsjaq1-lQ9Q2SChN02Hkyvk0hpNyB0-1QGL2Fj4sK1tPrPG)  [`child代码实例`](https://github.com/luhaoaimama1/zone-sdk/blob/master/Android_Zone_Test/src/com/example/mylib_test/activity/touch/NestedScrollingActivity_Child.java) 

## 方法的使用时机:

startNestedScroll,stopNestedScroll；

![](https://ww3.sinaimg.cn/large/006tKfTcgw1fb8yykhy0yj30m808i76m.jpg)

dispatchNestedPreScroll使用时机  此例为RecyclerView的内容；
(因为是子view消耗钱 父消耗 )  所以 用if判断 父亲是否消耗；

![](https://ww2.sinaimg.cn/large/006tKfTcgw1fb8yyf39tvj30jg04y402.jpg)

dispatchNestedScroll  则和上边不同；是child先消耗了；在吧剩下的给NestedParent；看他消耗不；
>参考RecyclerView

![](https://ww4.sinaimg.cn/large/006tKfTcgw1fb8yy026faj30m80hgjxf.jpg)

## 注意：
1.抖动问题：offsetInWindow 如果不加这个参数会出现抖动，
>因为父亲消耗后，布局会偏移导致  每次的触摸事件e 其实也跟着偏移了(偏移量是上一次的布局的位移)所以e需要修正；


# Reference&Thanks：

https://segmentfault.com/a/1190000002873657

http://blog.csdn.net/lmj623565791/article/details/52204039