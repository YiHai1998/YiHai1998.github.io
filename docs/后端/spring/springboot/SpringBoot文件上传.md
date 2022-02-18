### 单文件上传



1. 新建SpringBoot项目，添加web依赖
2. resources目录下创建upload.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<form action="/upload" method="post" enctype="multipart/form-data">
<input type="file" name="uploadFile" value="请选择文件">
<input type="submit" value="上传">
</form>
</body>
</html>
```

3. 新建Controller.FileUpLoadController.java

```java
package com.xiyun.uploadfile.controller;

import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.multipart.MultipartFile;

import javax.servlet.http.HttpServletRequest;
import java.io.File;
import java.io.IOException;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.UUID;

/**
 * @Author 溪云
 * @Verse 溪云初起日沉阁，山雨欲来风满楼。
 * @Date 2021/1/17 17:08
 */
@RestController
public class FileUploadController {
    SimpleDateFormat sdf = new SimpleDateFormat("yyyy/MM/dd/");

    @PostMapping("/upload")
    public String upload(MultipartFile uploadFile, HttpServletRequest req) {
        String realPath =
                req.getSession().getServletContext().getRealPath("/uploadFile/");
        String format = sdf.format(new Date());
        File folder = new File(realPath + format);
        if (!folder.isDirectory()) {
            folder.mkdirs();
            String oldName = uploadFile.getOriginalFilename();
            String newName = UUID.randomUUID().toString() +
                    oldName.substring(oldName.lastIndexOf("."), oldName.length());
            try {
                uploadFile.transferTo(new File(folder, newName));
                String filePath = req.getScheme() + "://" + req.getServerName() + ":" +
                        req.getServerPort() + "/uploadFile/" + format + newName;
                return filePath;
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        return "上传失败!";
    }
}
```

4. 启动之后，输入：http://localhost:8080/upload.html，上传图片

5. 输入路径，如：http://localhost:8080/uploadFile/2021/01/17/46a02601-bfaf-42a7-9693-ce732db92409.png，即可查询图片
6. 更多细节在application.properties

```properties
spring.servlet.multipart.enabled=true
spring.servlet.multipart.file-size-threshold=0
spring.servlet.multipart.location=C:\\Users\\Long\\Desktop\\temp
spring.servlet.multipart.max-file-size=1MB
spring.servlet.multipart.max-request-size=10MB
spring.servlet.multipart.resolve-lazily=false
```



### 多文件上传

> 与单文件基本一致

1. upload.html，新增multiple

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<form action="/upload" method="post" enctype="multipart/form-data">
<input type="file" name="uploadFile" value="请选择文件" multiple>
<input type="submit" value="上传">
</form>
</body>
</html>
```

2. 修改控制器，主要是添加遍历，变为了数组。

```java
package com.xiyun.uploadfile.controller;

import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.multipart.MultipartFile;

import javax.servlet.http.HttpServletRequest;
import java.io.File;
import java.io.IOException;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.UUID;

/**
 * @Author 溪云
 * @Verse 溪云初起日沉阁，山雨欲来风满楼。
 * @Date 2021/1/17 17:08
 */
@RestController
public class FileUploadController {
    SimpleDateFormat sdf = new SimpleDateFormat("yyyy/MM/dd/");

    @PostMapping("/upload")
    public String upload(MultipartFile[] uploadFiles, HttpServletRequest req) {
        for (MultipartFile uploadFile : uploadFiles) {
            String realPath =
                    req.getSession().getServletContext().getRealPath("/uploadFile/");
            String format = sdf.format(new Date());
            File folder = new File(realPath + format);
            if (!folder.isDirectory()) {
                folder.mkdirs();
                String oldName = uploadFile.getOriginalFilename();
                String newName = UUID.randomUUID().toString() +
                        oldName.substring(oldName.lastIndexOf("."), oldName.length());
                try {
                    uploadFile.transferTo(new File(folder, newName));
                    String filePath = req.getScheme() + "://" + req.getServerName() + ":" +
                            req.getServerPort() + "/uploadFile/" + format + newName;
                    return filePath;
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
        return "上传失败!";
    }
}
```

