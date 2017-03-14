---
title:  Android疑难问题汇总 ##文章标题
date: 2016-7-13 13:19:37
## updated: 2016-12-29 13:19:37
comments: true ##开启评论 false关闭
tags:
    - android

categories:
    - android
---
    
持续收集ing 敬请留言~

<!-- more -->

*   当一个对象被当作参数传递到一个方法后，此方法可改变这个对象的属性，并可返回变化后的结果，那么这里到底是值传递还是引用传递? 
  
    *   基本类型: 按值传递。既传递值的副本。实际两个对象  
    *   对象数组: 按值传递。传递 引用的副本。实际一个对象
        >Tips:数组需要注意的地方
        ```
         handValue(sort[1]);//传递的是sort[1]值的副本
         handValue2(sort);//传递的是sort的引用的副本
        ```