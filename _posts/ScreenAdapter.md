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
 