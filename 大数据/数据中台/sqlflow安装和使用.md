# SqlFlow调研

参考资料：

SQLFlow从零开始安装使用：<https://blog.csdn.net/qxk5055/article/details/109314643>

sql安装：<https://www.freesion.com/article/35341403604/>

搭建sqlflow：<https://zhuanlan.zhihu.com/p/258104727>

官网参考： <https://sql-machine-learning.github.io/sqlflow/doc/run/kubernetes/>

一. 安装 Docker, 参考: <https://docs.docker.com/engine/install/centos/>
\0. 卸载之前 Docker

```
yum remove docker \
        docker-client \
        docker-client-latest \
        docker-common \
        docker-latest \
        docker-latest-logrotate \
        docker-logrotate \
        docker-engine
```

1. 安装工具包

```
yum install -y yum-utils
```

1. 配置镜像仓库 (官网镜像比较慢, 使用国内阿里云镜像仓库)

```
yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

1. 更新 yum 软件包索引

```
yum makecache fast
```

1. 安装最新版本Docker Engine和容器

```
yum install docker-ce docker-ce-cli containerd.io
```

1. 启动 Docker

```
systemctl start docker
```

1. 配置容器镜像加速, 修改 /etc/docker/daemon.json 文件内容, 无则创建.

```
打开阿里云网页 -- 菜单 -- 产品与服务 -- 容器镜像服务 -- 镜像加速器
	注: 其中的 xxxxx ,每个人不相同,自己注册登陆后复制自己的.
sudo mkdir -p /etc/docker

sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://xxxxx.mirror.aliyuncs.com"]
}
EOF

sudo systemctl daemon-reload
sudo systemctl restart docker
```

1. 通过运行 hello-world 镜像来验证是否正确安装了Docker Engine

```
docker run hello-world
	容器运行时,它会打印参考消息并退出,下载的镜像使用 docker images 可查看.
```

至此, Docker 安装成功!

SqlFlow安装过程相关文件目录：

```
/
	sqlflow
		config            --所有配置文件统          
		out               --所有日志输出统
```

二. 安装 Minikube (迷你版 k8s 集群), 参考: <https://minikube.sigs.k8s.io/docs/start/>
\0. 删除集群, 重新安装 (报错太多或安装失败)

```
minikube delete --all
```

1. 安装和配置 kubectl, 官网参考: <https://kubernetes.io/docs/tasks/tools/install-kubectl/>
2. 安装和配置 Minikube, 官网参考: <https://minikube.sigs.k8s.io/docs/start/>

1. 启动 Minikube

```
minikube start --vm-driver=none --kubernetes-version=v1.17.0 --image-repository=registry.cn-hangzhou.aliyuncs.com/google_contain
```

1. 查看 K8S 服务进程

```
[root@tsingdata01 config]# kubectl get pods -n kube-system
NAME                                  READY   STATUS    RESTARTS   AGE
coredns-7ff77c879f-cf2mg              1/1     Running   5          4d1h
coredns-7ff77c879f-t4wvv              1/1     Running   5          4d1h
etcd-tsingdata01                      1/1     Running   0          22d
kube-apiserver-tsingdata01            1/1     Running   3          22d
kube-controller-manager-tsingdata01   1/1     Running   10         22d
kube-flannel-ds-7l765                 1/1     Running   8          22d
kube-flannel-ds-8svtw                 1/1     Running   8          22d
kube-flannel-ds-gtl4p                 1/1     Running   6          22d
kube-proxy-jl4zt                      1/1     Running   5          22d
kube-proxy-ncrvf                      1/1     Running   4          22d
kube-proxy-wmvr4                      1/1     Running   4          22d
kube-scheduler-tsingdata01            1/1     Running   9          24h
```

至此, Minikube 安装成功!
三. 安装 Argo

1. 需要提前下载一个工具包 socat, 否则最终访问页面被拒

```
yum install socat
```

1. 启动 Argo 服务

```
创建 Argo 命名空间
 kubectl create namespace argo

 创建用户
 kubectl create rolebinding default-admin --clusterrole=admin --serviceaccount=default:default

 启动 Argo 服务
 kubectl apply -n argo -f https://raw.githubusercontent.com/argoproj/argo/v2.7.7/manifests/install.yaml
 	这一步骤,通常因网络原因导致失败.
 	则可先使用 wget 将文件下载到本地某文件夹中,再进行加载:
 	1. wget https://raw.githubusercontent.com/argoproj/argo/v2.7.7/manifests/install.yaml
 		移动并修改名称到此, pwd : /sqlflow/config/argo.yaml
 	2. kubectl apply -n argo -f /sqlflow/config/argo.yaml
 	
 *若下载或操作错误,可删除后重新下载
 kubectl delete -f /sqlflow/config/argo.yaml  
