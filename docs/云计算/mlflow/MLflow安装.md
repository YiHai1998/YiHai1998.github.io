

官网：https://mlflow.org/



# 前置条件

1. 安装Anaconda或miniconda，使用[清华镜像源](https://mirror.tuna.tsinghua.edu.cn/help/anaconda/)。
2. python3.6及其以上

# 开始

```
pip install mlflow

git clone https://github.com/mlflow/mlflow
```



```
python setup.py install
```

## 训练模型

测试训练的线性回归模型包含两个超参数：alpha 和 l1_ratio。

这个例子用pandas，numpy，sklearn的API构建了一个简单的机器学习模型。通过[MLflow tracking APIs](https://mlflow.org/docs/latest/tracking.html)来记录每次训练的信息，比如模型超参数和模型的评价指标。这个例子还将模型进行了序列化以便后续部署。

代码位于examples/sklearn_elasticnet_wine/train.py

```
python examples/sklearn_elasticnet_wine/train.py <alpha> <l1_ratio>
```

其中，<alpha> <l1_ratio> 为参数，0~1之间。可以不输入，会使用默认参数。

使用不同的超参数训练几次模型

```
python train.py 0.5 0.5
python train.py 0.4 0.4
python train.py 0.6 0.6
```

每次运行完训练脚本，MLflow都会将信息保存在目录mlruns中。如下图所示

![img](https://cdn.nlark.com/yuque/0/2021/png/519413/1619687435383-a7f130a7-7f4b-4725-90b7-45513a42f955.png)

## 比较模型

在mlruns目录的上级目录中输入命令：

```
mlflow ui
```

访问：http://127.0.0.1:5000/#/

如果在本地执行mlflow ui，那么浏览器输入：[http://localhost:5000/#/](https://links.jianshu.com/go?to=http%3A%2F%2Flocalhost%3A5000%2F%23%2F)，即可看到效果，查看本地端口。

> 问题：因为mlflow服务启动的时候默认是127.0.0.1，通过代理访问的时候会有问题，需要重新绑定ip。

```
mlflow ui  --host 0.0.0.0
```

这样，无论mlflow服务部署在本地，还是部署远程通过代理访问，浏览器都能正常访问mlflow了。

![img](https://cdn.nlark.com/yuque/0/2021/png/21655255/1622009544367-afb9e85d-6645-4284-8e3a-d81b30c5be4f.png)

![img](https://cdn.nlark.com/yuque/0/2021/png/519413/1619687622810-d6342788-669f-402b-807b-266b0353f287.png)

可以通过**搜索功能**快速筛选感兴趣的模型，比如搜索条件设置为metrics.rmse<0.8可以将rmse小于0.8的模型找出来，如果更复杂的搜索条件，可以下载CSV文件并用其他软件进行分析。

## 打包模型

如果已经写好了训练代码，可以将其打包提供给其他的人来**复用**，或者可以进行**远程训练**。

我们根据[MLflow Projects](https://mlflow.org/docs/latest/projects.html)的惯例来指定代码的依赖和代码的入口。比如创建一个**sklearn_elasticnet_wine**目录，在这个目录下创建一个**MLproject**文件来指定项目的conda依赖和包含两个参数alpha和l1_ratio的入口文件。

```
# sklearn_elasticnet_wine/MLproject

name: tutorial

conda_env: conda.yaml

entry_points:
  main:
    parameters:
      alpha: float
      l1_ratio: {type: float, default: 0.1}
    command: "python train.py {alpha} {l1_ratio}"
```

conda.yaml文件列举了所有依赖：

```
# sklearn_elasticnet_wine/conda.yaml

name: tutorial
channels:
  - defaults
dependencies:
  - numpy=1.14.3
  - pandas=0.22.0
  - scikit-learn=0.19.1
  - pip:
    - mlflow
```

通过执行`mlflow run examples/sklearn_elasticnet_wine -P alpha=0.42`可以运行这个项目，MLflow会根据conda.yaml的配置在指定的conda环境中训练模型。

如果代码仓库的根目录有MLproject文件，也可以直接通过Github来运行，比如代码仓库：https://github.com/mlflow/mlflow-example。我们可以执行这个命令`mlflow run git@github.com:mlflow/mlflow-example.git -P alpha=0.42`来训练模型。

## 部署模型

我们通过[MLflow Models](https://mlflow.org/docs/latest/models.html)来部署模型。一个MLflow Model是一种打包机器学习模型的标准格式，可以被用于多种下游工具，比如实时推理的REST API或者批量推理的Apache Spark。

在训练代码中，这行代码用于保存模型（原文称为artifact，暂且翻译成模型产品吧）：

```
mlflow.sklearn.log_model(lr, "model")
```

我们可以在UI中通过点击`Date`链接来查看每次训练的模型产品，例如：

![img](https://cdn.nlark.com/yuque/0/2021/png/21655255/1622010809089-e6ce4f9b-41ef-4e27-9885-093c8e970034.png)

在底部，我们可以看到通过调用`mlflow.sklearn.log_model`产生了两个文件，位于类似目录

```
/Users/mlflow/mlflow-prototype/mlruns/0/7c1a0d5c42844dcdb8f5191146925174/artifacts/model
```

。MLmodel元数据文件是告诉MLflow如何加载模型。model.pkl文件是训练好的序列化的线性回归模型。

运行下边的命令，可以将模型部署成本地REST服务(要确保训练模型和部署模型所用的python版本一致，否则会报错)：

```
# 需要替换成你自己的目录
mlflow models serve -m /Users/mlflow/mlflow-prototype/mlruns/0/7c1a0d5c42844dcdb8f5191146925174/artifacts/model -p 1234
```

部署好服务之后，可以通过curl命令发送json序列化的pandas DataFrame来测试下。模型服务器接受的数据格式可以参考[MLflow deployment tools documentation](https://mlflow.org/docs/latest/models.html#local-model-deployment).

```
curl -X POST -H "Content-Type:application/json; format=pandas-split" \
--data '{"columns":["alcohol", "chlorides", "citric acid", "density", "fixed acidity", "free sulfur dioxide", "pH", "residual sugar", "sulphates", "total sulfur dioxide", "volatile acidity"],"data":[[12.8, 0.029, 0.48, 0.98, 6.2, 29, 3.33, 1.2, 0.39, 75, 0.66]]}' \
http://127.0.0.1:1234/invocations
```

服务器会返回类似输出：

```
[6.379428821398614]
```

