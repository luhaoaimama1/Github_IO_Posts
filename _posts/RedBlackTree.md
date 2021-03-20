---
title: 红黑树认真看完保证你懂 ##文章标题
date: 2020-5-5 3:13:37
## updated: 2016-12-29 13:19:37
comments: true ##开启评论 false关闭
tags:
    - redblacktree

categories:
    - 算法
---

这个是基于理解什么叫平衡二叉树，如果这个不懂 不建议看
 
>如果有错误请指出谢谢~

<!-- more -->


# 1.红黑树为什么是2-3-4树？

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1geh0ctnyzpj315s0u0dt8.jpg)

在补充一下。这个节点也会影响最终树的高度，那么就是查找会更快一些.其次记住红黑平衡树并不是真正的平衡，而是红黑平衡。已二叉树去显示和查找都是已经不一定平衡的。
最好的情况就是都是红黑树都是4节点 高度是Log(4,N)=1/2*Log(2,N)，最坏的情况就都是2节点的真正完美平衡树Log(2,N)。

对于算法4中的2-3树 去学习，我就不用不建议了。为啥 因为你学完还要学234树因为这个更效率对吧。你以234树学的话 那么23树你就不需要学习了。这个东西学起来满麻烦的
# 2.为什么是左倾红黑树?
这里用引用https://www.jianshu.com/p/37c845a5add6 这篇文章是从外国翻译过来的。不错非常好 学没学过都建议看一看

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1geh0iw4t80j30rf1e4gq1.jpg)

# 3.红黑树的定义

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1geh0ickrlfj314x0c8416.jpg)

# 4.获取操作什么的就不说了。这里研究下添加操作的原理。
![](https://tva1.sinaimg.cn/large/007S8ZIlgy1geh0hpw4zlj315x0u07fn.jpg)

# 红链 可能出现的问题与解决  

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1geh0hgep00j315h0u0gty.jpg)


> 这个是2-3树里的添加操作的理解 未整理 可以不必看

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1geh0h013uwj313z0u0n8s.jpg)

# 删除最大操作的理解
![](https://tva1.sinaimg.cn/large/007S8ZIlgy1geh0gu8zdej316n0u04aa.jpg)

> 其实修复那个颜色翻转一直理解有知道 告诉下我~

# 删除元素的最总理解
![](https://tva1.sinaimg.cn/large/007S8ZIlgy1geh0gkcdmqj311a0u0ap9.jpg)


> [代码地址](https://github.com/luhaoaimama1/JavaZone/blob/master/JavaTest_Zone/src/%E7%AE%97%E6%B3%95/%E7%AE%97%E6%B3%95%E4%B9%A6%E7%B1%8D/%E7%AC%A6%E5%8F%B7%E8%A1%A8/BlanceTree234.java) 这里的删除的代码都没重构，能不抽出逻辑就不抽出 主要是为了理解 只要便于理解就好) 还有就是Node public等泛型强转的问题 
我也是因为另外的绘图类[DrawUtils](https://github.com/luhaoaimama1/JavaZone/blob/master/JavaTest_Zone/src/%E7%AE%97%E6%B3%95/%E7%AE%97%E6%B3%95%E4%B9%A6%E7%B1%8D/%E5%B7%A5%E5%85%B7/DrawUtils.java).因为自己看不到每次debug都得一层一层点开Node然后用笔画，所以写的这个.时间匆忙 就能简单就简单 好用就行。 

![](https://tva1.sinaimg.cn/large/007S8ZIlgy1geh0swjtgjj31rr0gijut.jpg)


### 剩下的闲聊 和代码无关：

算法第4版的红黑树代码 有问题 刚开始我是不信的，我一直以为我代码的问题  自己检查对比 就是不行。后来看我推荐看的那个链接里 发现评论里有我一样的问题他说了。我才敢相信。虽然作者很强 应该赞扬，但是这个事情真的就很蛋疼。我就这个删除的理解硬是搞了3天 我一直觉得我也不笨 。对着算法书里删除的解释 就那两三页的介绍 不停的看，看了大概一天半，期间好几次想放弃。但是我越想越不服，都花费这么长时间了也不差时间了。然后各种上网找资料 奈何找到的都没法看，因为都是红黑树作者的规定，但是他为什么这么规定的 网上的资料却没人说 。感觉答案就靠硬背的感觉 感觉那种根本不算学会 而且我这种人记忆力超差的 如果不理解半天就忘记了，就算理解了 我一两天也忘记了 必须总结出非常易于理解的 然后每次忘记就看看，终于在推荐的那个网页找到了 一看就能看进去。后来发现算法第4版网站里对红黑树PDF的翻译 真的是太给力了。然后确实看懂了个大概。写下来然后加上自己前一天半的思想。从另一个方式理解红黑树。所有NIL的高度不变为核心去理解.总算写完了 内心也算舒畅了 希望能帮助更多的人理解红黑树。 顺便吐槽下 这个小圆实在是难画 多年不用笔了 感觉写字都发飘。 终于可以去学习新的算法了 不知道这本书啥时候能看完呢 俺（算法小白）还想刷刷lecode。

# Reference&Thanks：

https://www.jianshu.com/p/37c845a5add6