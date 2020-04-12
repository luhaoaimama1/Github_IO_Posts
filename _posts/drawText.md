---
title: Android-drawText()详解 ##文章标题
date: 2016-12-18 13:19:37
## updated: 2016-12-29 13:19:37
comments: true ##开启评论 false关闭
tags:
    - canvas
    - drawText

categories:
    - android
---

* 全局声明
    * canvas.drawText(string, **baseLineX**, **baseLineY**, paint)
    * baseLineY=基线的Y;
    * baseLineX=基线的X;
    * 本篇以textSize=100为例
    
>API:canvas.drawText(String text,int start,int end,float x,float y, paint); //截取字符串 然后绘制     

<!-- more -->

<a id="way0"></a>
# 基础了解 

![](http://img.blog.csdn.net/20151229092331153?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

* 除了基线以外，如上图所示，另外还有四条线(通过FontMetrics可以获取到)，分别是ascent,descent,top,bottom，他们的意义分别是：
    * top: 可绘制的最高高度所在线
    * ascent: 系统建议的，绘制单个字符时，字符应当的最高高度所在线
    * descent:系统建议的，绘制单个字符时，字符应当的最低高度所在线
    * bottom: 可绘制的最低高度所在线
    
>[ascent,descent]与[top,bottom]的区别就是安全区与最大区域，但由于制式的不同，每个字体的显示区域并不一定一致，当遇到不一致时，就会裁剪。

<a id="way1"></a>
# 获取FontMetrics
```
paint.getFontMetricsInt();
```
FontMetricsInt: top=-106 ascent=-93 descent=24 bottom=28 leading=0

<a id="way2"></a>
# 归纳重点 
* TextSize的含义
    * 经测试 平时paint.setTextSize的值=descent+bounds.top;
* 一个字的高度
    * 一个字的高度 并不是textSize  而是top与bottom差值(代表绘制一个TextSize大小的字所需要的空间);
        >例如EditText里的字和光标发现高度不一致,光标比字高那么一点

<a id="way3"></a>
# 测量宽度:
通过paint(包括了 textSize),所以给定内容即可测量其宽度

```
int width = (int) paint.measureText(content);
```

<a id="way4"></a>
# 测量所绘制的矩形范围:
通过paint(包括了 textSize),所以给定内容即可测量其宽度

```
Rect bounds = new Rect();
paint.getTextBounds(content, 0, content.length(), bounds);
```

bounds：Rect(4, -75 - 347, 21)
>注意:这里仅仅是显示一个矩形范围,默认这里是baseLineX=0,baseLineY=0,LEFT模式;

<a id="way5"></a>
# setTextAlign

<img src="http://ww4.sinaimg.cn/large/006tKfTcgw1fb921d86qhj30jk0eq3ze.jpg" width="400" >

* Left(默认模式):baseLineX在 绘制矩形bounds的左边
* Center:baseLineX在 绘制矩形bounds的中间
* Right:baseLineX在 绘制矩形bounds的右边

<a id="way6"></a>
# 如何把这个bounds移动到对应的字符显示位置呢?
```
canvas.drawText(string, baseLineX, baseLineY, paint);
int width = (int) paint.measureText(content);
switch (paint.getTextAlign()) {
            case LEFT:
                bounds.offset(baseLineX, baseLineY);
                break;
            case RIGHT:
                bounds.offset(baseLineX- width, baseLineY);
                break;
            case CENTER:
                bounds.offset(baseLineX- width/ 2, baseLineY);
                break;
        }
```
>这里的代码其实就是系统做的逻辑; 

>从默认的baseLineX=0,baseLineY=0,LEFT模式。转变到现在的值

<a id="way7"></a>

# [drawText的工具封装](https://github.com/luhaoaimama1/zone-sdk/blob/master/Android_Zone_Lib/src/and/utils/view/DrawUtils.java)
>在内部类Text里

范例:
```
 DrawUtils.Text.with(canvas, content, 0, y, paint)
                        .align(align)
                        .drawBound(paintBounds)
                        .show(showType);
```

# [Demo演示图-点击可进入代码](https://github.com/luhaoaimama1/zone-sdk/blob/master/Android_Zone_Test/src/com/example/mylib_test/activity/animal/viewa/DrawTextView.java)
<img src="https://ww1.sinaimg.cn/large/006tKfTcgw1fb921xmxlsj30fm0rgmyg.jpg" width="400" >

# 对应 align 与show方法的 范例
**实例demo**:[zone-sdk项目](https://github.com/luhaoaimama1/zone-sdk)运行后:->动画、surfaceView、绘图方面的研究->drawtext工具测试 进入即可看到;

![](http://ww3.sinaimg.cn/large/006tNbRwly1fbw1kiaxghj30ka0gqjrw.jpg)

# Reference&Thanks：

http://blog.csdn.net/harvic880925/article/details/50423762