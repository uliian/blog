---
title: 我的2018-技术篇
date: 2019-01-06 17:08:59
tags:
    - 年终总结
---

2018，从未见过的挑战。虽然每一年都会经历一些新的东西，但2018似乎与前些年大有不同。我的技术版图而言，无疑大大拓宽了，我笑称“面向工资编程”，我依旧爱`C#`但是我也承认，已经有越来越多的好东西进入了我的视野，我更加激进地开始探索。
<!-- more -->

2018年初，大家匆匆进行了告别，连一顿像样的散伙饭都没吃就各自奔向了新的下家，算是一场遗憾吧，BOSS肖精挑细选的团队随着“国字号”的垮塌也灰飞烟灭。BOSS肖和高鹏进入了币圈，老胡和郭老师去了720云，D大和V大创业，还未谋面的神龙去了传奇程序员`Martin Fowler`他们公司，在`华为`被咨询着他的骚操作，话不多的易同学去做了广电相关的项目，学霸戴博还是没有选择学历再深造而选择了进入“赵家军”。我总是惋惜，能在这块互联网荒漠上组建起这样一支团队十分难得，过多的吐槽也于事无补，两个月前传来前公司法人已经被请去接受审查的消息，不知道前公司的这些幺蛾子能算他的几宗罪？很有意思的是，包括我在内，我们之中好几位童鞋都上了金鼎山，时不时还聚个餐，遇到了问题还会一起在群里进行一番讨论；几天前D大的CMS产品还被我拿来修改了几天作为我们的新产品上了线。

## PHP
2018年，学习使用了几门语言，其中用量最大的该是PHP了，说实话，我是不爱动态语言的，但文章开头也说了，需要面向工资编程，生活所迫，不得不学习使用。随着不断地学习使用，我也在思考，动态语言的优势具体在什么地方？和常见的静态OOP语言相比，优劣在哪？动态语言确实简单粗暴，一言不合就加东西，各种骚操作也能提升不少编码效率。但我真的不是很习惯这种玩法......尤其是`PHP`，某些语言特性丧心病狂得让人匪夷所思。被人骂得最多的语言`JS`这些年的发展就像装上了喷气式发动机，我还真的越来越喜欢这东西了，尤其是有了`TypeScript`加持，更是令槽点少了很多。PHP却不是这样......写PHP的时候经常会很难受，很多在其他语言里是标配的东西，它却没有，或者支持得极其简陋。例如：增加运行时元数据的注解（特性、装饰器），神奇的`Lambda表达式`，我真的被刷新了认知，我觉得JAVA支持的lambda已经够拧巴了，没想到PHP更甚；如果再加上其神奇的`闭包语法`，时常让我哭笑不得......但是随着我对PHP生态的不断了解，我发现PHP和JAVA有个极大的不同，两者开源生态都很好，但PHP的开源生态偏业务系统，开箱即用。JAVA的开源生态偏基础组件，这个区别很值得玩味，所以大多数创业公司选择PHP真算是一个不错的选择，对于快速实现业务PHP有着较大优势。

## Golang
经过去年的一些小实践，我已经基本找到了GO之于我的一些兴奋点，在一些工具性的开发场景和一些简单业务上使用GO真的是不错的选择。尤其在硬件资源紧张的场景下，对比JAVA，GO有着得天独厚的优势，尤其在一些业务简单、业务内存耗费低的场景下更是如此，我在一些数据迁移、信息收集场景下使用了GO感觉十分良好。但如果你用C#/JAVA的那一套套路来写GO的话就会感觉十分拧巴，直到我看了一篇文章提到了GO的编写风格其实和C很类似后我才茅塞顿开！简单粗暴，避免不必要的抽象似乎成了GO程序的一个设计哲学，前些天看了一篇文章说道“鼓励复用和鼓励易读”是两种不一样的设计哲学，而GO很明显偏向于后一种，减少层层抽象，少了很多花样，变得简单直接。如果你采用这样的思维模式去编写GO的代码，你会舒服很多。但是，这是对的吗？我持严重的怀疑态度~我们长期的代码设计理念都在教育我们追求两件事：
>1、抽象

>
>2、高内聚、低耦合

