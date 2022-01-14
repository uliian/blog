---
title: 使用jenkins在docker中打包docker镜像
date: 2022-01-14 11:05:17
tags:
    - docker
---

在docker环境中，打包docker镜像
<!-- more -->
两个方法，第一个方法，docker官方提供了一个docker in docker 镜像，使用这个镜像按理说可以在docker中使用docker命令进行build。镜像名就是docker
```shell
docker pull docker
```

我看了一下它的dockerfile，还依赖一些奇奇怪怪的外部文件，我简单尝试了一下，和我的编译环境一起打了个包，结果当场爆炸，所以没测试哈，按官方的说法是能用的。

另一个方法比较清真，直接把宿主机的docker链进来，干净清爽。在jenkins流程定义文件里
``` groovy
pipeline {
  agent {
    docker {
      image 'node:alpine'
      reuseNode 'true'
      // 挂载外部的 docker socket
      args '-v /var/run/docker.sock:/var/run/docker.sock -v /usr/bin/docker:/usr/bin/docker'
    }
  }
}
```

实测可用，清真！