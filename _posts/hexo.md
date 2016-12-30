---
title: Mac下Hexo搭建博客攻略  ##文章标题
date: 2016-6-2 13:19:37 
## updated: 2016-12-28 13:19:37
comments: true ##开启评论 false关闭
tags:  
    - hexo

categories: 
    - hexo    
---
[Hexo](https://hexo.io/zh-cn/)是基于`NodeJs`的静态博客框架，简单、轻量，其生成的静态网页可以托管在`Github`和`Heroku`上。

- 超快速度
- 支持MarkDown
- 一键部署
- 丰富的插件

下面以我的博客为例，luhaoaimama1.github.io

<!-- more -->
## 环境准备
#### 安装node.js
去[nodejs下载最新版本](https://nodejs.org/en/)下载对应系统的安装包，按提示安装。
检验安装成功：
```
$ node -v

```
#### 安装hexo
```
$ sudo npm install hexo-cli -g
```
如果遇到 npm: Permission denied,请先使用
```
npm config set unsafe-perm true
```
采用本地淘宝缘
```
sudo npm install -g hexo-cli --registry=https://registry.npm.taobao.org
```
>参考：[https://github.com/hexojs/hexo/issues/2223](https://github.com/hexojs/hexo/issues/2223)

然后进行本地安装
```
hexo init
```
安装好之后不要忘记执行
```
npm install
```
至此，就已经安装完毕了。是不是很简单呢？

##  部署一些常用命令
```
**hexo new"postName"** #新建文章

**hexo new page"pageName"** #新建页面

**hexo clean** #清理静态页面--缩写 hexo c

**hexo generate** #生成静态页面至public目录,缩写 hexo g

**hexo server** #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）,缩写 hexo s

**hexo deploy** #将.deploy目录部署到GitHub,缩写hexo d

**hexo help** # 查看帮助

**hexo version** #查看Hexo的版本
```
##  首先部署一篇页面显示

#### **添加文章**
```
hexo new "postName"
```
其中postName是博客名。

#### **生成静态页面**
```
hexo g
```

#### **本地启动**
执行好上面的命令之后就可以在本地启用服务来看效果了。执行下面的命令：

```
hexo s
```

看到 *INFO  Hexo is running at http://0.0.0.0:4000/. Press Ctrl+C to stop.* 之后，就可以在浏览器中打开页面[http://localhost:4000](http://localhost:4000)来看了。

## 上传至Github

#### 安装git部署插件

在部署之前，首先我们要确认在你的Github帐号的Repository中有 **用户名.github.io** 的项目。(这里项目必须是**以用户名开头**)
在确认之后，就可以执行命令
```
npm install hexo-deployer-git --save
```
来安装插件

####  配置 _config.yml 文件
在Hexo安装的目录，如 *~/Document/hexo* 中找到 **_config.yml** 文件。打开。
翻到最后，找到 **deploy** 字样，改成如下格式：
```
deploy: 
  type: git 
  repo: https://github.com/用户名/用户名.github.io.git 
  branch: master
```
>冒号后面有一个空格；使用github可以不用写branch那一行。
####  同步

```
hexo d
```
来执行。以后每次执行就可以依次输入下面三行命令：
>更改配置才需要 这些命令，如果仅仅更改文章内容和新建 **不需要从启服务器**
```
hexo c
hexo g
hexo s
```
如果想同步到github在
```
hexo d
```
需要输入用户名密码 完成同步后
>到这里就可以打开 bolg了https://luhaoaimama1.github.io/

##  更换主题
Hexo 中有很多主题，可以在[官网](https://hexo.io/themes/)查看。下面列举更换主题的一般套路：

#### 下载主题资源
```
$ cd /Users/fuzhipeng/themes
$ git clone https://github.com/tufu9441/hexo-blog themes/next
```
#### 应用下载的主题

在网站配置文件**_config.yml**中，配置**theme**

```
theme: next

```
>next是主题名称，具体的可查看主题的文档

#### 主题其他配置

可在`/theme/{theme}/_config.yml` 主题的配置文件下进行主题的配置。

接下来，可以执行万能的调试命令看看效果

```
$ hexo c
$ hexo g
$ hexo s
```

##我选择的主题

![](https://camo.githubusercontent.com/60dd124e56fa642adc6f8f125c79702db25cc48c/687474703a2f2f7777322e73696e61696d672e636e2f6c617267652f303036744e63373967773166616f6139746d37676e6a33313276306f723432312e6a7067)
>[主题地址](https://github.com/ahonn/hexo-theme-even)

###这里我总结下新手所遇到的问题

#### 更改中文

打开主题文件夹下的_config.yml ,添加

```
_language: zh-CN
```

如果其内容翻译的不满意可以到/themes/maupassant/languages下打开其对应语言的文件进行更改

#### 作为新手关于页面的文章应该在哪里？

```
$ hexo new page about
```

这个about是主题文件夹下的_config.yml

<img src="https://ww4.sinaimg.cn/large/006tNbRwgw1fb6xpz8l44j30ko0fijt6.jpg" width="400" >

最终生成在source目录下 一个**about**文件夹和一个**index.md**文件；

>index.md里的内容就是about了

#### 作为一个博客应该有评论，改如何添加

百度多玩 注册账号 然后进入**开发者中心的讨论区**   **右侧  插件下载 里的创建xxx**

创建后 把**duoshuo_shortname**填入 主题文件夹下的_config.yml 的 **duoduo字段**

>duoshuo_shortname 不是全称 ！！！ 

这时候还没有评论 需要在文章中 开启评论功能.

#### 范例：postName.md

```
---
title: postName  ##文章标题
date: 2016-12-28 13:19:37
updated: 2016-12-28 13:19:37
comments: true  ##开启评论 false关闭
tags: 
    - Tag1
    - Tag2
    - Tag3
categories: 
    - cate
---
正文内容 :dalfajlkdjfalkjdf
```

>貌似 不是立刻生效 因为能存在审核时间差吧  我当时没好使，后来突然就好了大概一个小时左右 我这里不是很清楚了 或许你们直接好使了~

#### 最后更改网站图标

**网站图标**

若要设置网站Favicon，可以将**favicon.ico**放在主题介绍里说的对应位置；
一般是source下 或者是 主题的source下；
>整理有个**坑!!!** 就是网站缓存,不管你怎么换都看不到最新的。需要换个电脑看

## 制作主题
[从零开始制作 Hexo 主题](http://www.ahonn.me/2016/12/15/create-a-hexo-theme-from-scratch/)
>看这篇文章即可！

## Reference&Thanks：

[Hexo官方文档，对于制作主题，选择主题，安装hexo很有帮助](https://hexo.io/zh-cn/docs/index.html)
[简书：HEXO+Github搭建属于自己的博客](http://www.jianshu.com/p/ecd51e8ef2fa)
[HexoLearning：HEXO+Github搭建属于自己的博客](https://github.com/limedroid/HexoLearning)
[我选的主题地址](https://camo.githubusercontent.com/60dd124e56fa642adc6f8f125c79702db25cc48c/687474703a2f2f7777322e73696e61696d672e636e2f6c617267652f303036744e63373967773166616f6139746d37676e6a33313276306f723432312e6a7067)
[从零开始制作 Hexo 主题](http://www.ahonn.me/2016/12/15/create-a-hexo-theme-from-scratch/)

