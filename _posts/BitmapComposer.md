---
title: Android-BitmapComposer(类似于AE的合成模式) ##文章标题
date: 2017-1-19 13:19:37
## updated: 2017-1-19 13:19:37
comments: true ##开启评论 false关闭
tags:
    - android
    - bitmap

categories:
    - android
---

灵感来源于AE,参考AE合成图的步骤;

![](https://ww4.sinaimg.cn/large/006tNbRwly1fbvy8lrq8fj31h80ayt9l.jpg)

<!-- more -->

**实例demo**:[zone-sdk项目](https://github.com/luhaoaimama1/zone-sdk)运行后:->动画、surfaceView、绘图方面的研究->波浪 进入即可看到;

![](https://ww1.sinaimg.cn/large/006tNbRwly1fbvyespt80g307i0dcax1.gif)

> demo原理:第一层是 **黑白图片**,第二层是加过 **波浪Mask(遮罩的-既android的PorterDuff)**的**原图**

# 代码范例:


```
 image2.post(new Runnable() {
             @Override
             public void run() {
                 Bitmap bt = SampleUtils.load(WaveActivity.this, R.drawable.aaaaaaaaaaaab)
                         .bitmap();
 
                 ColorMatrix colorMatrix = new ColorMatrix();
                 colorMatrix.setSaturation(0);
 
                 BitmapComposer bitmapComposer = BitmapComposer.newComposition(bt.getWidth(), bt.getHeight(), Bitmap.Config.ARGB_8888);
                 Matrix first = new Matrix();
                 first.postTranslate(0, -20);
                 new WaveHelper(bt.getWidth(), bt.getHeight(), new WaveHelper.RefreshCallback() {
 
                     @Override
                     public void refresh(Bitmap wave) {
                         Bitmap render = bitmapComposer.clear()
                                 .newLayer(BitmapComposer.Layer.bitmap(bt)
                                         .colorFilter(new ColorMatrixColorFilter(colorMatrix)))
                                 .newLayer(
                                         BitmapComposer.Layer.bitmap(bt)
 //                                              .colorFilter(new ColorMatrixColorFilter(colorMatrix))
                                                 .mask(wave, PorterDuff.Mode.DST_IN)
 //                                              .matrix(first)
                                 )
                                 .render();
                         image2.setImageBitmap(render);
                     }
                 });
 
             }
         });
```

# Reference&Thanks：

https://github.com/race604/WaveLoading