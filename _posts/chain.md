---
title: 继承与链式调用的结合 ##文章标题
date: 2017-4-29 01:40:37
## updated: 2017-3-14 13:19:37
comments: true ##开启评论 false关闭
tags:
categories:
---

# 父类:返回值是泛型；

```
public class Helper<T extends Helper> {
     protected T child;
     public Helper() {
            child = (T) this;
        }
     public T setText(@IdRes int viewId,String textStr) {
            ...
            checkChild();
            return child;
        }
     protected void checkChild() {
            if (child == null)
                throw new IllegalStateException("child must be set in child's Constructor!" +
                        "not method:initDefaultValueAnimator");
        }
}
```

<!-- more -->

# 子类:制定泛型 + 继承父类

* 光继承，就已经包含扩展能力了。
* warpper方法其实装饰器模式
> 用到的地方:例如一个方法的参数是Helper类型，并且无法改变参数的类型。继而用装饰器扩展其功能

```
public class ExtraHelper extends Helper<ExtraHelper> {


    private ExtraHelper(Helper helper) {
        super();
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

# 最后的使用

```
 ExtraHelper.wrapper(helper).setText(R.id.tv, data).heihei().heihei2()
```

[Demo](https://github.com/luhaoaimama1/ZAdapter3/blob/master/app/src/main/java/zone/com/zadapter3/helper/ExtraHelper.java)

