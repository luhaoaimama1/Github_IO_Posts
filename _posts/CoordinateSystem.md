---
title: Android-CoordinateSystem ##文章标题
date: 2016-11-5 13:19:37
## updated: 2016-12-29 13:19:37
comments: true ##开启评论 false关闭
tags:
    - android

categories:
    - android
---

主要讲述关于View中坐标系系统~

<!-- more -->

>**可视区域**：如果view，上半显示，下半超出屏幕不显示，那么可视区域是**上半**，而不是整个；

# View中方法

| 方法 | 坐标系  | 含义 | 
| :------------: |:---------------:|:---------------:| 
| getX/Y | parent | mLeft/mTop + getTranslationX/Y()|
| getWidth,getHeight | 自己 | 获取宽高|
| getLeft,getTop,getRight,getBottom | parent |view到parent某一方向的距离|
| getLocalVisibleRect | 自己 |可视区域 |
| getGlobalVisibleRect | srceen |可视区域 |
| getDrawingRect | 自己 | 自身滚动后的Rect|
| getHitRect | parent | 相当于getLeft/Top等值放入Rect |
| getLocationOnScreen | srceen | 获取左上角的点在srceen的位置 |
| getLocationInWindow | window(Activity为Dialog的时候和 screen不一样大) |获取左上角的点在window的位置 |
| getWindowVisibleDisplayFrame | screen |获取到程序显示的区域，包括标题栏，但不包括状态栏。 |

# Event中方法

| 方法 | 坐标系  | 含义 | 
| :------------: |:---------------:|:---------------:| 
| getX/Y | 自己 | |
| getRawX/Y| Srceen | |

# View的滑动方法

>坐标系均为  **自己**

| View的滑动方法 | 效果及描述  |
| :------------: |:---------------:|
| offsetLeftAndRight/offsetTopAndBottom(int offset)| 	水平/竖直方向挪动View,getLeft()/getTop会变的.`注意:requestLayout后一些值丢失`| 
| scrollTo(int x, int y)| 	将View中内容滑动到相应的位置，x，y为正则向xy轴反方向移动。| 
| scrollBy(int x, int y)| 	在scrollTo()的基础上继续滑动xy。| 
| setScrollX/setScrollY(int value)| 	实质为scrollTo()，只是只改变X/Y轴滑动。| 
| getScrollX()/getScrollY()| 获取当前滑动位置偏移量。| 
