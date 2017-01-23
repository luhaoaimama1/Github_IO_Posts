---
title: Camera的研究 ##文章标题
date: 2017-1-23 13:19:37
## updated: 2017-1-19 13:19:37
comments: true ##开启评论 false关闭
tags:
    - android
    - camera

categories:
    - android
---

<!-- more -->

# View,Camera 坐标轴分析
>[下面的多图在此项目中](https://github.com/luhaoaimama1/ZAnimate)

| View | Camera(锚点:左上角点)  | Camera Lib|
| :------------: |:---------------:| :-----:|
| ![](https://ww4.sinaimg.cn/large/006tNbRwly1fc0em565jkg307i07i7wm.gif) | ![](https://ww2.sinaimg.cn/large/006tNbRwly1fc0f25baf7g307i07i1l1.gif) | ![](https://ww1.sinaimg.cn/large/006tNbRwly1fc0f2btfm7g307i07i7wl.gif) |
| ![](https://ww4.sinaimg.cn/large/006tNbRwly1fc0f6bb02pj30at08cwel.jpg)   | ![](https://ww3.sinaimg.cn/large/006tNbRwly1fc0fai83kyj30at08cglh.jpg) | 同view |
| 左手坐标系 [判断依据](http://fromwiz.com/share/s/3Hsjaq1-lQ9Q2SChN02Hkyvk1nSH681Fc4lk2iftz320K7Za)  | *camera视角的图片锚点坐标系* | 同view |

> *camera视角的图片锚点坐标系*：其实用的也是左手坐标系,但是为什么会围绕X旋转180呢？ 

> 回答：因为我们的API名字叫做Camera 他是以摄像机的角度去 理解做操作的； 可以自己拿手机拍照去 通过移动摄像机 而让里面的物体移动。就会明白了。 

> Tips:Camera真实**没有移动**(因为getLocationX、Y、Z都是没有移动的)，只是以他的角度去理解； 

# 理解 Camera的translate与rotate


```
    /**
     * Applies a translation transform on all three axis.
     * 
     * @param x The distance to translate by on the X axis
     * @param y The distance to translate by on the Y axis
     * @param z The distance to translate by on the Z axis
     */
    public native void translate(float x, float y, float z);
    
     /**
     * Applies a rotation transform around all three axis.
     * 
     * @param x The angle of rotation around the X axis, in degrees
     * @param y The angle of rotation around the Y axis, in degrees
     * @param z The angle of rotation around the Z axis, in degrees
     * 
     * @see #rotateX(float)
     * @see #rotateY(float)
     * @see #rotateZ(float)
     */
    public native void rotate(float x, float y, float z);
```
看到没关键点是-> **axis 的位移与旋转**；

## 想要理解上面 先跟我做一些事情

>锚点：左上角点

拿起你的手机  平行面对你，已左上角为手机坐标系原点(任何时候 包括 旋转位移以后)。 

* 围绕Y轴做旋转45，然后X位移一段距离distanceX。在旋转75°

## 从中明白了什么
>锚点：左上角点

* 明白什么叫做 axis 的位移与旋转。 就是每次造作都是以 手机左上角(相当于咱们的图片左上角)为坐标系

* 从整体来看我们经过了 三次操作，以每次操作之前，手机左上角的点为例，第一次操作点 记为 operate1，依次 operate2 ，operate3.那么从整体来看 我相当于  operate1 X轴:distanceX的点 rorate1 围绕operate1 旋转45°，然后围绕本身operate3 75°。总结为:
    * 既 旋转之后 +位移操作，就是相当于 **[旋转之前+位移]之后的点** 围绕 **旋转之前的点** 旋转，
    * 旋转不加位移  就相当于 [锚点] 旋转。
    * 这个手机外边的世界就暂且叫做合成坐标系,而手机左上角则暂且叫做锚点坐标系;
    >不管做了多少次操作都遵循这个 规律；

# Camera 传说中 移动旋转点的 解释
>camera的合成坐标系 是 

```
Matrix temp = new Matrix();		// 临时Matrix变量
this.getMatrix(temp);			// 获取Matrix
temp.preTranslate(-centerX, -centerY);	// 使用pre将旋转中心移动到和Camera位置相同。
temp.postTranslate(centerX, centerY);	// 使用post将图片(View)移动到原来的位置
```
temp.preTranslate操作:是把最开的图片移动位置。
>center为例: 左上角(锚点)是center点了,因为开始的图片变了位置,左上角(锚点)对于camera的合成坐标系依然没变

temp.postTranslate操作 移动的是 camera的合成坐标系
> Tips: 旋转的中心点依然是左上角点(锚点 例子中的center点),而不是post中的点。 


## 最后我还要提示下： 
>我们的视角 就是view坐标系(所有View 大小 padding等等都是参考此来设计的)；

* camera translate,rotate 是在合成坐标系里 的视角 ,位移旋转， 位移和旋转的值都是相对于 合成坐标系里 的视角,因为里面有摄像机 
>假如camera向下移动100 以我们的视角可能是89等,因为以camera坐标系的视角(包含摄像机 Z轴 视差等等因素)判定的；
*  Matrix postTranslate 系列,则是 对于合成坐标系的操作， 更改的位置和旋转等。都是 相对于View坐标系的值 
>假如Matrix向下移动100  就是100 这是以View坐标系既我们的视角来判定的；

### 实例讲解  

>[此demo在此项目中](https://github.com/luhaoaimama1/ZAnimate)

![](https://ww1.sinaimg.cn/large/006tNbRwly1fc0l088rrtg307i0dcdwu.gif)

左图关键代码：

```
      camera.save();
        camera.rotateX(-rotateDegree);
        camera.getMatrix(matrix);
        camera.restore();

        // -viewWidth / 2 保持着竖线高度不变的，其他会倾斜；
        matrix.preTranslate(-viewWidth / 2, 0);
        matrix.postTranslate(viewWidth / 2, (float) axisY);
```
右图关键代码：把 **postTranslate**移动的 移动到了**camera.transalte**

```
 camera.save();
        camera.rotateX(-rotateDegree);
        camera.translte(0,(float) axisY,0)
        camera.getMatrix(matrix);
        camera.restore();

        // -viewWidth / 2 保持着竖线高度不变的，其他会倾斜；
        matrix.preTranslate(-viewWidth / 2, 0);
        matrix.postTranslate(viewWidth / 2, 0);
```
>这里的问题是,0度的时候 还能看到？因为你camera.translte(0,(float) axisY,0),因为Y不在是0,和摄像机不保持相同高度后是可以看到的。所以这里用matrix.postTranslate比较好

>这还算好的 如果摄像机移动Z轴的话  会出现和严重的问题,因为这仅仅是一段代码补全就暂且不说了；

# Reference&Thanks：

https://github.com/zhangyuChen1991/Roll3DImageView