而如何做到恰当的抽象？总是软件工程界不断探索的领域，目前看来，没有银弹；JAVA的过度抽象已经越来越被广泛诟病，而GO如此简单，甚至简陋的抽象能力我也并不认为会成为主流。另外，GO的发展也充满了纠结~长期以来争论最大的两个点：`1、异常；2、泛型`随着`Golang 2.0`的白皮书的出现似乎又要有了新的风向。做技术讨论时，似乎所有技术体系内傲慢的观点都可以通过两个理由来驳斥你的质疑：`1、我们不需要；2、我们也可以`。曾经我和GO的粉丝讨论过异常，他们通常会和我说：
>“错误和异常不一样，要分开处理”
>

>我：“数据库连接错误到底算是错误还是异常？”
>

>“应该算是异常”
>

>我：“那为什么大多数数据库连接库遇到这个问题都会返回一个erro呢？”
>

>沉默 沉默是今晚的康桥...

我也探讨过泛型问题：
>“我们不需要泛型，其实interface已经能满足我们的需求了”
>

>“那么...请问我想做一个类型通用的去重、排序、关联怎么做比较合适？”
>

>“这样的业务需求Golang不太擅长，如果真的需要，你可以看看Golinq”
>

>原来反射还能这样用哦.......

必须承认，Go在特定领域已经成为较为优秀的开发工具，其领域内生态也较为完善，是个非常不错的选择，时常让你感叹，写程序心理负担好小，但我目前难以承认它是优秀的全能型语言......而Golang2.0已经受到了真香打击，异常、泛型极大概率会随着2.0带到我们的生产生活中来，我对其充满期待。

## JAVA
是我今年写得最多的语言，对于一个`C#`出身的程序员而言，我对JAVA存在着天生的抵触，随着对JAVA的了解逐渐深入，我也开始重新审视这一门伟大的语言。虽然JAVA语法很差，很是啰嗦，但其生态的健壮、全面真的是其他任何一门语言都无法比拟的。几乎你的所有需求，都有相应的解决方案，总体而言，基础设施方面极其省心。尤其经历了前公司大量迁移轮子后，我对底层生态带来的生产力提升越发看重，毕竟人生苦短，造轮子艰难。尤其在微服务架构盛行而Service Mesh还未成气候的今天，完善的底层生态所带来的收益将是难以估量的。但是随着最近JAVA的一些方向变更，你会发现JAVA这个篮子也并不如预期那样坚固了......

1、看不懂的版本飙升

随着JAVA9的到来，JAVA开始进入了版本飙升的阶段，每半年一个大版本......大哥，运行时升级不算一件十分轻松的事情啊，况且让你更抓狂的是每一次大版本升级所带来的变更似乎太少了，这就令人无比尴尬，升级嘛，风险不小，收益不大；不升级嘛将来跨版本升级风险更大，升还是不升？成了一个大问题。

2、Spring-Could Netflix 套件的停滞

作为这几年最为明星的微服务套件，Spring Cloud的重要性毋庸置疑，其中Netflix贡献的一系列组件已经成为最核心的产品。而随着年初Eureka2.0的流产，到如今Hystrix等组件纷纷进入维护状态，可以感受到Netflix相关套件的停滞，虽然其他厂商也纷纷开始将自己的产品纳入Spring Cloud生态圈，但毕竟还需时日，前些天还看了阿里的`nacos`，感觉挺不错，但不得不说，还不完善，有待观察。

3、语法和基础组件的拖累

从我们不需要委托，到Lambda真香，JAVA的语法进步真的太过于缓慢了，RxJava发布已经很多年了，随着Spring 5的发布，Reactive编程热度才有了较大的攀升。我也尝试了Reactor的开发，但必须说....有时候真的很痛苦.....很多场景下我们需要的东西很简单，也许就是一个简单的异步IO，不说C#已经用了那么多年的`async/await`了连JS都满大街的`async/await`了，真的心累。JDBC对异步的支持也是个神秘的问题，ADO.NET在我还不知道C#为何物的时候就支持异步接口了，至今JDBC还是不为所动，看着Reactor下的JDBC事务操作，真是哭笑不得......我记得几年前就开始说的JAVA10要加入的`reified generics`现在都准备发布JAVA12了还没反应......

4、律师公司的作恶

虽然主流JAVA社区一直在解释这一影响，并可以使用Open-JDK完全替代，但不得不承认，律师公司修改了JAVA的授权协议以后，还是会对JAVA的用户造成不小的伤害，就我接触过的很多朋友而言，之前他们都还有着生产环境用Oracle JDK，而不用Open JDK的传统，新年伊始，Oracle JDK全面转向收费我相信对风控管理严格的公司是会造成一些负面影响的。

