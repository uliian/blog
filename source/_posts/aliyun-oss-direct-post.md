---
title: C#使用HttpClient阿里云直传失败解决方案
date: 2022-02-14 15:59:05
tags:
---

在C#里，使用HttpClient配合`MultipartFormDataContent`直传文件，遇到`The body of your POST request is not well-formed multipart/form-data`异常的处理办法
<!-- more -->
在使用HttpClient直传时，遇到传输body阿里云不认的情况，经过仔细比对，发现以下几个差异：

1、在Http Header中Content-Type中，boundary需要不带双引号

2、在Form中，每个Form的name值需要用双引号引起来

3、如果文件表单域带有`Content-Type`Header，则该字段需要放在`Content-Disposition`字段后

4、文件表单域在`Content-Disposition`中需要带有`fileName`

5、文件表单域`Content-Disposition`不支持`filename*=utf-8''%22aaa.txt%22`这样的文件名表示

阿里云的文档说，表单域name大小写敏感，实际上没有！
我的代码如下：
```C#
        public async Task UploadFile(string path, UploadFileData uploadFileData)
        {
            var fileName = Path.GetFileName(path);
            var multipartForm = new MultipartFormDataContent($"-----{Guid.NewGuid().ToString()}");
            var streamContent = new StreamContent(File.OpenRead(path));
            multipartForm.Add(new ByteArrayContent(Encoding.UTF8.GetBytes(uploadFileData.AccessKey)), "\"OSSAccessKeyId\"");
            multipartForm.Add(new ByteArrayContent(Encoding.UTF8.GetBytes(uploadFileData.Key)), "\"Key\"");
            multipartForm.Add(new ByteArrayContent(Encoding.UTF8.GetBytes(uploadFileData.Policy)), "\"Policy\"");
            multipartForm.Add(new ByteArrayContent(Encoding.UTF8.GetBytes(uploadFileData.Sign)), "\"Signature\"");
            multipartForm.Add(streamContent, "\"file\"",$"\"{fileName}\"");

            //修饰boundary，移除双引号
            var boundary = multipartForm.Headers.ContentType.Parameters.First(o => o.Name == "boundary");
            boundary.Value = boundary.Value.Replace("\"", String.Empty);

            //修饰文件表单域，移除FileNameStar
            streamContent.Headers.ContentDisposition.FileNameStar = null;
            var rsp = await this._httpClient.PostAsync(uploadFileData.Url, multipartForm);
            var content = await rsp.Content.ReadAsStringAsync();
        }
```

我真的不理解，阿里云OSS到底用了什么技术来解析Mutipart/Form表单，导致标准支持如此落后，如此死板。你们的996福报都用来干啥了？