# Superset部署

## 前置要求

```
- Python3
- Anaconda/miniconda
```

### 安装Anaconda

https://mirrors.tuna.tsinghua.edu.cn/anaconda/miniconda/

下载上传到tsingdata02的/opt/software

```
bash Miniconda3-py39_4.9.2-Linux-x86_64.sh
```

一路回车、yes

![img](https://cdn.nlark.com/yuque/0/2020/png/519413/1599734808927-b3da7918-4a09-4679-9239-e59c04ff8064.png)

默认路径：/root/miniconda3

我们要修改为  /opt/module/miniconda3

![img](https://cdn.nlark.com/yuque/0/2020/png/519413/1599734891859-907e3bb9-2c43-41a4-9034-de1b6c717f90.png)

安装完毕！



添加为最新miniconda路径

```
vim ~/.bashrc 
```

```
export PATH="/opt/module/minianaconda3/bin:$PATH"
```

```
source ~/.bashrc 
```



重新打开，base为默认环境  3.9.1 是默认的python版本

```
(base) [root@tsingdata01 software]# python
Python 3.9.1 (default, Dec 11 2020, 14:32:07) 
[GCC 7.3.0] :: Anaconda, Inc. on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> 
```

是否每次登陆自动激活base环境，

```
conda config --set auto_activate_base false
```

再次开启就不会有base了



激活base等环境

```
conda activate 需要激活的环境，如base
```



## 安装Superset

### 环境

使用pip或者conda（Python的包管理工具），和centos的yum类似。

配置conda国内镜像

```
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/bioconda/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
```

显示下载地址：

```
conda config --set show_channel_urls yes
```

创建python3.6环境（不创建而使用默认的也可以）

```
conda create -name superset python=3.6  # 退出base环境，或者重启再执行这个命令
```

```
[root@tsingdata01 ~]# conda activate superset
(superset) [root@tsingdata01 ~]#
```

conda环境管理命令

```markdown
1. 创建环境：conda create -n env_name

2. 查看所有环境：conda info -envs

3. 删除环境: conda remove -n env_name --all
```

激活虚拟环境

```
conda activate 
```

关闭虚拟环境

```
conda deactivate
```



### 部署Superset

官网 [http://superset.apache.org/installation.html](http://superset.apache.org/installation.html#getting-started)

安装python 和 其他依赖

```
yum install -y python-setuptools

yum install -y gcc gcc-c++ libffi-devel python-devel python-pip python-wheel openssl-devel cyrus-sasl-devel openldap-devel
```

>`遇到的问题`：-bash: /usr/bin/yum: /usr/bin/python: 坏的解释器: 没有那个文件或目录
>
>`解决方法`：
>
>修改以下配置文件
>
>```
>vim /usr/bin/yum
>vim /usr/libexec/urlgrabber-ext-down
>```
>
>```
>#!/usr/bin/python2 -> #!/usr/bin/python2.7
>```

setuptools pip升级至最新版

```
pip install --upgrade setuptools pip -i https://pypi.douban.com/simple 
```

安装Superset

```
pip install apache-superset -i https://pypi.douban.com/simple 
```

初始化superset数据库

```
superset db upgrade
```

创建管理员用户

```
export FLASK_APP=superset
flask fab create-admin
```

```sh
[root@tsingdata02 ~]# flask fab create-admin
Username [admin]: tsingdata
User first name [admin]: 
User last name [user]: 
Email [admin@fab.org]: 
Password: 123456
Repeat for confirmation: 
logging was configured successfully
INFO:superset.utils.logging_configurator:logging was configured successfully
/opt/module/miniconda3/lib/python3.9/site-packages/flask_caching/__init__.py:201: UserWarning: Flask-Caching: CACHE_TYPE is set to null, caching is effectively disabled.
  warnings.warn(
No PIL installation found
INFO:superset.utils.screenshots:No PIL installation found
Recognized Database Authentications.
Admin User tsingdata created.
```

![image-20210511153005739](images/image-20210511153005739.png)

初始化

```
superset init 
```

安装gunicorn，这是类似tomcat的python web服务器

```
pip install gunicorn -i https://pypi.douban.com/simple
```

**启动**

```
gunicorn --workers 5 --timeout 120 --bind tsingdata02:8787 "superset.app:create_app()" --daemon
```

>- workers：指定进程个数
>- timeout： worker 进程超时时间，超时会自动重启
>- bind：绑定本机地址，即为Superset访问地址
>- daemon：后台运行4
访问：http://192.168.157.129:8787/

```
用户名 tsingdata
密码	123456
```

![image-20210511153551074](images/image-20210511153551074.png)

停止

```
ps -ef | awk '/superset/ && !/awk/{print $2}' | xargs kill -9
```



## 启动脚本

```
vim superset.sh
```



```sh
#! /bin/bash
superset_status(){
	result=`ps -ef | awk '/gunicorn/ && !/awk/{print $2}' | wc -l`
	if [[ $result -eq 0 ]]; then
		return 0
	else
		return 1
	fi
}
superset_start(){
	# 该段内容取自~/.bashrc，所用是进行 conda 初始化
	# >>> conda initialize >>>
	# !! Contents within this block are managed by 'condainit' !!
	__conda_setup="$('/opt/module/miniconda3/bin/conda' 'shell.bash' 'hook' 2> /dev/null)"
	if [ $? -eq 0 ]; then
		eval "$__conda_setup"
	else
		if [ -f "/opt/module/miniconda3/etc/profile.d/conda.sh" ]; then
			. "/opt/module/miniconda3/etc/profile.d/conda.sh"
		else
			export PATH="/opt/module/miniconda3/bin:$PATH"
		fi
	fi
	unset __conda_setup
	# <<< conda initialize <<<
	superset_status >/dev/null 2>&1
	if [[ $? -eq 0 ]]; then
		gunicorn --workers 5 --timeout 120 --bind 192.168.28.116:8787 --daemon 'superset.app:create_app()'
	else
		echo "superset 正在运行"
	fi
}
superset_stop(){
	superset_status >/dev/null 2>&1
	if [[ $? -eq 0 ]]; then
		echo "superset 未在运行"
	else
		ps -ef | awk '/gunicorn/ && !/awk/{print $2}' | xargs kill -9
	fi
}

case $1 in
	start )
		echo "启动 Superset"
		superset_start
;;
stop )
		echo "停止 Superset"
		superset_stop
;;
restart )
		echo "重启 Superset"
		superset_stop
		superset_start
;;
status )
	superset_status >/dev/null 2>&1
	if [[ $? -eq 0 ]]; then
		echo "superset 未在运行"
	else
		echo "superset 正在运行"
	fi
esac

```



# Superset使用

## 对接 MySQL 数据源

### 安装依赖

```
# 安装连接 MySQL 数据源的依赖
conda install mysqlclient	
```

>注意：对接不同的数据源，需安装不同的依赖，以下地址为官网说明
>
>http://superset.apache.org/installation.html#database-dependencies



```
# 重启 Superset

ps -ef | awk '/superset/ && !/awk/{print $2}' | xargs kill -9

gunicorn --workers 5 --timeout 120 --bind tsingdata02:8787 "superset.app:create_app()" --daemon
```



### 数据源配置

1、Database 配置

- 点击 Data/Databases

![image-20210511161445395](images/image-20210511161445395.png)

![image-20210511161508261](images/image-20210511161508261.png)

```
# 点击填写 Database 及 SQL Alchemy URI

Database: gmall_report
SQLAIchemy URI: mysql://root:root@tsingdata01:3306/gmall_report?charset=utf8
```

>注：SQL Alchemy URI 编写规范：mysql://账号:密码@IP:post/数据库名称

- 点击 Test Connection，右下角出现下图提示即表明成功

![image-20210511161923486](images/image-20210511161923486.png)

- 点击Data/Datasets



## 对接 Hive 数据源

安装驱动：https://superset.apache.org/docs/databases/installing-database-drivers

```
yum install gcc python-devel libsmbclient-devel openldap-devel zlib-devel libjpeg-turbo-devel libtiff-devel freetype-devel libwebp-devel lcms2-devel krb5-devel
pip install sasl
pip install thrift
pip install thrift-sasl

pip install pyhive		# 最重要的
```



```
superset.sh restart
```



```
hive://hive@192.168.28.116:10000/gmall
```



## 对接Kylin数据源

```
pip install kylinpy
```



## 实战

Charts

# Superset汉化

参考：https://www.jianshu.com/p/c751278996f8

```
cd /opt/module/miniconda3/lib/python3.7/site-packages/superset
```

修改`config.py`

```python
BABEL_DEFAULT_LOCALE = "zh"		# 将默认的en改为zh
# Your application default translation path
BABEL_DEFAULT_FOLDER = "superset/translations"
# The allowed translation for you app
#  
LANGUAGES = {
    "en": {"flag": "us", "name": "English"},
    #"es": {"flag": "es", "name": "Spanish"},
    #"it": {"flag": "it", "name": "Italian"},
    #"fr": {"flag": "fr", "name": "French"},
    "zh": {"flag": "cn", "name": "Chinese"},
    #"ja": {"flag": "jp", "name": "Japanese"},
   # "de": {"flag": "de", "name": "German"},
   # "pt": {"flag": "pt", "name": "Portuguese"},
   # "pt_BR": {"flag": "br", "name": "Brazilian Portuguese"},
   # "ru": {"flag": "ru", "name": "Russian"},
   # "ko": {"flag": "kr", "name": "Korean"},
}
# Turning off i18n by default as translation in most languages are
# incomplete and not well maintained.
# LANGUAGES = {}
```

translations/zh/LC_MESSAGES/messages.po就是编译好了的文件，直接重启应用即可

```
superset.sh restart
```



