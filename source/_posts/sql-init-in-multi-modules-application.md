---
title: 在Spring boot多模块项目中，分模块管理初始化SQL脚本
date: 2023-11-27 18:07:46
tags:
---

我在一个多模块的Spring boot项目中，每个模块负责着相对独立的业务，我能够按需编译我所需要的模块，我希望应用启动时，引入的包能够自动执行初始化SQL脚本，进行数据表的初始化。同时，我希望每个模块能够自行维护自身的SQL版本，互不干扰。

我们看两种技术选择:

1、使用`Spring boot` 自带的`sql.init`配置

2、使用flyway

<!-- more -->
# Spring boot SQL init方案
在Spring boot 中，提供了数据库初始化解决方案，在配置中
```yml
spring:
  #老版本
  datasource:
    initialization-mode: ALWAYS|EMBEDDED|NEVER
  #新版本
  sql:
    init:
      mode: ALWAYS|EMBEDDED|NEVER
```

该配置可以直接配置是否在应用启动时执行SQL语句进行数据库初始化。在默认模式下，应用启动后，会执行classpath下的schema.sql文件和data.sql。当然，这两组文件的具体路径可以通过配置文件进行修改。
那么假如我们有如下的目录解构
```
- application
  - module1
    - src
      - main
        - resources
          - schema.sql
          - data.sql
  - module2
    - src
      - main
        - resources
          - schema.sql
          - data.sql

```
应用程序启动后，会将两个`schema.sql`和`data.sql`执行以进行数据初始化。这样已经能初步满足我们的应用要求。

但是在具体的实践中，存在两个问题

## 1、何时执行？

我们可以看到`mode`中有三个选项，使用`ALWAYS`和`EMBEDDED`会执行这些脚本，其中最令人困惑的选项是`EMBEDDED`,从字面上看，如果使用的数据库是嵌入式数据库即执行，不是嵌入式数据库即不执行。

那么问题来了，请问`SQLite`是不是嵌入式数据库？

很可惜，`Spring boot` 并不认为`SQLite`是嵌入式数据库。它仅认为H2，HSQL，Derby是嵌入式数据库。所以这个选项是具有很大迷惑性的，我个人认为，将这个选项改为MEMORY更好。

那么在正经使用过程中，如果要通过这个方式进行数据库初始化，那么只有`ALWAYS`可以使用，但是当你在使用中你会发现，使用这个选项后，***每一次应用启动时，都会执行你的数据库脚本！！***如果执行失败，将导致应用无法启动。

那如何处理这个问题呢？

1、我们需要在SQL脚本里做文章。如常见的建表脚本需要增加判断，改为：
```SQL
create table if not exists person
(
    id    integer primary key,
    name  varchar(255),
    email varchar(255)
);

```
而数据脚本则维护为
```SQL
INSERT IGNORE ...
INSERT ... ON DUPLICATE KEY UPDATE
```
这样的幂等脚本。

2、我们可以进行设置，将` spring.sql.init.continue-on-error `选项设置为`true`，即时执行出错了也将正常运行程序。

> TIPS:spring 执行 脚本时，对脚本要求较为严格，我就出现过因为没有打;导致把单一一行脚本拿去执行造成出错。

## 2、表解构、数据变更怎么处理？
在我们的开发过程中，尤其是不能指望数据库表解构不做变更，而采用了上述方法，长期的变更、维护显然就变得很麻烦，需要注意很多SQL黑魔法，心智负担越来越重。

只需要几十张表，每个月新增/变更一两张表，一年以后，这个维护工作得代码规范执行的多好的人才能完成得下来？

# Flyway方案
在实践中，我更加推荐`Flyway`方案，尤其在数据库变更总是在同一主线的情况下，在常见的中小业务系统情形下，使用`Flyway`是近乎于完美的选择。

## Flyway的简单工作原理
什么是数据库变更总是在同一主线？就是你能很自然地将你每一个数据变更脚本放置到一个目录下，你的应用实例的每个副本对应的基础表都是一样的情况下。使用`flyway`实在是不二之选。

简单地说，`Flyway`的工作原理是使用一个***约定的SQL脚本命名规则***在应用启动后，在目标数据库实例中维护一张`flyway_schema_history`表用以记录变更历史，然后判断此次应用启动中，指定目录下的脚本是否出现了新版本，要是出现了新版本SQL文件，即执行新版本的SQL。这样维护SQL的工作就减轻了不少。

## Flyway的局限性

但是，在我的一个应用场景中，Flyway出现了明显的局限性：

我维护着一个物联网设备操作应用，该应用是模块化、插件化设计的。我根据不同的实施场景，在编译时选择不同的物联网设备插件组装我的应用，组装完毕后分发执行应用。

我的应用自带了一个`SQLite`数据库作为本地数据从持久化方案，但是不同的插件有可能需要在数据库里建立不同的表，并且随着应用的更新升级，任何表都有可能出现变化。
而且根据我选择的插件不同，不同应用副本对应的数据库表也不同。我希望我的表维护有多个分支，不同插件在各自的模块中维护各自的表版本分支。

Flyway对这样的模块化的支持不太友好。我们看一下影响这一实现的主要因素：`flyway_schema_history`表。
| instaalled_rank  | version  | description | type | script| checksum|installed_by|installed_on|execute_time|success|
|---|---|---|---|---|---|---|---|---|---|
|主键|版本（来自于文件名的版本部分）|描述，来自于文件名的描述部分|'SQL'|对应的完整文件名|校验和||执行时刻|执行耗时|执行结果|

很悲剧，在这张表的设计里，并没有任何一个地方标识模块。而在Flyway的仓库里，也有不少人提出了这个issue，如：[#304](https://github.com/flyway/flyway/issues/304)、[#3003](https://github.com/flyway/flyway/issues/3003)、[#852](https://github.com/flyway/flyway/issues/852)

## 临时解决方案
综合了各种issue下提的解决方案，目前我认为相对可行的方案是给每个模块分配一个大版本号，然后各个模块自行维护自己的数据库版本分支。

具体方案如下：

文件树：
```
- application
  - module1
    - src
      - main
        - resources
          - db
            - migration
              - V0.0.1__init.sql
              - V0.0.2__changeA.sql
              - V0.0.3__changeB.sql

  - module2
    - src
      - main
        - resources
          - db
            - migration
              - V1.0.1__init.sql
              - V1.0.2__changeA.sql
              - V1.0.3__changeB.sql
```

然后修改`application.yml`文件
```yml
spring:
  flyway:
    out-of-order: true
```
这样能够变相提供不同模块维护不同SQL脚本版本的目的。

# 总结
在上述两种方法中，我们可以看到，各有局限性，在多模块、数据库变更极少的情况下，`Spring boot`提供的数据库初始化方案也不失为一个好选择。可以看到，单纯对模块化的支持而言，`Spring boot`提供的方案更加优雅。而长期看来，`Flyway`提供的数据库版本管理方案功能更加完备，对于长期维护，容易出现数据库变更的场景下，还是强烈建议使用`Flyway`方案的。

让人遗憾的是，在截稿日期（2023年11月28日），并未看到`Flyway`有任何对模块化加大支持的计划，在最初有人提出模块化需求至今，已经过了很多年头了。