---
title: 深入理解Java虚拟机总结---类初始化过程 ##文章标题
date: 2016-12-7 13:19:37
## updated: 2016-12-29 13:19:37
comments: true ##开启评论 false关闭
tags:
    - jvm

categories:
    - jvm
---

<!-- more -->

# 类的初始化过程
![](http://images0.cnblogs.com/blog2015/544748/201505/251937420687138.jpg)

## 非法向前引用

编译器手机的顺序是由语句在源文件中出现的顺序决定的,静态语句块中只能访问到定义在静态语句之前的变量,定义它之后的变量,可以赋值,但不能访问

```
public class Test{
    static{
         i=0;
         system.out.print(i);//非法向前引用
    }
    static int i=1;
}
```
><clinit>(类构造器方法):在jvm第一次加载class文件时调用,如果类或者接口没有静态语句块,也没有对变量的赋值,那么编译器可以不为这个类生成<clinit>方法并且他被加锁了,既不能做耗时操作。

>Tips:如果在此方法中耗时很长,就可能造成多个进程阻塞;

## 类加载的时机
* 加载
>加载与连接阶段的验证动作是交叉进行的
* 连接
    * 验证
        * 文件格式验证。是否符合Class文件格式的规范
        * 语义分析。父类,抽象类,接口等。
        * 字节码验证
        * 符号引用验证  
    * 准备
    > 正式为类变量分配内存并设置类变量初始值的阶段
    ```
    public static int value=123;
    //final的话  准备阶段既123;
    //非常量的 static 则准备阶段是0;<clinit>类构造方法执行才会变成123
    ```
    * 解析
    >可选的,loadClass第二个参数来判定是否需要解释。这里的解释是根据勒种的符号引用查找相应的实体,在把符号引用替换成一个直接引用的过程。
* 初始化
* 使用
* 卸载

## 类什么时候才被初始化
* 只有这6中情况才会导致类的类的初始化
    * 创建类的实例，也就是new一个对象
    * 访问某个类或接口的静态变量，或者对该静态变量赋值
    * 调用类的静态方法
    * 反射（Class.forName("com.lyj.load")）
    * 初始化一个类的子类（会首先初始化子类的父类）
    * JVM启动时标明的启动类，即文件名和类名相同的那个类
    
* 所有引用类的方法都不会触发初始化,称为被动引用。
>类引用父类的静态字段,不会导致该类被初始化

* 类的初始化步骤：
    * 如果这个类还没有被加载和链接，那先进行加载和链接
    * 假如这个类存在直接父类，并且这个类还没有被初始化（注意：在一个类加载器中，类只能初始化一次），那就初始化直接的父类（不适用于接口）
    * 加入类中存在初始化语句（如static变量和static块），那就依次执行这些初始化语句。

## 双亲委派模型
Java虚拟器角度仅仅有两种不同的类加载器:

一种启动类加载器(Bootstrap ClassLoader):C++语言实现是虚拟器自身的一部分;

另一种是所有其他的类加载器(java语言,JVM之外 继承ClassLoader)
>更详细:

!![](https://ww4.sinaimg.cn/large/006tKfTcgw1fb917dq0h0j30hs0cd775.jpg)

Bootstrap ClassLoader:负责加载$JAVA_HOME中jre/lib/rt.jar里所有的class既核心API(ExtClassLoader和AppClassLoader也在此时被加载)，由C++实现，不是ClassLoader子类

Extension ClassLoader:负责加载java平台中扩展功能的一些jar包，包括$JAVA_HOME中jre/lib/*.jar或-Djava.ext.dirs指定目录下的jar包

App ClassLoader:负责记载classpath中指定的jar包及目录中class.可以通过getSystemClassLoader()方法获得

Custom ClassLoader:属于应用程序根据自身需要自定义的ClassLoader，如tomcat、jboss都会根据j2ee规范自行实现ClassLoader
>加载过程中会先检查类是否被已加载，检查顺序是自底向上，从Custom ClassLoader到BootStrap ClassLoader逐层检查，只要某个classloader已加载就视为已加载此类，保证此类只所有ClassLoader加载一次。而加载的顺序是自顶向下，也就是由上层来逐层尝试加载此类。

### 为什么这么设计?

类加载器:任何一个类都需要加载它的类加载器和这个类一同确立其在java虚拟机唯一性。每个类加载器都有类名称空间。
>两个类是否相同,是由同一个类加载器为前提下才有意义.相同是指equals、instanceof isAssignalbeFrom isIntance等;

例如类java.lang.Object,他存放在rt.jar中,无论哪个类加载器加载这个类,最终都是委派给魔性最顶端的启动类加载器进行加载。因此Object类在程序的各种类加载器环境中都是**同一个类**。
相反如果没有使用,各个类加载自行加载的话。那么系统将出现多个不同的Object类,那么java类型体系中最基本的行为也无法保证;


以下是ClassLoader的源码,实现很简单
```
rotected synchronized Class<?> loadClass(String name, boolean resolve)
	throws ClassNotFoundException
    {
	// First, check if the class has already been loaded
	Class c = findLoadedClass(name);
	if (c == null) {
	    try {
		if (parent != null) {
		    //从父加载器加载
		    c = parent.loadClass(name, false);
		} else {
		    //从bootstrap loader加载
		    c = findBootstrapClassOrNull(name);
		}
	    } catch (ClassNotFoundException e) {
                // ClassNotFoundException thrown if class not found
                // from the non-null parent class loader
            }
            if (c == null) {
	        // If still not found, then invoke findClass in order
	        // to find the class.
	        c = findClass(name);
	    }
	}
	if (resolve) {
	    resolveClass(c);
	}
	return c;
    }
```



* 其他思考的问题    
    * 类加载的逻辑应该在哪里写?
    >jdk 1.2之后 已经不提倡用户覆盖loadClass方法了,而是应当在自己的类加载逻辑写到findClass()中。因为loadClass()方法如果加载失败就会调用自己的findClass()去加载。这样就能保证 写出来的类加载器是符合双亲委派模式的
    * 如果依赖特定的扩展包,需要继承特定的classLoader吗?
    > 下面的默认构造器, 要在父亲是AppClassLoader的基础上 加载自己的类。不然会破坏双亲的
    > 总结:新的ClassLoader需要旧的去委托。如果不这样就会导致在同一个类出现在不同的ClassLoader中。
    ```
     protected ClassLoader() {
        //getSystemClassLoader()其实就是AppClassLoader
        this(checkCreateClassLoader(), getSystemClassLoader());
    }
    ```
    * 如果类已经 加载过了,那么应该在哪里存储 下一次去验证是否加载过呢?
    >可以通过该ClassLoader中 `protect` findLoadedClass(name)方法找到。
    * resolveClass 什么时候使用
    
* 类加载过程总结
    * 得到类的原始字节数组byte[]。
    * findClass里通过defineClass(name, buf, 0, buf.length) 完成类加载。    

### 举例分析流程
如果一个非ClassPath目录下的新的数据流类通过新的ClassLoader(NewClassLoader)去加载。

Parent:
> NewClassLoader-AppClassLoader->ExtClassLoader

第一次初始化
>loadClass:每次findLoadedClass都找不到

>NewClassLoader-AppClassLoader->ExtClassLoader

findClass:(loadClass的逆序)
>ExtClassLoader->AppClassLoader->NewClassLoader(最后在此define 生成类后loadClass一次)

第二次初始化
>loadClass:第一个NewClassLoader中findLoadedClass就找到了

NB技巧:子类可以公开父类中的protected的方法;

```
public void findClass_(){
    super.findClass();//protected
}
```

[Demo Code](https://github.com/luhaoaimama1/JavaZone/blob/master/JavaTest_Zone/src/classload/ClassLoaderTest.java)