## Kotlin
在JAVA焦虑的环境下，我在下半年开始全面接触Kotlin，我很惊喜地发现Kotlin在完整兼容JAVA的基础上大大升级了语法，如果作为`better JAVA`那算是极佳的选择了。逆变、协变的支持；可空操作符，null默认值，data class类型、扩展函数、更好的lambda表达式，协程......很多很优秀的语言特性出现在了这门语言中，这对于语法进步迟缓的JAVA而言无疑是一个大喜讯，能大大提高生产力。有幸于出生较晚，它吸收了很多语言的优秀特性，摒弃了很多JAVA的糟粕（如throws），从它的身上找到了很多`C#`的影子，让我很很是欣慰，很快爱上了这门语言，现在它已经成为了我的主力语言。在欣喜的同时，我也感受到了它的一些无奈，如同`Typescript`受制于`Javascript`一样，`Kotlin`很明显的受制于JVM，万恶的类型擦除还是影响了Kotlin，泛型还是如同残疾一般......而且从协程库和异步处理的方案上也能感受到深深的无奈，这些优秀的特性都成了库，而不是语法......更让人郁闷的是Kotlin对集合的处理另起了一套炉灶，如果在Kotlin里强用`Stream API`你会感到一些别扭。

## C#
很难过，C#已经不是我的主力语言，虽然我还用C#维护着`EasyRBAC`,但是写C#的时间已经很少了。我很欣喜地看到`.NET Core`在2018活得越来越好，`.NET Core 3`也提上了日程，几个星期前我在给`EasyRBAC`增加一些特性地时候，发现我已经不记得VS的一些热键了，甚至有时候会把关键字写错，很是伤感。但我在用其他语言时，经常会怀念C#，优秀的语言设计，设计优秀的BCL，还好有了`Kotlin`不然我觉得我长期写JAVA会吐......`EasyRBAC`在新的一年我想我会扩大使用规模，同时也对内部一些实现进行一些变更，尤其是SSO部分，当初写的时候就是想图省事，一个纯HTML页面完事，现在用起来越来越不方便，是时候重构一波了。我想我会持续对`.NET Core`进行关注并跟进，我很希望C#能够重新振作起来，毕竟，这个世界里多一种选择对大家都是有好处的，但是，从历史发展的规律看，推翻一个事物的东西通常不是这个事物的优化，而是全新的理念；那C#如果可以和JAVA分庭抗礼的话，只能依托于杀手级应用产品，如同`Unity`这样的某个场景下存在绝对优势的产品，而现在，这样的产品还是太少了。对此，我并不十分悲观，尤其近期接触的某些场景，C#在一些传统软件领域的优势还是存在的，远不像互联网场景那么悲惨，有些细分领域甚至还有很大的优势，这些优势可以发扬，在互联网大潮降温的今天，不失为一个机会。

今年还玩了`Typescript`、`Python`、`Lua`......但终究花的时间精力很少，今年做的UI极其少，所以可爱的`Typescript`也没有过多宠幸，`Python`还写了一些，但并不是火热的AI领域，而是一些小工具之类的东西，玩票性质更多。这方面Python的生态比`PHP`更奇葩，总能找到一些奇奇怪怪的应用，什么爬虫、微信机器人这些正经人不太用得上的东西非常多~ `Lua`嘛，就是用来玩一玩`OpenResty`，我越来越发现`Nginx`上针对某些功能的扩展越来越像某种刚需，深度解析个请求日志什么的，用上`OpenResty`还真是性价比超高。哦对了，还学了一点点`Arduino`这玩意儿有点意思，心里有个小小的产品计划，希望2019能够做出原型吧~

纯看技术实践的话，今年在一些奇技淫巧方面还是比较满足的，对云服务也算有了一些挺好的实践，但是对于大型应用场景，今年显然是不够的，毕竟这需要场景......初步打算明年学一学`Rust`，今年简单看了看，感觉还不错，我觉得对我会有一些新的启发。而底层场景方面，应该好好实践一下`K8S`了，同时搭建一套`Service mesh`玩一玩，虽说这玩意儿现在并不算十分成熟，但是我很看好其前景。明年如果一切顺利的话，会为公司做一套不太复杂的用户画像系统，算是有点挑战吧，2019，希望经济寒冬早点过去，大家都少一点焦虑......