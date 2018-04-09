---
title: Java8函数式编程读书总结 ##文章标题
date: 2017-5-5 12:40:37
## updated: 2017-3-14 13:19:37
comments: true ##开启评论 false关闭
tags:
categories:
---



# Lambda表达式

* Lambda表达式的类型:函数接口是`只有一个抽象`方法的接口
* @FunctionalInterface:是否仅仅一个抽象方法！不然编译错误，主要为了lambda提供方便;
    > 有些多抽象方法的接口不必使用此注解！
* interface 
    * default(`注意有方法体!`)：符合多态 子类如果覆盖,既用自己的行为；
        > 事件原因：Java8中Collection接口增加stream方法，意味所有子类都需要增加。
    * static default：未构造即可使用,一般用于构造 例如Stream.rang()方法构造流；

<!-- more -->

## 快捷键提示
![](https://ww4.sinaimg.cn/large/006tNbRwgy1ffbnfxt95bj30zk0j4wgl.jpg)

## lambda简单使用 

> Tips:Lambda中的引用值是既成事实的final而不是变量。如果用的变量的话。编译器会告诉你的

```
    //无参数
    Runnable runnable = () -> log();
    
    //单个参数
    Predicate<String> p=o -> o.isEmpty();
    
    //两个参数 不能省略括号！
    BiConsumer<String,String> biFunction=(o, o2) -> log();
    
    //有返回值的Lambda.一句逻辑既是的时候可以省略return!
    Callback callback = a -> 1==1?"Lambda":"Java8";
    
    public interface Callback {
        String a(String a);
       }
```

## 方法引用

这种方法很普遍
```
artist -> artist.getName()
```
java8为其提供简化语法，叫做方法引用 去重用已有方法；
```
Artist:getName
```
标准语法为 Classname::methodName

> 凡是使用Lambda表达式的地方都可以使用方引用

特殊 构造
```
(name,age)-> new Artist(name,age)
Artist::new
数组的话
String[]::new
```

## 数组
```
    //老方法
    String[] array = new String[]{"hello", "world"};
    //lambda 数组新写法；根据上下文推断类型信息；
    String[] array2 = {"hello", "world"};
```

## default多重继承。碰到相同签名的方法

```
 public interface Juckbox{
        public default String rock(){
            return "...all over the world!";
        }
    }
    public interface Carriage{
        public default String rock(){
            return "...from side to side!";
        }
    }
    public class Child implements Juckbox,Carriage{

        @Override
        public String rock() {
            return Carriage.super.rock();
        }
    }
```

# Java8中重要的函数接口

> 英文翻译是有道的翻译~

| 接口 | 英文含义  | 函数描述符  | 示例  |
| :------------: |:---------------:| :---------------:| :---------------:| 
| Predicate&lt;T&gt;  | 断言 | T -> boolean | Predicate&lt;String&gt; p=o -> o.isEmpty() |
| Consumer&lt;T&gt;  | 消费  |  T -> void |  Consumer&lt;String&gt; consumer=s -> log(); |
| Function&lt;T&gt;  | 功能/函数  |  T -> R | 转换成另一个对象:Function&lt;String,File&gt; f= s -> new File(s); |
| Supplier&lt;T&gt;  | 功能  |  None -> T | 供应者:Supplier&lt;String&gt; s=() -> "heihei"; |
| UnaryOperator&lt;T&gt;  | 一元运算  |  T -> T |  UnaryOperator&lt;String&gt; s=s1 -> s1+"," ;  |
| BinaryOperator&lt;T&gt;  | 二元运算  |  T,T -> T | BinaryOperator&lt;String&gt; a=(s, s2) -> s+s2;） |


## Bi系列
BiConsumer,BiFunction,BiPredicate:从一个参数值,扩展支持到两个参数；
范例：
```
@FunctionalInterface
public interface BiConsumer<T, U>{
 void accept(T t, U u);
}
```

##  基本类型系列
为了减小装箱拆箱而消耗的性能；

> Double/Int/Long 简称 *DIL*

* *DIL*XXX：代表参数值泛型
* To*DIL*XXX：代表返回值泛型
* *DIL*To*DIL*XXX：To前面代表参数值泛型 ,To后边代表返回值泛型
* Obj*DIL*Consumer(`特殊`) :相当于BiConsumer, 代表的第二个参数类型的泛型

范例：
```
@FunctionalInterface
public interface LongToIntFunction {
    /**
     * Applies this function to the given argument.
     * @param value the function argument
     * @return the function result
     */
    int applyAsInt(long value);
}

```

# 流

> Double/Int/Long 简称 *DIL*

集合stream的方法简介；

```
  Arrays.asList("a","b").stream();
  new HashSet<>().stream();
  new HashMap<>().entrySet().stream()
```

## Stream终结符 
[官方演示范例](http://www.concretepage.com/java/jdk-8/)

[演示范例](https://github.com/luhaoaimama1/Java8Study/blob/master/app/src/main/java/zone/com/java8study/java8/test/EndSymbolTest.java)

| 方法名 | 类型  | return | 函数接口  | 功能  |
| :------------: |:---------------:| :---------------:| :---------------:| :---------------:|
| all/any/none Match  | 终结符 | boolean | Predicate&lt;T&gt;  | 筛选 |
| count  | 终结符 | long | None | 同list.size() | 
| max/min  | 终结符 | Optional&lt;T&gt; | Comparator&lt;T&gt;  | 返回一个最大/最小元素 | 
| findFirst  | 终结符 | Optional&lt;T&gt; | None | 返回流中第一个元素 | 
| findAny  | 终结符 | Optional&lt;T&gt; | None | 返回流中任意元素<br /> `实测还是第一个元素` | 
| collect  | 终结符 | R | Collector&lt;T, A, R&gt; | 流中的元素收集到R中 | 
| collect  | 终结符 | R |Supplier&lt;R&gt; supplier,<br />BiConsumer&lt;R,T&gt; ,<br />BiConsumer&lt;R, R&gt; | 流中的元素收集到R中 |
| reduce  | 终结符 | Optional&lt;T&gt; |BinaryOperator&lt;T&gt;  | 累加运算: 求和,求积等 | 
| reduce  | 终结符 | Optional&lt;T&gt; |T identity,<br />BinaryOperator&lt;T&gt;  | 累加运算: 求和,求积等 | 
| reduce  | 终结符 | U |U,<br />BiFunction&lt;U,T, U&gt;,<br />BinaryOperator&lt;U&gt;  | 累加运算: 求和,求积等 | 
| forEach  | 终结符 | void | Consumer&lt;T&gt; | 遍历流中元素 |
| forEachOrdered  | 终结符 | void | Consumer&lt;T&gt; | 顺序遍历流中元素 | 
| isParallel  | 终结符 | void | None| 是并行？ |
| close  | 终结符 | void | None  | 手动关闭流 | 
| spliterator  | 终结符 | Spliterator&lt;T&gt; | None  | ？？？ | 
| toArray  | 起始符 | Object/A[] | None/IntFunction&lt;A[]&gt;| 流转换成数组 |

## Stream中间符 
[演示范例](https://github.com/luhaoaimama1/Java8Study/blob/master/app/src/main/java/zone/com/java8study/java8/test/CenterSymbolTest.java)

| 方法名 | 类型  | return  | 函数接口  | 功能  | 
| :------------: |:---------------:| :---------------:| :---------------:| :---------------:|   
| filter  | 中间符 | Stream&lt;T&gt; | Predicate&lt;T&gt;  | 筛选 | 
| peek  | 中间符 | Stream&lt;T&gt; | Consumer&lt;T&gt;   | 查看流元素 | 
| distinct  | 中间符 | Stream&lt;T&gt; | None | 去掉重复元素 | 
| skip  | 中间符 | Stream&lt;T&gt; | long | 扔掉前面n个元素 | 
| limit  | 中间符 | Stream&lt;T&gt; | long | 返回不超过跟定长度n的流 | 
| map  | 中间符 | Stream&lt;T&gt; | Function&lt;T,R&gt; | 原来的流转换成新流:方形过渡到圆形 |
| mapTo*DIL*  | 中间符 | *DIL*Stream | To*DIL*Function&lt;T&gt; |  | 
| flatMap  | 中间符 | Stream&lt;T&gt; | Function&lt;T,R&gt;  | 合并流 |
| flatMapTo*DIL*  | 中间符 | *DIL* Stream | To*DIL*Function&lt;T&gt; |  | 
| sort  | 中间符 | Stream&lt;T&gt; | Comparator&lt;T&gt;/None  | 排序 | 
| sequential  | 中间符 | Stream&lt;T&gt; | None  | 串行 |
| parallel  | 中间符 | Stream&lt;T&gt; | None  | 并行 | 
| unordered  | 中间符 | Stream&lt;T&gt; | None  | 返回一个无序的流<br />`实际测试无效` | 
| onClose  | 中间符 | Stream&lt;T&gt; | Runnable  | 流关闭会执行这个任务 |

> parallel 不一定并行就是最快的。与数据还有多核处理器有关. 例如(本意是并行不一定快的。范例仅仅是个大概 请当看着玩)：如果处理10个元素，串行代码速度是并行的8倍。如果数量增至100则速度相等。如果增至10000 则并行是串行的2.5倍；

## Stream静态起始符

[演示范例](https://github.com/luhaoaimama1/Java8Study/blob/master/app/src/main/java/zone/com/java8study/java8/test/StartSymbolTest.java)

| 方法名 | 类型  | return  | 函数接口  | 功能  | 
| :------------: |:---------------:| :---------------:| :---------------:| :---------------:|  
| concat  | 起始符 | Stream&lt;T&gt; | Stream&lt;T&gt; a, Stream&lt;T&gt; b | 合并流 |
| generate  | 起始符 |Stream&lt;T&gt; | Supplier&lt;T&gt;  | while循环 | 
| iterate  | 起始符 | Stream&lt;T&gt; | final T , final UnaryOperator&lt;T&gt;  | while循环+累加器 | 
| of  | 起始符 | Stream&lt;T&gt; | T/T... | 转成流 | 
| empty  | 起始符 | Stream&lt;T&gt; | None  | 返回一个空的流 | 
| builder  | 起始符 |  Builder &lt;T&gt;  | None  | 通过add.build一个流 | 

## IntStream多出来的方法

### IntStream  中间符/终结符

> DoubleStream,LongStream与之相同；

[演示范例](https://github.com/luhaoaimama1/Java8Study/blob/master/app/src/main/java/zone/com/java8study/java8/test/dil/IntExtraTest.java)

| 方法名 | 类型  | return  | 函数接口  | 功能  |
| :------------: |:---------------:| :---------------:| :---------------:| :---------------:|  
| asDoubleStream  | 中间符 | DoubleStream | None  | 转换成DoubleStream | 
| asLongStream  | 中间符 | LongStream | None  | 转换成LongStream | 
| boxed  | 中间符 | Stream:Integer | None  | 装箱操作,int编程Integer| 
| average | 终结符 | OptionalDouble  | None  | 平均值 |
| sum | 终结符 | int  | None  | 求和 | 
| summaryStatistics | 终结符 | IntSummaryStatistics  | None  | 简易的统计 |

### IntStream 静态起始符

| 方法名 | 类型  | return  | 函数接口  | 功能  | 
| :------------: |:---------------:| :---------------:| :---------------:| :---------------:|   
|  range | 起始符 | IntStream  | int startInclusive, int endExclusive  | 不包括后边界 |
|  rangeClosed | 起始符 | IntStream  | int startInclusive, int endExclusive  |  包括后边界  |

例子:

```
IntStream.range(1,3)//流为:1,2
IntStream.rangeClosed(1,3)//流为:1,2,3
```

### IntSummaryStatistics简介

* IntSummaryStatistics（ Summary:简易的 Statistics：统计）
    * accept(int） 添加一个值
    * combine(IntSummaryStatistics)与另一个合并成一个
    * getAverage 平均数
    * getCount 元素的个数
    * getSum 求和
    * getMin 最小的值
    * getMax 最大的值
    * toString 
    
    
# 收集器
Steam类会为你自动挑选合适的类型；
```
 Stream.of("a","b","c").collect(Collectors.toSet());
```
如果你想制定类型用toCollection他接受一个参数来创建集合
```
Stream.of("a","b","c").collect(Collectors.toCollection(TreeSet::new));
```


Collectors 

* Collector<T, A, R>
    * T 流中的元素类型
    * A 中间类型
    * R Stream.collect返回的类型
    
[演示范例](https://github.com/luhaoaimama1/Java8Study/blob/master/app/src/main/java/zone/com/java8study/java8/collectors/SymbolTest.java)

| 方法名 | return  | 函数接口  | 功能  | 
| :------------: | :---------------:| :---------------:| :---------------:|
|  counting |  Collector&lt;T, ?, Long&gt;  | None | 同list.size() |
|  max/minBy | Collector&lt;T, ?, Optional&lt;T&gt;&gt;  | Comparator&lt;T&gt;| 对比器中最大/小元素 |
|  averaging*DIL* |  Collector&lt;T, ?, *DIL*&gt;  | To*DIL*Function&gt;T&gt;  | 平均值 |
|  summing*DIL* |  Collector&lt;T, ?, *DIL*&gt;  | To*DIL*Function&lt;T&gt;  | 求和 |
|  summarizing*DIL* |  Collector&lt;T, ?, *DIL*SummaryStatistics&gt;  | To*DIL*Function&lt;T&gt;  | 简易的统计 |
|  collectingAndThen |  Collector&lt;T,A,RR&gt;  | Collector&lt;T,A,R&gt;,<br />Function&lt;R,RR&gt; | 收集之后装起来 |
|  mapping |  Collector&lt;T, ?, R&gt;  | Function&lt;T,U&gt;,<br />Collector&lt;U, A, R&gt;  | 原来的流转换成新流:方形过渡到圆形 |
|  toCollection|  Collector&lt;T, ?, C&gt;  | Supplier&lt;C&gt;  | 制定一个集合去装载进来的流元素 |
|  toList|  Collector&lt;T, ?,  List&lt;T&gt;&gt;  | None  | 用List去收集,<br />Array/Linked等会选优 |
|  toSet|  Collector&lt;T, ?,  Set&lt;T&gt;&gt;  | None  | 用Set去收集,<br />Hash/Tree等会选优 |
|  toMap<br />toConcurrentMap|  Collector&lt;T, ?, Map&lt;K,U&gt;&gt;  | Function&lt;T, K&gt;,<br />Function&lt;T,U&gt;  | Hash/ConcurrentMap去收集 |
|  joining |  Collector&lt;CharSequence, ?, String&gt;  | None | 流连起来 |
|  joining |  Collector&lt;CharSequence, ?, String&gt;  | CharSequence delimiter | 流连起来<br />delimiter:连接符 |
|  joining |  Collector&lt;CharSequence, ?, String&gt;  | CharSequence delimiter,<br />CharSequence prefix,<br />CharSequence suffix | 流连起来<br />delimiter:连接符 <br />prefix:开始字符<br />suffix:结尾字符 |
|  groupingBy<br />groupingByConcurrent |  Collector&lt;T, ?, Map&lt;K, List&lt;T&gt;&gt;&gt;  | Function&lt;T, K&gt; | 类型分组 |
|  groupingBy<br />groupingByConcurrent |  Collector&lt;T, ?, Map&lt;K, D&lt;T&gt;&gt;&gt;  | Function&lt;T, K&gt;,<br />Collector&lt;T, A, D&gt; | 类型分组 |
|  groupingBy<br />groupingByConcurrent |  Collector&lt;T, ?, M &gt; | Function&lt;T, K&gt;,<br />Supplier&lt;M&gt;,<br />Collector&lt;T, A, D&gt; | 类型分组 |
|  partitioningBy |   Collector&lt;T, ?, Map&lt;Boolean, List&lt;T&gt;&gt;&gt;  | Predicate&lt;T&gt; | groupingBy的简化<br />仅有两种分组类型true,false |
|  partitioningBy |   Collector&lt;T, ?, Map&lt;Boolean, D&gt;&gt;  | Predicate&lt;T&gt;,<br />Collector&lt;T, A, D&gt;  | groupingBy的简化<br />仅有两种分组类型true,false |
|  reducing |   Collector&lt;T, ?, Optional&lt;T&gt;&gt;  | BinaryOperator&lt;T&gt;  | 累加器 |
|  reducing |   Collector&lt;T, ?, T&gt;  | T identity,<br />BinaryOperator&lt;T&gt;  | 累加器 |
|  reducing |   Collector&lt;T, ?, U&gt;  | T identity,<br />Function&lt;T, U&gt; ,<br />BinaryOperator&lt;T&gt  | 累加器<br />`这个就不需要并行了请注意` |

> groupingBy等方法内包含Collector参数的含义，因为这个设计的时候就是不能连用只能通过传递参数更改泛型之类的。外部的collector会传递到新的collector中;

## 自定义收集器

[演示范例](https://github.com/luhaoaimama1/Java8Study/blob/master/app/src/main/java/zone/com/java8study/java8/collectors/StringCollector.java)


# Reference&Thanks：

http://tangpj.com/2017/04/24/java8-inAndroid/

http://www.jianshu.com/p/c0d5c3094324

