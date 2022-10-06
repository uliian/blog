---
title: 记录一次诡异的Ubuntu Service自动停止的问题
date: 2022-10-06 13:56:09
tags:
---

记录一次Ubuntu20下用户Service自动退出问题。
<!-- more -->

我在我的一台云服务器上使用`ubuntu20`，并且通过在`~/.config/systemd/user`目录下建立业务Service定义文件，并通过`Systemctl --user `来管理Service。一直以来运行都很好，这种模式下不需要root用户即可管理相关服务，我很喜欢。但是这次在一台边缘网关上使用却遇到了问题。

问题现象是每隔一段时间，该服务就会自动退出，并且程序没有任何错误日志记录。我的服务定义如下：
```
[Unit]
Description=rtsp-push
After=network.target

[Service]
WorkingDirectory=/home/xxx/yyy
ExecStart=/home/xxx/yyy/zzz
Restart=always
RestartSec=30
Type=simple

[Install]
WantedBy=multi-user.target
```

我一直将解决问题的思路放在程序本身上，各种调试问题依旧存在。这就让我很疑惑，我在Service的定义里明确指明了`Restart=always`，每次自动推出后，使用`Systemctl --user status`查看，就只能查看到一堆Service Starting，Start Succeed，Service Stping的日志。让我百思不得其解。我不得不怀疑是`Systemd`出了问题。
使用`journalctl --user -xe`进行查看，发现了这么一条日志：

`systemd[179819]: Stopped target Main User Target.`

一查果然是罪魁祸首。

```
The services where automatically terminated, after the user logged out.

loginctl enable-linger username

Fixed this
```

当Service定义中存在如下定义：`WantedBy=multi-user.target`，当用户登出后，服务将自动停止。

解决办法也说明了，使用命令`loginctl enable-linger username`即可解决，这一命令释义如下：
```
enable-linger [USER…], disable-linger [USER…]¶
启用/禁止用户逗留(相当于保持登录状态)。 如果指定了用户名或UID， 那么系统将会在启动时自动为这些用户派生出用户管理器， 并且在用户登出后继续保持运行。 这样就可以允许未登录的用户在后台运行持续时间很长的服务。 如果没有指定任何参数， 那么将作用于当前调用者的用户。
```
