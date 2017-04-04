---
title: 原型模式-下拉刷新的全局设置 ##文章标题
date: 2017-4-4 13:19:37
## updated: 2016-4-23 13:19:37
comments: true ##开启评论 false关闭
tags:
    - android

categories:
    - android
---


# 目的

全局替换刷新头部

`头部可拔插`:需要应用策略模式(组合与接口的方式set get)；
`全局替换`:就是原型模式 

>设置一个全局头部,那么每次使用的下拉刷新 都是全局头部的深拷贝对象。当然可以随意生成想要拷贝的特性；

<!-- more -->

# 范例

头部接口
```
public interface IHeaderView {

    中间省略.....
    /**
     * 全局更换头的配置
     * 主要是为了复制头部对象，复制想要复制的属性
     * 注意:返回null时候，默认就是新浪头了
     */
    IHeaderView clone_();

}
```

全局设置
```
       WaveHead waveHead=new WaveHead();
        waveHead.setResourceId(R.drawable.aaaaaaaaaaaab);
        Config.build()
                .setHeader(waveHead)
                .setFooter(new MeterialFooter())
                .setResistance(new Damping2Head8per())
                .writeLog(true)
                .perform();
```

# 实践项目：

https://github.com/luhaoaimama1/ZRefreshLayout

>欢迎Star 自恋的宣传一下 (*>∀<)ﾉ))★

![](https://ww2.sinaimg.cn/large/006tNbRwly1fbthspgg0qj31kw0xrdjz.jpg)

# 特点与功能实现

- [x] 支持添加下拉刷新与上拉加载
- [x] 支持所有的View:ImageView,FrameLayout,ListView, GridView, ScrollView, WebView...
- [x] 全局配置(头部配置都在这里,所以没有xml属性~),与独立更改
- [x] 自动刷新 延时刷新(如果刷新完毕有动画的话 需要用到延时刷新 ,[延时demo](https://github.com/luhaoaimama1/ZRefreshLayout/blob/master/app/src/main/java/zone/com/zrefreshlayoutdemo/header/CircleRefresh.java))
- [x] 头部是否固定
- [x] 刷新出发位置
- [x] 下拉位置映射，既resistance阻力
- [x] 因为是继承FrameLayout,可以添加多个child。(我没有控制必须一个,感觉更灵活吧)
