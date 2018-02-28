---
title: MyBatis-Generator 不生成*ByPrimaryKey系列方法的问题
date: 2018-02-28 23:02:49
tags:
---

今天在家用MBG生成一张新表的Mapper，突然发现*ByPrimaryKey系列方法统统不见了。<!-- more -->网上搜了一下，[比较权威的说法](http://mybatis-user.963551.n3.nabble.com/Select-By-Primary-Key-Mapper-Method-Not-Generated-for-Join-Table-td4029422.html)是如果你的表中有且仅有主键列，则不生成那一系列方法。

我用的JAVA一定是个假JAVA，MySQL一定是个假货...明明有一堆字段啊...定睛一看，这货还是给我发了个warning的~：

```bash
Cannot obtain primary key information from the database, generated objects may be incomplete
```

居然说我的表信息里没有主键...我一定用了假的MySQL查了半天，在[这个帖子](http://blog.csdn.net/angel_xiaa/article/details/52474022)里稍微说了一下这个问题。但是这种说法...感觉很有乡间秘术的感觉，让人难以信服啊~但是`bing搜索引擎`再也搜不出更多的靠谱的信息了...

抱着一线希望，上了Google，居然找到了[这篇博客](https://www.kunzhao.org/blog/2017/07/23/mybatis/)详细分析了这个问题的起因。还真的是 **MySQL的驱动的锅！！！** 真的是无fuck说...

好吧，遇到这个问题怎么办？**将MBG使用的MySQL Driver降级至5.X版本**