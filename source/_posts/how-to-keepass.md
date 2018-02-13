---
title: 如何在多端使用KeePass并保持数据同步?
date: 2018-01-22 13:14:43
tags:
- password
---
# 如何免费地在多端使用KeePass并保持数据同步？

最近收集了一些社工库，悲剧地发现了好几个自己地账号，我是一个很懒的人，常用地密码就那么几个~他们说地各种小技巧我都觉得好麻烦。之前在IPhone上用了 `1Password` ，好像还花了点钱买了他的Pro版，结果~它是不提供云同步服务的。如果需要使用云同步，则必须注册账号并充钱。充钱还是小问题，需要充外币就坑了我了~我木有VISA卡肿么破?
<!-- more -->
而且~我注册了他的账号，它把我的密码信息同步到云端了~接下来我用Android手机把同步的数据拉了下来（PS:小米商店没有1Password，百度商店有一个老版本，几乎用不了，废了好大的力气搞了GOOGLE play才下下来）把数据捞下来以后发现，这些数据都是**只读**的！！！而且我也没找到什么办法把这些数据撸到本地不用云端变成可读写了~实在太难忍了！！！

愤怒的我甚至都想自己做一个密码管理工具了~ 但是貌似看到有人说 `KeePass` 是可以同步的!而且可以用坚果云同步数据。好开心哦~于是说干咱就干！

1、注册[坚果云](https://www.jianguoyun.com)

2、查看个人信息!
![how-to-keepass-2018122135550](http://blog.uliian.com/resources/how-to-keepass-2018122135550.png)

3、点击安全选项

在第三方应用管理处添加应用密码：
![how-to-keepass-2018122133449](http://blog.uliian.com/resources/how-to-keepass-2018122133449.png)

创建一个应用

4、下载[KeePass](https://keepass.info/download.html)创建数据库并保存

![how-to-keepass-2018122134018](http://blog.uliian.com/resources/how-to-keepass-2018122134018.png)

5、保存数据库到本地磁盘

6、打开坚果云，创建Keepass目录，并将数据库文件上传至坚果云目录：
![how-to-keepass-2018122134252](http://blog.uliian.com/resources/how-to-keepass-2018122134252.png)

7、因为坚果云支持WebDaV协议(当年我做FSP的时候也参考了很多WebDav协议呢~)此时坚果云上你的数据库文件的URL是酱紫的：

```text
https://dav.jianguoyun.com/dav/{目录名}/{文件名}
例如我的就是
https://dav.jianguoyun.com/dav/KeePass/uliian.kdbx
```

8、在KeePass中点击File-Close菜单，关闭你的数据库,并用URL打开你的数据库文件
![how-to-keepass-2018122134714](http://blog.uliian.com/resources/how-to-keepass-2018122134714.png)

弹出菜单中
![how-to-keepass-2018122134833](http://blog.uliian.com/resources/how-to-keepass-2018122134833.png)

URL填入`WebDav地址`

用户名填入`坚果云的用户名`

密码填入`坚果云的密码`

OK-之后填入创建数据库时的数据库密码~

每次新增-变更密码后，点击保存，都会将密码同步到坚果云~
![how-to-keepass-2018122135114](http://blog.uliian.com/resources/how-to-keepass-2018122135114.png)

OK~您的私人云端密码工具就完成了！

如果你需要在手机端使用，Android建议下载KeePass2Android，设置方法和PC端一样，填入WEBDAV地址用户名-密码就完事了~

愿一站一密伴随你左右！