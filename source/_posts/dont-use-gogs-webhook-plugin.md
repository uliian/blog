---
title: 别在Jenkins里用gogs-webhook 插件了
date: 2024-03-16 16:24:31
tags:
    - gogs
    - jenkins
---

答应我，做gogs和Jenkins的CI方案时，GOGS的WebHook通知方案不要再用gogs-webhook了，Generic Webhook Trigger也许才是版本答案。
<!-- more -->

## 起因
一位长者曾经说过“一个人的命运啊，当然要靠自我奋斗，但是也要考虑到历史的进程。”在软件世界里，不得不说，有些项目真是取了个好名字，让使用者不由自主地趋之若鹜。今天要嫌弃的这个项目就是Jenkins插件：`Gogs`，全称`Gogs-Webhook Plugin`。

在很多中小团队的私有Devops体系都偏爱于Gogs或者Gitea这样的轻量代码托管方案。当开发者提交代码以后，由代码托管服务向Jenkins发起Webhook调用，触发Jenkins进行CI。那么，在Jenkins中，都会安装相关插件与代码托管服务进行集成，如果使用GOGS，到Jenkins插件市场中搜索`GOGS`，并安装`GOGS`插件是一套很符合直觉的操作。

**但是**如果仔细观察这个插件的相关信息界面，你会发现几个问题：
>>1、该插件最后的发布日期是5年前
>>2、该插件页面的顶部存在两条硕大无比的Jenkins警告

![picture 0](https://blog.uliian.com/images/d22c7ef861130b2b1f300b069ae42a00833c144ad760f556eb4dbf9c6093f26d.png)  

**但是**我相信大多数人和我一样，都不爱关心这些，尤其被这个很“官方”的名字蛊惑，一顿操作就开干。如果你使用了这个插件，你会遇到两个致命问题（我的Jenkins版本为：Jenkins 2.426.1）：

1、你的webhook**有可能**无法触发构建，gogs的webhook测试显示404，找不到项目。我分别在两天，创建了两个Jenkins多分支Pipline构建，第一个很幸运地能够正常触发，第二个无论用什么姿势创建，都无法触发。

2、你的构建上下文中，无法使用GOGO webhook中Payload中的**任何信息**。

如果你感到困惑，找到该项目的GitHub仓库，你会惊喜地发现，该项目的最后一次提交停留在两年前，并且作者对issue已经处于不理不看的地步了。


## 解决方案
后来我找到了一个新的插件：`Generic Webhook Trigger`，这个插件深得我意，首先，它能通过自定义token来触发CI任务。
其次，它能通过Http Body（JSON/xml）中所携带的内容通过`JsonPath`、`XPath`来获取webhook中携带的各种变量，也能通过Form body所携带的内容通过指定KEY使用，还能获取http header中的内容及url查询字符串中的内容，这些获取到的信息都能定义环境变量供后续构建使用。
而且该项目使用者多，更新活跃，Github上也能看到频繁的commit。
同时，他不依赖于gogs，任何能够进行webhook调用的工具都能使用，给后续提供了较大的想象空间，适应性更强。

所以`Generic Webhook Trigger`,你值得拥有