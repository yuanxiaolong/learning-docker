# 镜像

镜像(images)是docker的2大核心概念之一，另一个概念叫做容器(container)

它是什么呢？
* 从面相对象的角度上看，它是 **类**
* 从工程的角度上看，它是 **模具**

为什么这样说呢？

因为镜像是 *无状态* 的，我们对镜像能做事情比较少。

#### 常用命令（root权限）

1.``` docker pull``` 获取一个镜像（拿centos6为例）

```
[root@yuanxiaolong ~]# docker pull centos:centos6
Pulling repository centos
68edf809afe7: Download complete
511136ea3c5a: Download complete
5b12ef8fd570: Download complete
```

2.``` docker images ```查看已有镜像

```
root@yuanxiaolong ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
centos              centos6             68edf809afe7        2 weeks ago         212.7 MB
```

3.``` docker rmi <镜像id> ``` 来删除一个容器

4.``` docker search ``` 在中心仓库搜索相应名称的容器

5.``` docker build ``` 构建镜像（在第三章Dockerfile里讲解，此时我们还用不上这个命令）


#### 命令解析
> <font size="3"> ```centos:centos6``` 表示从 centos 仓库获取 tag 为 centos6 的镜像</font>

#### 小技巧

1.获取版本
> <font size="3">
如果没有加tag参数，则会获取```lastest```版本，先看本地已有仓库是否已存在，如果不存在，则从中心仓库下载。</font>

2.删除镜像
> <font size="3">
如果``` docker rmi ``` 删除镜像失败，很可能是此镜像产生的容器有运行中的。</font>




