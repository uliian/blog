---
title: Garnet不能替代Redis
date: 2024-03-31 19:09:22
tags:
    - Redis
    - Garnet
---

在我简单的测试中，发现了两个关键问题，目前看来，短期内Garnet无法成为Redis的替代品。
<!-- more -->

# 起因
2024年3月29日消息，Redis 作为开发项目常用的缓存数据库，于3月21日宣布变更开源协议，不再使用 BSD 3-Clause 协议，未来所有版本都将使用 “源代码可用” 的许可证。当然，在中国特色的自主研发下，我相信一定时期内，这一变更不会对大家使用Redis带来任何使用影响。

几乎同时，微软宣布开源了他的缓存服务：Garnet。Garnet在几个方面带给我了很大的吸引力：

1、全部由C# 代码实现，并且性能较Redis而言有不小提升。这对于一个C# 死忠粉而言，其诱惑力着实不小。

2、网络协议使用RESP实现，即Redis的网络协议，这意味着原有的Redis Client都能无缝对接使用。

3、根据其介绍，底层存储使用微软另一开源项目`FASTER`实现，这个开源项目几年前我关注过，其具备一些诱人的特性。如可靠持久性及一致性、分布式集群的天生支持等特性，对当年苦苦寻找可靠分布式KV引擎的我带来了致命的诱惑。

# 问题
我对Garnet的测试目标很简单，能否覆盖我现有常用的Redis功能，能否无痛切换。经过我的测试，有一下两个问题非常明显且致命：

## 1、不支持多数据库
虽然Redis之父`Salvatore Sanfilippo`曾表示，多数据库设计是Redis的一大失误，但是很遗憾，业界已经大量使用了这一特性，甚至一些库把这一特性用于一些业务场景。这直接造成了Garnet无法直接无痛替换Redis。这一问题在Garnet的仓库中已有使用者提出 [#61](https://github.com/microsoft/garnet/issues/61),目前该issue处于open状态。

## 2、不支持LUA
这在Garnet的[官方文档](https://microsoft.github.io/garnet/docs/welcome/compatibility)中有直接提到：
>3、Garnet does not support Lua scripting. We have an experimental version, but it was noted to be too slow for realistic use so we have not added it to the project.
>翻译：Garnet不支持 Lua 脚本。我们有一个实验版本，但是被指出在实际使用中速度太慢，因此我们没有将其添加到项目中。

这导致了一个很麻烦的问题：由于Redis的事务支持过于拉跨，过去大量库使用了基于Lua脚本的方式模拟事务，虽然Garnet带来了更加优雅的事务实现方式，但是这毫无疑问，完全无法支持过去的这一堆实现，君不见，连Spring Boot Data中都大量使用了这一黑魔法。

# 结论
这是我小小尝试将我自己的微小的应用迁移至Garnet发现的问题，可以想象，如果有更多的特性应用下，一定会暴露出更多麻烦的问题。同时，这也带给我了一些思考。在很多应用中，我们往往认为底层协议互通即可解决大部分的兼容性问题，从而带来便利的应用迁移条件。但是往往上层应用对Feature的使用是让人难以预料的，很多时候，基于底层库的上一层应用框架也带来了一些抽象程度更低，易用程度更高的解决方案（如JPA之于JDBC）这使得底层兼容/抽象变得更具挑战。