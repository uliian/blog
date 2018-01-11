---
title: 如何用Hexo免费搭建自己的博客
date: 2018-01-11 17:09:44
tags:
---
# 如何用Hexo免费搭建自己的博客

## 准备工作

1、七牛账号一个

2、Github仓库一个

3、Travis-CI账号一个并与Github账号关联

4、七牛客户端Qshell下载好

5、Node.js安装好，并安装好Yarn

## 创建七牛Bucket

进入七牛管理后台-对象存储服务-创建Bucket。此时，你就拥有了一个静态 `Web Server`可以使用了。
接下来，你需要点击右上角个人面板-密钥管理来获取七牛API的使用`appKey`和`appSecret`
![build-blog-2018111171820](http://blog.uliian.com/resources/build-blog-2018111171820.PNG)
即`AK`和`SK`
七牛为我们提供了10G的免费空间和CDN可以使用，对于个人博客来说完全绰绰有余，如果你需要域名的话，可以向七牛的账户里充个10元钱，七牛就可以让你的域名CNAME到七牛的空间了哦

## 获取Hexo并建立博客

按照Hexo的文档操作非常简单,进入命令行，并进入合适存放博客相关文件的目录，例如`D:\`？

``` bash
npm install hexo-cli -g
hexo init blog
cd blog
npm install
hexo server
```

如果您敲完了以上命令，恭喜你！你将看到一个博客运行起来了。