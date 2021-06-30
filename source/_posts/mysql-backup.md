---
title: MySQL 备份脚本
date: 2021-06-30 10:27:37
tags: 
    - MySQL
---

记录一个简单的MySQL备份脚本：

<!-- more -->
``` bash
time=$(date +"%Y%m%d$H")
echo 'begin mysql dump'
mysqldump --opt --single-transaction --master-data=2 --host=127.0.0.1 --user=root --password=mypswd --all-databases > /mnt/mysqlbackup/"$time".sql
echo 'mysql dump finish'
echo 'begin tar'
tar -czPf /mnt/mysqlbackup/"$time".tar.gz /mnt/mysqlbackup/"$time".sql
rm -f /mnt/mysqlbackup/*.sql
echo 'end tar'
echo 'begin upload'

#用我自己两句代码撸的阿里云OSS命令行工具上传压缩后的文件，并删除本地备份文件
alioss -ak {accessKey} -as {secret}  -b {bucketName} -e {endpoint} -f {source file} -r {target oss location}
rm -f /mnt/mysqlbackup/*.tar.gz
echo 'success'

```

在crontab里加一个定时任务，每天凌晨2点执行脚本
``` 
# crontab -e

0 2 * * * /mnt/mysqlbackup/mysqlbackup.sh
```

可以给阿里云上的目录做一个过期策略，然后就能自动删除过期文件咯，每日进行一次全备份。

顺便把工具的代码发上来吧

``` go
package main

import (
	"flag"
	"fmt"
	"github.com/aliyun/aliyun-oss-go-sdk/oss"
	"os"
)

func main() {
	var endPoint string
	var accessKeyId string
	var accessKeySecret string
	var bucketName string
	var filePath string
	var remotePath string

	flag.StringVar(&endPoint,"e","","阿里云EndPoint")
	flag.StringVar(&accessKeyId,"ak","","阿里云AccessKey")
	flag.StringVar(&accessKeySecret,"as","","阿里云AccessKeySecret")
	flag.StringVar(&bucketName,"b","","BucketName")
	flag.StringVar(&filePath,"f","","文件路径")
	flag.StringVar(&remotePath,"r","","远程文件路径")
	flag.Parse()

	_, err := os.Stat(filePath)
	if err!= nil{
		println("文件不存在")
		return
	}

	client, err := oss.New(endPoint, accessKeyId, accessKeySecret)
	if err!=nil{
		println("创建client发送错误",err)
	}
	bucket, err := client.Bucket(bucketName)
	if err!=nil{
		println("初始化bucket发送错误",err.Error())
	}
	err = bucket.PutObjectFromFile(remotePath, filePath)
	if err!= nil{
		fmt.Errorf("上传文件发生错误:%s",err.Error())
	}

}

```