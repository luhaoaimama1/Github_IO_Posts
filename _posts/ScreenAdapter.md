---
title: Android-屏幕适配终极解决方案（包含生成程序dp/sp与解决方案） ##文章标题
date: 2016-4-25 13:19:37
## updated: 2016-4-25 13:19:37
comments: true ##开启评论 false关闭
tags:
    - android
    - 适配

categories:
    - android
---
已经测试我一般用 [方案1](#way2)

<!-- more -->

# 基础了解

如果在手机对应的目录没有找到图片，就会按照一定的策略去其他目录找，那找到了以后就原图显示么？ 非也。
对于放在不同目录下的图片， 系统会按照一定比例对原始的图片进行放大或者缩小， 具体的放大缩小比例可参考下表， 图片所在目录和对应的屏幕密度是相同时图片缩放比例为1，也就是原图显示，而横向的比例表示分别放在该密度手机上运行时图片被缩放的比例。
对原始图片的缩放倍数。
![](https://ww3.sinaimg.cn/large/006tNbRwly1fcj5r2ltxqj30nn0c474l.jpg)

* dpi的运算方式是：
    * dp = √（长度像素数² + 宽度像素数²） / 屏幕对角线英寸数
    * dp：Density-independent pixels，以160PPI屏幕为标准，则1dp=1px，
    * sp：Scale-independent pixels，它是安卓的字体单位，以160PPI屏幕为标准，当字体大小为 100%时， 1sp=1px。
* sp 与 px 的换算公式：
    * sp*dpi/160 = px
* px 与 dp的换算公式：
    * px = dp*dpi/160
    * dp = px / (dpi / 160)
* px 与 sp的换算公式：
    * px = sp*dpi/160
    * sp = px / (dpi / 160)

> 总结：dp=sp?

<a id="way2"></a>
# 方案1
[ldpi,mdip,hdpi,xhdip,xxhdpi生成DP](https://github.com/luhaoaimama1/JavaZone/blob/master/JavaTest_Zone/src/%E9%80%82%E9%85%8D/MakeXml_Dp.java)
[ldpi,mdip,hdpi,xhdip,xxhdpi生成SP](https://github.com/luhaoaimama1/JavaZone/blob/master/JavaTest_Zone/src/%E9%80%82%E9%85%8D/MakeXml_Sp.java)
主要代码就是下面的
```
ldpi("values-ldpi",0.75F),mdpi("values-mdpi",1.0F),hdpi("values-hdpi",1.5F),xhdpi("values-xhdpi",2F),xxhdpi("values-xxhdpi",3F);
```
# 方案2

[如果想支持多种像素文件夹那种 看这个](https://github.com/luhaoaimama1/JavaZone/blob/master/JavaTest_Zone/src/%E9%80%82%E9%85%8D/MakeXml_AccurateSize.java)
 
 
# Reference&Thanks：
 
 [无线手册-4] dp、sp、px傻傻分不清楚[完整] 
 http://zhuanlan.zhihu.com/zhezhexiong/19565895
 
 Supporting Different Screens | Android Developers
 http://developer.android.com/training/basics/supporting-devices/screens.html#create-bitmaps
 
 Iconography | Android Developers
 http://developer.android.com/design/style/iconography.html
 
 Devices and Displays | Android Developers
 http://developer.android.com/design/style/devices-displays.html