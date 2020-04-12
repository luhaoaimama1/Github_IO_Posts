---
title: introduce
comments: true ##开启评论 false关闭
reward: false
date: 2016-12-30 10:41:59
---
# Github项目介绍(按顺序 从好到一般)
> 好坏是我认为的 ~

|                             名字                             |                             功能                             |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
| [RetrofitListHelper](https://github.com/luhaoaimama1/RetrofitListHelper) | `网络请求库 支持上传下载 https cookies等` `支持rxjava2` `可以与dialog pop view等进行请求关联` `firstLoad那种 网络状态关联` `一个下拉刷新与上拉加载自动完成` |
|     [zone-sdk](https://github.com/luhaoaimama1/zone-sdk)     |            所有基础工具类 收集与封装 与demo测试;             |
|     [ZAnimate](https://github.com/luhaoaimama1/ZAnimate)     | [2d库](https://github.com/luhaoaimama1/ZAnimate):更改androidnineold为链式调用,参数优化,预设动画的支持 [3d库](https://github.com/luhaoaimama1/ZAnimate/blob/master/README-3D.md):理解方式遵循ae的图层 |
| [ZRefreshLayout](https://github.com/luhaoaimama1/ZRefreshLayout) |    一个可以`全局`随意`配置头部`的`下拉刷新`与`上拉加载`库    |
|    [ZAdapter3KT](https://github.com/luhaoaimama1/ZAdapter3KT)    | `实体类 配置额外功能` `支持断头吸附` `Holder类的链式调用` `支持空数据布局 头部 尾部` `支持加载更多` `支持 非官方的divder(根据实体类自动更新)` `支持与ZRefresh联动` `支持细分复用 ,整体可见与不可以见的监听` `Holder类的 可扩展 参考zone.com.zadapter3kt.adapterimpl文件夹下` |
|        [Shine](https://github.com/luhaoaimama1/Shine)        | [与shimmer类似](https://github.com/facebook/shimmer-android); 多了个 bitmap加shine与倾斜角度 等等 |
|        [ZUnReadMessage](https://github.com/luhaoaimama1/ZUnReadMessage)        | `当某个树形节点收到消息，单链表不行 因为第二层可能是多个。 逆向迭代 弱引用 监听的回调`  `UnReadTree 一次性配置。结构清晰。 相比 activity配置中好太多` |
|      [Zbanner](https://github.com/luhaoaimama1/Zbanner)      |  可无限循环,亦可不无限循环,定时。兼容。包含指示器(简单的);   |
| [APT 与动态代理](https://github.com/luhaoaimama1/AnnotationStudy/blob/master/README.md) |   `已封装`,增有ReadMe告诉如何学习APT,同时demo包含动态代理    |
| [5.0动画的学习与封装](https://github.com/luhaoaimama1/TransitionStudy) | Transition类的研究与封装,与5.0事物动画(list嵌套等)的实现与注意; |
|   [Java8Study](https://github.com/luhaoaimama1/Java8Study)   |                  rxjava2与java8的学习与测试                  |
| [Gesture类的学习与扩展](https://github.com/luhaoaimama1/GestureStudy) | `位移,缩放,旋转` 都可以得到的手势支持 (zone-sdk中已经包含了) |
|      [okhttp](https://github.com/luhaoaimama1/ZOkHttp)       | 网络请求库;支持 `get` `post` `post file` `cookie` `https`等功能 |
|        [ZView](https://github.com/luhaoaimama1/ZView)        | 目的:为了搜集 常用的 小型的 稳定的 自定义View Ps:虽然暂时还很小~ 但是架不住收集的时间长啊 |
| [Http_Rflist_Helper](https://github.com/luhaoaimama1/Http_Rflist_Helper/blob/master/README-cn.md) | 一个可以把`网络请求`和`pullview关联`，从而不需要处理`翻页``上拉下拉的动画处理`与`数据更新`问题 ,并且可以`全局切换网络请求库` |
| [如果有人对json解析实体类感兴趣,请看这个项目](https://github.com/luhaoaimama1/JsonParser) | 仅仅解析到string没有匹配其他类型~ 老项目(GBK编码 GUI项目) 命名什么的不要吐槽了~ 写的很坑不过理解的话应该还可以 |
|     [JavaZone](https://github.com/luhaoaimama1/JavaZone)     |             java测试类库主要是调研用 没啥用的说              |

# AS插件开发项目

|                             名字                             |         功能         |                             痛点                             |
| :----------------------------------------------------------: | :------------------: | :----------------------------------------------------------: |
| [快速生成六种单例](https://github.com/luhaoaimama1/SingletonTest) | 快速生成六种单例模式 |   单例模式无法继承,导致每次都需要写 很费劲。并且是固定写法   |
|    [空格检测](https://github.com/luhaoaimama1/SpaceTest2)    | 检查字符串是否有空格 | 当一个类url常量过多,不小心开头弄出空格,还好使,但是cache缓存方面却失常的问题… |

# Python

|                             名字                             |                             功能                             |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
| [ZonePython](https://github.com/luhaoaimama1/ZonePython) | 工作中遇到的一些繁琐事情的快速处理工具 |