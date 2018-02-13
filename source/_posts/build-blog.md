---
title: 如何用Hexo免费搭建自己的博客
date: 2018-01-11 17:09:44
categories:
- 技术
tags:
- js
- 七牛
- hexo
---
# 如何用Hexo免费搭建自己的博客

## 准备工作

1、七牛账号一个

2、Github仓库一个

3、Travis-CI账号一个并与Github账号关联

4、七牛客户端Qshell下载好

5、Node.js安装好，并安装好Yarn
<!-- more -->
## 创建七牛Bucket

进入七牛管理后台-对象存储服务-创建Bucket。此时，你就拥有了一个静态 `Web Server`可以使用了。
接下来，你需要点击右上角个人面板-密钥管理来获取七牛API的使用`appKey`和`appSecret`
![build-blog-2018111171820](http://blog.uliian.com/resources/build-blog-2018111171820.PNG)
即`AK`和`SK`
七牛为我们提供了10G的免费空间和CDN可以使用，对于个人博客来说完全绰绰有余，如果你需要域名的话，可以向七牛的账户里充个10元钱，七牛就可以让你的域名CNAME到七牛的空间了哦。
**注意将七牛bucket中空间设置设默认首页设置开启**

## 获取Hexo并建立博客

按照[Hexo](https://hexo.io/zh-cn/)的文档操作非常简单,进入命令行，并进入合适存放博客相关文件的目录，例如`D:\`？

``` bash
npm install hexo-cli -g
hexo init blog
cd blog
npm install
hexo server
```

如果您敲完了以上命令，恭喜你！你将看到一个博客运行起来了。接下来，你需要阅读Hexo的[文档](https://hexo.io/zh-cn/docs/)对目录下的_config.yml进行简单的配置。

好了，回到命令行，进入你的博客目录`D:\blog`敲入命令

``` bash
hexo new helloworld
```

你将会看到\source\\_posts目录下出现了一个文件：helloworld.md OK，你可以在这里面写博客了。严重推荐使用VScode写，毕竟它对`Markdown`的支持是如此的好！

### 图片处理

稍等稍等，我写博客肯定是需要加图片的啊，不能加图我怎么晒娃？

![build-blog-2018111173718](http://blog.uliian.com/resources/build-blog-2018111173718.png)

嗯~VSCode有一个插件：

VSCode里按下

Ctrl+P

并输入

```bash
ext install qiniu-upload-image
```

进入用户设置界面
![build-blog-2018111174354](http://blog.uliian.com/resources/build-blog-2018111174354.png)

加入七牛相关设置

```javascript
{
    // 插件开关
    "qiniu.enable": true,

    // 一个有效的七牛 AccessKey 签名授权
    "qiniu.access_key": "*****************************************",

    // 一个有效的七牛 SecretKey 签名授权
    "qiniu.secret_key": "*****************************************",

    // 七牛图片上传空间
    "qiniu.bucket": "ysblog",

    // 七牛图片上传路径，参数化命名，暂时支持 ${fileName}、${mdFileName}、${date}、${dateTime}
    // 示例：
    //   ${fileName}-${date} -> picName-20160725.jpg
    //   ${mdFileName}-${dateTime} -> markdownName-20170412222810.jpg
    "qiniu.remotePath": "${fileName}",

    // 七牛bucket域名
    "qiniu.domain": "http://xxxxx.xxxx.com"
}
```

OK，在你的markdown编辑页面里按下`shif+o`可以直接上传文件并插入图片标签

## 自动发布

建立一个目录叫CI，然后到七牛的网站[下载七牛命令行工具](https://developer.qiniu.com/kodo/tools/1302/qshell)下载Linux64位版改名位：`qshell.exe` `qshell` 放到CI目录下并添加相关七牛上传config

qiniuconfig_ci.cfg:

```javascript
{
   "src_dir"            :   "./public/",
   "bucket"             :   "blog",
   "file_list"          :   "",
   "key_prefix"         :   "",
   "up_host"            :   "",
   "ignore_dir"         :   false,
   "overwrite"          :   true,
   "check_exists"       :   false,
   "check_hash"         :   false,
   "check_size"         :   false,
   "rescan_local"       :   true,
   "skip_file_prefixes" :   "test,demo,",
   "skip_path_prefixes" :   "hello/,temp/",
   "skip_fixed_strings" :   ".svn,.git",
   "skip_suffixes"      :   ".DS_Store,.exe,.cfg",
   "log_file"           :   "upload.log",
   "log_level"          :   "info",
   "log_rotate"         :   1,
   "log_stdout"         :   true,
   "file_type"          :   0
}
```

其实这个我也就是抄抄七牛的配置拉~
在博客目录下创建文件`.travis.yml`用于CI自动发布

```yml
language: node_js
node_js:
  - "8"
cache:
  yarn: true
script:
    - hexo deploy
    - chmod +x ./ci/qshell
    - ./ci/qshell account $qiniuak $qiniusk
    - ./ci/qshell qupload ./ci/qiniuconfig_ci.cfg
```

为了不把没用的东西发上去，我把我的.gitignore贴出来一下：

```gitignore
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/
```

好了，把这个博客的所有程序push到你的github上吧！

接下来，需要设置[Travis-CI](https://travis-ci.org)用你的GITHUB账号登录并授权，选择你的**博客仓库**
![build-blog-2018111183726](http://blog.uliian.com/resources/build-blog-2018111183726.PNG)
并将七牛的AK/SK设置为环境变量供七牛qshell使用
![build-blog-2018111184233](http://blog.uliian.com/resources/build-blog-2018111184233.png)
![build-blog-2018111185211](http://blog.uliian.com/resources/build-blog-2018111185211.png)

OK~接下来可以将你的代码push到github上了，CI会自动帮你生成博客并传到七牛

**但是**很遗憾！Travis-CI和七牛的通信并不顺畅，经常上传失败。

## 半自动发布

我们可以通过vscode的Task发布，其实也就是执行deploy命令，再执行七牛qshell就好咯~

1、下载七牛windows shell，并将shell放到CI文件夹下，命名为qshell.exe

2、创建七牛配置：

```javascript
{
   "src_dir"            :   ".\\public",
   "bucket"             :   "blog",
   "file_list"          :   "",
   "key_prefix"         :   "",
   "up_host"            :   "",
   "ignore_dir"         :   false,
   "overwrite"          :   true,
   "check_exists"       :   false,
   "check_hash"         :   false,
   "check_size"         :   false,
   "rescan_local"       :   true,
   "skip_file_prefixes" :   "test,demo,",
   "skip_path_prefixes" :   "hello/,temp/",
   "skip_fixed_strings" :   ".svn,.git",
   "skip_suffixes"      :   ".DS_Store,.exe,.cfg",
   "log_file"           :   "upload.log",
   "log_level"          :   "info",
   "log_rotate"         :   1,
   "log_stdout"         :   false,
   "file_type"          :   0
}
```

3、执行命令：

```shell
hexo clean
hexo deploy
.\\ci\\qshell.exe qupload .\\ci\\qiniuconfig_win.cfg
```

执行完毕完成上传~其实我们也可以用vscode的task帮我们做哦~我把我的task配置晒出来：

```javascript
{
    // See https://go.microsoft.com/fwlink/?LinkId=733558
    // for the documentation about the tasks.json format
    "version": "2.0.0",
    "tasks": [
        {
            "label": "run",
            "type": "shell",
            "command": "hexo server",
            "problemMatcher": []
        },
        {
            "label": "clean",
            "type": "shell",
            "command": "hexo clean",
            "problemMatcher": []
        },
        {
            "label": "deploy",
            "type": "shell",
            "command": "hexo deploy",
            "problemMatcher": []
        },
        {
            "label": "upload",
            "type": "shell",
            "command": ".\\ci\\qshell.exe qupload .\\ci\\qiniuconfig_win.cfg",
            "problemMatcher": []
        }
    ]
}
```

顺序执行`clean`-`deploy`-`upload`任务。完成部署~