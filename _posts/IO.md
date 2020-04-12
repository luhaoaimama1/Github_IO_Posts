---
title: 了解Java中的I/O系统 ##文章标题
date: 2017-3-14 20:19:37
## updated: 2017-3-14 13:19:37
comments: true ##开启评论 false关闭
tags:
    - I/O

categories:
    - 源码分析
    - android
    - I/O
---

# 概览
![](http://ww4.sinaimg.cn/large/006tKfTcgy1fdmkir64xbj30xi0yrt9r.jpg)
[图片项目地址 可以复制下来 进行修改](https://www.processon.com/view/link/58c7cef4e4b0337115d577a9)

[IO's Demos Code](https://github.com/luhaoaimama1/JavaZone/tree/master/JavaTest_Zone/src/a%E9%9D%A2%E8%AF%95/io)

<!-- more -->

> 编码乱码原因: 机器中的编码都是 二进制编码 显示的时候 为了让人看懂 从而设置了各种字符集 ,如果与原来的编码不一致则会导致看不懂既乱码 。机器的二进制码没有变化 ,仅仅是 对照的字符集 不一致 导致的

# 字符流和字节流

> 字符流的由来： 因为数据编码的不同，而有了对字符进行高效操作的流对象。本质其实就是基于字节流读取时，去查了指定的码表。 字节流和字符流的区别：

*   读写单位不同
    *   字节流以字节（8bit）为单位，字符流以字符为单位，根据码表映射字符，一次可能读多个字节。
*   处理对象不同
    *   字节流能处理所有类型的数据（如图片、avi等），而字符流只能处理字符类型的数据。Java中字符是采用Unicode标准，一个字符是16位，即一个字符使用两个字节来表示。为此，JAVA中引入了处理字符的流 

> 结论：只要是处理纯文本数据，就优先考虑使用字符流。 除此之外都使用字节流。

# 常用的处理流类型

该类型是对一个已存在的流的连接和封装，通过所封装的流的功能调用实现数据读写，处理流的构造方法总是要带一个其他流对象作为参数，一个流对象进过其他流的多次包装，叫做流的链接。主要可以分为以下几种：

## 缓冲流（BufferedInPutStream/BufferedOutPutStream和BufferedWriter/BufferedReader）   

*   功能
    *  提高对流的操作效率 
    *  readLine()
     一次读一行，到行标记时，将行标记之前的字符数据作为字符串返回，当读到末尾时，返回null，其原理还是与缓冲区关联的流对象的read方法，只不过每一次读取到一个字符，先不进行具体操作，先进行临时储存，当读取到回车标记时，将临时容器中储存的数据一次性返回。

```
BufferedWriter bufw=new BufferedWriter(new FileWriter("buf.txt"));//读取缓冲区对象：        
BufferedReader bufr=new BufferedReader(new FileReader("buf.txt")); 
```


## 转换流（InputStreamReader/OutputStreamWriter）

该类型时字节流和字符流之间的桥梁，
*   功能
    * 该类型时字节流和字符流之间的桥梁
    该流对象中可以对读取到的字节数据进行指定编码的编码转换。

构造函数主要有：    
```
 InputStreamReader(InputStream);        //通过构造函数初始化，使用的是本系统默认的编码表GBK。  
 InputStreamWriter(InputStream,String charSet);   //通过该构造函数初始化，可以指定编码表。  
 OutputStreamWriter(OutputStream);      //通过该构造函数初始化，使用的是本系统默认的编码表GBK。  
 OutputStreamwriter(OutputStream,String charSet);   //通过该构造函数初始化，可以指定编码表。 
```

这里看下 FileReader类的构造器 
*   相当于 new InputStreamReader(new FileInputStream("a.txt"))
*   并且该对象使用的时默认的编码表
```
public class FileReader extends InputStreamReader {
 public FileReader(String fileName) throws FileNotFoundException {
        //相当于 new InputStreamReader(new FileInputStream("a.txt"));
        super(new FileInputStream(fileName));//该对象使用的时默认的编码表
    }
}
```

即如果a.txt中的文件中的字符数据是通过utf-8的形式编码，那么在读取时，就必须指定编码表，那么转换流时必须的。即：

``` 
 InputStreamReader isr=new InputStreamReader(new FileInputStream("a.txt"),utf-8);
```

## 数据流（DataInputStream/DataOutputStream）

该数据流可以方便地对一些基本类型数据进行直接的存储和读取，不需要再进一步进行转换，通常只要操作基本数据类型的数据，就需要通过DataStream进行包装。

构造方法：        

```
DataInputStreamReader（InputStream）；  
 DataInputStreamWriter（OutputStream）
```
方法举例：          
```
int readInt()；//一次读取四个字节，并将其转成int值  
writeInt(int)；//一次写入四个字节，注意和write(int)不同，write(int)只将该整数的最低一个8位写入，剩余三个8为丢失  
hort readShort();  
writeShort(short);  
String readUTF();//按照utf-8修改版读取字符，注意，它只能读writeUTF()写入的字符数据。  
writeUTF(String);//按照utf-8修改版将字符数据进行存储，只能通过readUTF读取。 
...
```
注意：在使用数据流读/存数据的时候，需要有一定的顺序，即某个类型的数据先写入就必须先读出，服从先进先出的原则。

## 打印流（PrintStream/PrintWriter）

PrintStream是一个字节打印流，System.out对应的类型就是PrintStream

*   它的构造函数可以接受数据类型：
    *   字符串路径
    *   File对象 
    *   OutputStream

PrintStream是一个字符打印流

*   它的构造函数可以接受数据类型：
    *   字符串路径
    *   File对象
    *   OutputStream  
    *   Writer 

对于1、2类型的数据，可以指定编码表，也就是字符集，对于3、4类型的数据，可以指定自动刷新，当该自动刷新为True时，只有3个方法可以用：println,printf,format。 

## 对象流（ObjectInputStream/ObjectOutputStream）
该类型的流可以把类作为一个整体进行存取，主要方法有：
```
        Object readObject();该方法抛出异常：ClassNotFountException。

        void writeObject(Object)：被写入的对象必须实现一个接口：Serializable，否则就会抛出：NotSerializableException
```
        
# Reference&Thanks：

http://blog.csdn.net/zzp_403184692/article/details/8057693