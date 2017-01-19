---
title: Android-打造无敌解耦的BaseActivity  ##文章标题
date: 2016-3-23 13:19:37
## updated: 2016-4-23 13:19:37
comments: true ##开启评论 false关闭
tags:
    - android

categories:
    - android
---
**封装库**:[*zone-sdk*](https://github.com/luhaoaimama1/zone-sdk)
**设计模式**:观察者模式
**目的**：解耦，各司其职。拍照等涉及onActivityResult的封装
<!-- more -->

# 逻辑图
![](https://ww4.sinaimg.cn/large/006tNbRwly1fbvwziewhij31kw0okgq0.jpg)

# BaseActivity

ActivityKinds(观察者的基类): 在使用的BaseActivity注册ActivityKinds或者移除不必要的ActivityKinds
>专注：`onCreate`，`onResume`，`onActivityResult`，`onPause`，`onDestroy`


| 名字 | 功能  |
| :------------: |:---------------:| 
| CollectionActivityKind  | 收集所有Activity用的 |  
| FeaturesKind  |  [专门处理关于onActivityResult的](#featuresKind) | 
| ScreenSettingKind  | setNoTitle,setFullScreen的 | 
| SwipeBackKind  | 左滑退出Activity用的 集成别的项目~ [xml额外的配置,参考地址](https://github.com/Jude95/SwipeBackHelper/blob/master/README_ch.md)|

>考虑兼容(简称BaseActivity)分成三块:`BaseActivity` `BaseAppCompatActivity` `BaseFragmentActivity`

## BaseActivity里的主要工作

| 名字 | 功能  |
| :------------: |:---------------:|
| updateKinds  | 在这里 移除与注册 ActivityKinds | 
 

##  ActivityKinds范例：
```
 public class StatKind extends ActivityKinds {
     @Override
     public void onResume() {
         super.onResume();
         //百度统计
         StatService.onResume(activity);
         //jpush
         JpushUtils.Activity.resumePush(activity.getApplicationContext());
     }
 
     @Override
     public void onPause() {
         super.onPause();
         StatService.onPause(activity);
         JpushUtils.Activity.stopPush(activity.getApplicationContext());
     }
 }
```

## BaseActivity移除不必要的SwipeBackKind 功能,注册StatKind;
```
public abstract class DBaseAppCompatActivity extends BaseAppCompatActivity {

    protected void onCreate(Bundle arg0) {
        super.onCreate(arg0);
    }

    @Override
    public void updateKinds() {
        mKindControl.remove(SwipeBackKind.class);
        mKindControl.put(new StatKind());
    }
}
```

<a id="featuresKind"></a>

# FeaturesKind

观察者模式：

>ExtraFeature(观察者的基类):专注：`init（onResume中使用）`,`onActivityResult`,`destory`

>RequestCodeConfig(所有ExtraFeature的Code声明):其中的START_CODE(默认1000开始)可以修改,而进行整体code偏移 主要是怕碰到别的类似开源项目code重复导致不必要的问题;

| 名字 | 功能  |
| :------------: |:---------------:|
| Feature_Pic  | 拍照功能与相册选择功能(如果写建议参考这个) | 
| Feature_SystemClip  | 系统裁剪(demo 让我删除了.) | 
| Featrue_CustomCamera  | 自定义相机(demo 没有. 很久以前的了就不给demo了~) | 
> Tips:`返回值的方法要抽象`,这样就不会忘记写。 剩下的方法就可以public不抽象了~

## Activity中拍照的使用范例：
>声明后 注意 把拍照的功能添加到FeaturesKind里面 `mKindControl.get(FeaturesKind.class).addFeature(feature_Pic);`

```
     @Override
    public void updateKinds() {
        super.updateKinds();
        feature_Pic = new Feature_Pic(this) {
            @Override
            protected void getReturnedPicPath(String path) {
                System.out.println(path);
                Intent intent = new Intent(Photo_Shot_MainActivity.this,ShowPicActivity.class);
                Uri uri = Uri.parse(path);
                intent.setData(uri);
                startActivity(intent);
            }
        };
        mKindControl.get(FeaturesKind.class).addFeature(feature_Pic);
    }
```
>然后调用此方法就好了~ `feature_Pic.openCamera();` `feature_Pic.openPhotos();`

```
    @Override
    public void onClick(View v) {
        switch (v.getId()) {
        case R.id.shot:
            feature_Pic.openCamera();
            break;
        case R.id.photo:
            feature_Pic.openPhotos();
            break;
        }
    }
```
