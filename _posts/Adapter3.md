---
title: ZAdapter3的分析与撸库 ##文章标题
date: 2017-4-29 00:19:37
## updated: 2017-3-14 13:19:37
comments: true ##开启评论 false关闭
tags:
    - adapter
categories:
    - android
---

# 分析时候的思路

* adapter里面的viewhold可以被复用
> 所以需要重写一个方法通过位置获取layoutStyle，addviewHolder顺便传递一个layoutStyle,也可不传为默认值；
* layoutID要在viewhold的类方法中,而不是构造器中 
>   因为resID与viewHold本就是一个整体。方法构造中的话表示resID是可变的，并且看到这个viewhold类的时候布局文件看不到 容易蒙蔽
* helper类与ButterKnife的取舍?
> helper比ButterKnife好,原因:可定制封装逻辑，不强转，可连写,可通过泛型+继承+装饰模式达到扩展的目的；
* adapter 可扩展+链式调用  是用 泛型+继承 还是 同一个接口呢？
> 泛型+继承 :在IDEA中是可以的。但是AS中出现了bug。我已经向google提意见了,估计不会吊我~
> ![](http://ww2.sinaimg.cn/large/006tNc79gy1ff2v3z7mq0j31a20cewg6.jpg)
> 所以退而求其次 选择同一个接口

* 基础功能 
> addViewHold(ViewHolder)//为default
> addViewHold(style,viewHolder)
> addHeaderHold(ViewHolder/resId)
> addFooterHold(ViewHolder/resId)
> addEmptyHold(ViewHolder/resId)
> notify系列
> notifyEx系列(这里的位置对应为数据集的位置。包括header/footer)
> relaeList(rv) 故事的开始。很多逻辑可以在这里完成  

<!-- more -->

# 书写类型初稿：
Adapter
```
new Adatper(context,data){
	o.getStyle()<——可不写
}
.addViewHold(ViewHolder)//为default
.addViewHold(style,viewHolder)
.addFooterHold(ViewHolder/resId)
.addHeaderHold(ViewHolder/resId)
.addEmptyHold(ViewHolder/resId)//支持emptyHold
.add上啦Hold(ViewHolder)//貌似应该放入 zrfresh的逻辑中.  ZRefresh loadMore可以和他联动？
.notify系列//notify之间 自动计算动画？ 看看diffuitl工具
.relaeList(rv)
```

ViewHolder
```
ViewHolder
public itemView
int getLayoutId()
update(postion,data ,helper)
```

# 封装后的简单实用

## QuickRcvAdapter范例

```
 IAdapter<String> muliAdapter = new QuickRcvAdapter(this, mDatas){
                @Override
                protected int getItemViewType2(int dataPosition) {
                //此方法可以默认 设置不写, 默认值返回Wrapper.DEFAULT_VALUE(-1)
                    return dataPosition % 2;
                }
            }
            .addViewHolder(new LeftDelegates())//默认
            .addViewHolder(0, new LeftDelegates()) //多布局
            .addViewHolder(1, new RightDelegates())//多布局
            .addHeaderHolder(R.layout.header_simple)//资源
            .addHeaderHolder(ViewDelegates footer)//也可以的
            .addFooterHolder(R.layout.footer_simple)//资源
            .addFooterHolder(ViewDelegates footer)//也可以的
            .addEmptyHold(R.layout.empty)//也支持empty 资源
            .addEmptyHold(ViewDelegates emtpy)//也支持empty
            .setOnItemClickListener(new IAdapter.OnItemClickListener() {
                @Override
                public void onItemClick(ViewGroup parent, View view, int position) {
                    System.out.println("被点击->onItemClick" + position);
                }
            })
            .setOnItemLongClickListener(new IAdapter.OnItemLongClickListener() {
                @Override
                public boolean onItemLongClick(ViewGroup parent, View view, int position) {
                    System.out.println("被点击->onItemLongClick:" + position);
                    return true;
                }
            })
            .addOnScrollListener(scroller = new OnScrollRcvListenerEx(new OnScrollRcvListenerEx.LoadMoreCallback() {
                @Override
                public void loadMore() {
                //这个类主要是为了和我的ZRefresh库兼容。我会委托上啦加载在这里处理!
                }
            }))
            .relatedList(rv)
```

## ViewDelegates范例

```
public class LeftDelegates extends ViewDelegates<String> {

    @Override
    public int getLayoutId() {
        return R.layout.item_left;
    }

    @Override
    public void fillData(int postion, String data, Helper helper) {
        ExtraHelper.wrapper(helper).setText(R.id.tv, data).heihei().heihei2()
                .setOnClickListener(new View.OnClickListener() {
                    @Override
                    public void onClick(View view) {
                        System.out.println("helper click测试 ");
                    }
                }, new int[]{R.id.tv, R.id.ll_main});
        ;
    }
}
```
     
## Helper的扩展技巧:扩展技巧是 装饰模式+链式调用+泛型

> 使用起来:ExtraHelper.wrapper(helper).setText(R.id.tv, data).heihei().heihei2();

实现方式：

```
public class ExtraHelper extends Helper<ExtraHelper> {


    private ExtraHelper(Helper helper) {
        super(helper.getContext(), helper.getHolder(),helper.getAdapter());
        child = this;
    }

    public static ExtraHelper wrapper(Helper helper){
        return new ExtraHelper(helper);
    }

    public ExtraHelper heihei() {
        System.out.println("heihei!");
        checkChild();
        return child;
    }

    public ExtraHelper heihei2() {
        System.out.println("heihei2!");
        checkChild();
        return child;
    }
}
```

## 全局配置

> 新裤暂时就支持这点~ 
> (原型模式-下拉刷新的全局设置)[https://luhaoaimama1.github.io/2017/04/04/Refresh/]

```
//全局替换加载更多
QuickConfig.build().setLoadMoreDelegates(ILoadMoreDelegates iLoadMoreDelegates).perform();
```

## 加载更多联动ZRefresh库

> 其实是ZRefresh把加载更多的功能委托出去而已

[参考范例](https://github.com/luhaoaimama1/ZAdapter3/blob/master/app/src/main/java/zone/com/zadapter3/Recycler2ZRefreshActivity.java)


# 最后建议看看

很美观一个库 https://github.com/GcsSloop/diycode

## 看完Diycode的总结：

* adapter重构(因为我原来写了adapter,看完这个库后推到重写adapter3)
headerFooterView功能
单独分离出一个类，既多类型复用。
数据并用helper定制图片加载等其他的逻辑
特点： 可复用，绑定数据

* 刷新控件与fragment的结合 ，布局结合 ？
这样有点耦合了  怎么才能 不耦合的处理？

* 刷新加载逻辑和网络请求 基类统一处理。  
> [有点类似我这个库,一直很少维护就是纠结啊,Ps:并且可以全局更换网络请求库](https://github.com/luhaoaimama1/Http_Rflist_Helper)

* 给我以灵感:网络请求 是 通过请求 异步+eventbus？
这样多个注册了Subscribe的页面同时存活，其中一个请求。另一个页面也会处理相同的数据，会不会凌乱呢？
> 不过这个确实很美观。我很喜欢 可以有一点缺憾,如果eventBus多支持一个方法,只在本类分发接受就好了~
> ![](http://ww4.sinaimg.cn/large/006tNc79gy1ff2vqpvwczj30eo07uwel.jpg)

* 工具类
DataCache，FileUtil，DataCleanManager，UrlUtil，ACache，CacheUtil，UUIDGenerator
SpeedyLinearLayoutManager

* 灵感，不错的方法
public void setOnClickListener(View.OnClickListener l, int... ids)

* 复杂的书写格式
![](http://ww1.sinaimg.cn/large/006tNc79gy1ff2wct9y99j30dc05gdg1.jpg)
非与或非的符号则用此格式
```
String a,
String b,
String c,
String d,
```

# Reference&Thanks：

https://github.com/GcsSloop/diycode
