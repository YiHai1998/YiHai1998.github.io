# 安装

http://nginx.org/en/download.html

alibaba cloud centos安装nginx教程：https://www.cnblogs.com/wuxu-dl/p/10516325.html

```
tar -zxvf nginx-1.20.0.tar.gz -C /opt/module/
```



pcre-8.35

```
wget http://downloads.sourceforge.net/project/pcre/pcre/8.35/pcre-8.35.tar.gz

tar -zxvf pcre-8.35.tar.gz -C /opt/module/

cd /opt/module/pcre-8.35/

./configure

make && make install

# 版本
pcre-config --version
```



配置安装

```
cd nginx-1.20.0/

./configure --prefix=/opt/module/nginx --with-http_stub_status_module --with-http_ssl_module --with-pcre=/opt/module/pcre-8.35

make && make install
```



## 启动

```
sbin/nginx
```

访问http://192.168.28.116/

## 部署其他

部署前端，以Griffin为例

#### 部署到服务器

```
npm run-script build
```

得到` dist `

把 dist 文件夹下的打包文件拷贝到 nginx/html 下并重命名为 griffinUI

修改 `conf/nginx.conf `文件

```
location / {
   root html/griffinUI;
   index index.html index.htm;
}
```

启动 nginx

```
sbin/nginx
```

在浏览器中输入http://192.168.28.116/即可看到项目

> 阿里云服务器如果无法访问，那么需要在conf文件首部加上
>
> ```
> user  root;
> ```

## 一些命令

```
(base) [root@glong sbin]# ./nginx -s reload   	# 重新加载配置
(base) [root@glong sbin]# ./nginx -s reopen		#  
(base) [root@glong sbin]# ./nginx -s stop    	# 停止 Nginx
```

