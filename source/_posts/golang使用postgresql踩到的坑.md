---
title: golang使用postgresql踩到的坑
date: 2018-01-15 16:15:00
categories:
- 技术
tags:
- golang
- postgresql
---
# golang 下使用postgresql踩到的坑

今天需要做一个很小的API，功能超级简单，那么简单的东西就别搞一些烦人的runtime之类的东西去写了~又没啥逻辑，两行代码写完算了。
<!-- more -->
之前就说过嘛，我没有现成的server，这个事情很尴尬的~我才不会告诉你我的1核1G腾讯云主机用docker装个mysql老是因为内存不够mysql罢工......

好吧，之前我有一台骚包的阿里云Server是2core 4G的，可以装数据库呢~但是！因为被团队成员挪用，装了一个`屁股SQL`上去，怎么办？将就用呗！

我用的ORM是XORM~其实基本配置都很简单，和用MySQL一样一样的~但是！都配好以后发现貌似...连不上！

卧槽什么鬼？！我可是照着官方文档来的啊~用的这个库：
`https://github.com/lib/pq`

因为原来是用MySQL比较习惯嘛，看到文档上说，可以用mysql那种连接字符串的玩法来连`postgresql`

``` text
You can also connect to a database using a URL. For example:

connStr := "postgres://pqgotest:password@localhost/pqgotest?sslmode=verify-full"
db, err := sql.Open("postgres", connStr)
```

我就当真了！！！！然后怎么连都不对啊~老是说连接不上啊~而且我感觉`open`方法是个假货！就算连不上他都不报错的破烂玩意儿！

后来看了一下，推荐连接字符串貌似也很简单直观 **(ps:此处忍不住要吐槽Oracle，地球上有比你更难看更恶心的连接字符串吗？)**

```golang
connStr := "user=pqgotest dbname=pqgotest sslmode=verify-full"
```

其实就是一个个属性用等号空格连起来就好了！然后一连，成功！嗯~我也不知道我哪里姿势错了，有上面那种连接字符串就是连不上，不知道什么鬼！