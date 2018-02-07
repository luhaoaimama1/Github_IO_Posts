---
title: Android-事件传递的学习 ##文章标题
date: 2016-9-17 13:19:37
## updated: 2016-10-17 13:19:37
comments: true ##开启评论 false关闭
tags:
    - android
    - 事件传递

categories:
    - android
---

事件传递的总结图

![](https://ww3.sinaimg.cn/large/006tKfTcly1fo26sk3xr9j319r10cabx.jpg)

https://www.processon.com/diagraming/5a72ba1be4b0874437b58c4f

<!-- more -->


## 处理特殊图形点击事件的几种方式 : 

通过path绘制特殊的点击范围, 放入Region中 然后通过contains是否包含即可

### 兼容onClick1:
    
```
 @Override
    public boolean dispatchTouchEvent(MotionEvent event) {
        return isEventInPath(event)?super.dispatchTouchEvent(event);
    }
参考：http://blog.csdn.net/qibin0506/article/details/52676670
```

### 兼容onClick2:

```
 @Override
    public boolean onTouchEvent(MotionEvent event) {
        return isEventInPath(event)?super.onTouchEvent(event):false;
    }
```

### 自己的回调监听:

```
 @Override
    public boolean onTouchEvent(MotionEvent event) {
    switch (event.getActionMasked()) {
        mListener.onCenterCliched();//
     }
     return true;
    }


   	// 点击事件监听器
    public interface MenuListener {
        void onCenterCliched();
        }
参考：http://www.gcssloop.com/customview/touch-matrix-region
```