```

1. 启动 Argo, 访问所有 pods, 访问 Argo UI

```
kubectl get pods -nargo --watch
等待所有组件均 running,具体表现运行情况为: 1/1,之后再进行下一步
 	
nohup kubectl -n argo port-forward deployment/argo-server 2746:2746 --address=0.0.0.0 &
查看日志是否报错: cat nohup.out
浏览器访问: http://192.168.28.116:2746
```

![img](https://cdn.nlark.com/yuque/0/2021/png/21655255/1623399514464-862f5024-8ed5-4ff4-bff1-601b1db30bf7.png)

至此, Argo 安装成功!
四. 切换数据源

1. 下载 k8s 配置文件

```
wget https://raw.githubusercontent.com/sql-machine-learning/sqlflow/develop/doc/run/k8s/install-sqlflow-multi-users.yaml 
```

\2. 备份该文件, 并配置 SQLFLOW_JUPYTER_SSL_KEY、SQLFLOW_JUPYTER_SSL_CERT 为空, 以及禁用身份验证

```
cp install-sqlflow-multi-users.yaml install-sqlflow-multi-users.yaml.bak

vim /sjfw/sqlflow/multi_user/install-sqlflow-multi-users.yaml
      置空KEY、CERT: 
         - name: SQLFLOW_JUPYTER_SSL_KEY
           value: 
         - name: SQLFLOW_JUPYTER_SSL_CERT
           value: 
      禁用身份验证:
         - name: SQLFLOW_JUPYTER_USE_GITHUB_OAUTH
           value: "false"
```

1. MAKE A FAKE SECRET IN K8S’ STORE

```
kubectl create secret generic sqlflow \
     --from-literal=jupyter_oauth_client_id=dummy \
     --from-literal=jupyter_oauth_client_secret=dummy

kubectl apply -f install-sqlflow-multi-users.yaml

kubectl get pods --watch
	等待所有组件均 running,具体表现运行情况为: 1/1,之后再进行下一步
```

\4. 将JUPYTER NOTEBOOK映射到本地端口

```
nohup kubectl port-forward deployment/sqlflow-jupyterhub 8000:8000 --address=0.0.0.0 &
	可查看日志是否报错: cat nohup.out

使用浏览器打开: localhost:8000, 登录用户名密码随意
```

登录成功页面:

![img](https://cdn.nlark.com/yuque/0/2021/png/21655255/1623399832444-d32bd36b-9dd6-4366-a4d1-0267c19aa845.png)

##### 至此, SQLFLOW 安装成功!

进入容器查看

```
kubectl exec -it sqlflow-server-6ff977f696-5dr7p /bin/bash
```



容器本身没有一些库，需要手动添加，从本地服务器复制过去。

```
kubectl cp /opt/module/miniconda3/lib/python3.7
/site-packages/ sqlflow-server-6ff977f696-5dr7p:/usr/lib/python3.8/
```



配置数据源

<https://blog.csdn.net/qxk5055/article/details/109314643>

进入容器，执行命令

```
kubectl exec -it sqlflow-server-6ff977f696-5dr7p /bin/bash
```

```
sqlflow --sqlflow-server=localhost:50051 --data-source="mysql://root:root@tcp(192.168.28.116:3306)/"
```

```
show databases;
```

```
sqlflow> show databases;
SQLFlow Step: [1/1] Execute Code: bash -c step -e "show databases;" 
SQLFlow Step: [1/1] Log: http://192.168.28.116:2746/workflows/default/sqlflow-skptm?tab=workflow&nodeId=sqlflow-skptm-118895837&sidePanel=logs:sqlflow-skptm-118895837:main
SQLFlow Step: [1/1] Status: Pending
SQLFlow Step: [1/1] Status: Running
SQLFlow Step: [1/1] Status: Succeeded
+--------------------+
|      DATABASE      |
+--------------------+
| information_schema |
| azkaban            |
| dataxweb           |
| davinci0.3         |
| dbusmgr            |
| gmall              |
| gmall_report       |
| hue                |
| metastore          |
| mysql              |
| performance_schema |
| quartz             |
| ry_es              |
| test               |
+--------------------+
```

###### 想成功运行并预测结果, 需在 mysql 创建 sqlflow 预测结果专用数据库 sqlflow_models, 否则找不到输出会报错.

###### 所以记得在自定义的数据源中, 创建该数据库。

```
create database sqlflow_models;
```