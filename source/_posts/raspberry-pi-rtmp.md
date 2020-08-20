---
title: 树莓派连接USB摄像头搭建RTMP服务进行直播
date: 2020-08-19 15:01:52
tags: 
    - raspberry pi
    - nginx
    - openresty
    - usb camera
    - ffmpeg
---

不需要代码，在树莓派上使用`Openresty`和`ffmpeg`利用USB摄像头搭建RTMP直播服务。
<!-- more -->

最近老在说，中年男人~~对女性失去兴趣~~以后，就会开始折腾一些稀奇古怪的东西：什么盘手串啊，摄影啊，钓鱼啊...最近，我当上了陈养鱼。

养鱼很讲究技巧，我主要采用了`1357养鱼法`，该方法的精髓在于：**一天喂食，三天换水，五天洗缸，七天换鱼**  得此大法，缸中鱼儿生命质量能够得到有效保证，条条生龙活虎，一幅喜气洋洋的盛世光景。为了能够时刻感受我治下鱼缸的盛世光景，我希望给它添加一个监控，便于我随时随地获取鱼缸的最新动态。

当然了，市面上有很多具有直播功能的摄像头出售，但是，身为一个垃圾佬，怎么能够随随便便向资本低头？我转头看向了我那吃灰多年的`raspberry pi 3`，和一个陈年摄像头。来，我们请两位老戏骨出来露个脸。

![raspberry-pi-rtmp-pi3](https://blog.uliian.com/raspberry-pi-rtmp-202081915416.jpg)

![raspberry-pi-rtmp-ten-years-old-camare](https://blog.uliian.com/raspberry-pi-rtmp-202081915436.jpg)


此次需要用到的软件：`openresty（可换成nginx）` `ffmpeg`

首先根据Openresty官网要求，安装openresty，此处需要注意，openresty官方所带的模块中是没有RTMP模块的！！需要自行下载：
[https://github.com/arut/nginx-rtmp-module/releases](https://github.com/arut/nginx-rtmp-module/releases)
下载，解压将模块放到一个方便使用的目录，接下来编译openresty：
```bash
#例如我放置在/path/to/  目录下
sudo ./configure --add-module=/path/to/nginx-rtmp-module
sudo make
sudo make install
```

安装完成后，将Openresty注册成为系统服务

```ini
  [Unit]
  Description=full-fledged web platform
  After=network.target
  
  [Service]
  Type=forking
  PIDFile=/usr/local/openresty/nginx/logs/nginx.pid
  ExecStartPre=/usr/local/openresty/nginx/sbin/nginx -t -q -g 'daemon on; master_process on;'
  ExecStart=/usr/local/openresty/nginx/sbin/nginx -g 'daemon on; master_process on;'
  ExecReload=/usr/local/openresty/nginx/sbin/nginx -g 'daemon on; master_process on;' -s reload
  ExecStop=-/sbin/start-stop-daemon --quiet --stop --retry QUIT/5 --pidfile /usr/local/openresty/nginx/logs/nginx.pid
  TimeoutStopSec=5
  KillMode=mixed
  
  [Install]
  WantedBy=multi-user.target

```

将以上配置文件放置到`/lib/systemd/system/`目录下
```bash
sudo systemctl start openresty
sudo systemctl enable openresty
```

接下来，打开Openresty的配置文件：`/usr/local/openresty/nginx/conf/nginx.conf`,在末尾添加以下配置：
```
rtmp {
    server {
        listen 1935;
        chunk_size 4096;
        application live {
            live on;
            record off;
        }
    }
}
```

接下来让Openresty载入配置 

`sudo openresty -s reload` 或者 `sudo systemctl restart openresty`

Openresty（nginx）的相关配置已经完成，如果nginx正常启动，则表示已经正常开启了RTMP服务。接下来要对其进行推流。

将USB摄像头与树莓派进行连接，然后输入命令：`lsusb`，查看有没有正常识别摄像头，比如我的就长这样：
![raspberry-pi-rtmp-2020819161231](https://blog.uliian.com/raspberry-pi-rtmp-2020819161231.png)

如果能正常显示，接下来我们试着拍一张照片试试~

`fswebcam -d /dev/video0 test1.jpg`

`-d`参数用于指定摄像头设备地址，如果没什么问题，就会在当前目录下出现`test1.jpg`文件了。

OK，接下来我们安装`ffmpeg`。(ps:网上很多用`GStreamer`什么的来进行推流，真的好复杂，树莓派官方给的`raspivid`倒是好简单，但是！它仅支持板载摄像头...)

其实用ffmpeg进行推流超级简单，仅需一句命令：

```bash
ffmpeg -i /dev/video0 -f v4l2 -framerate 60 -video_size 640x480 -c:v libx264  -g 10 -f flv rtmp://127.0.0.1/live
#127.0.0.1是你在本机架设的rtmp服务地址，如果在其他机器上可以自行更改IP
```
至此，如果没有什么报错，您的USB摄像头直播服务已经基本架设完成。任意打开一个播放器：(我用的PotPlayer)，打开指定地址(您的树莓派IP)：`rtmp://192.168.31.94/live`开始进行观看
![raspberry-pi-rtmp-202082083128](https:////blog.uliian.com/raspberry-pi-rtmp-202082083128.png)

(请忽略这个10多岁高龄摄像头的AV画质，毕竟真的可以看见一条鱼)


如果你要全天24小时随时连入进行观看，可以将以上ffmpeg命令做成一个Systemd的服务即可。

哦，别忘了老戏骨们的工作照：

![raspberry-pi-rtmp-20208209192](https://blog.uliian.com/raspberry-pi-rtmp-20208209192.jpg)

至于如何随时随地观看，当然是通过提供面向公网的服务咯，比如用FRP做远程端口映射，或者申请公网IP+DDNS也可以（不过现在申请公网IP越来越难了）~那是另一个主题了！

至此，我已经能愉快地随时观察我的鱼儿情况了，如果发现需要换鱼，在下班路上就能直接买了带回去咯！