# 容器

容器(container) 它又是什么呢？

* 从面相对象的角度上看，它是 **实例**
* 从工程的角度上看，它是 **产品**

为什么这样说呢？

因为容器是 *有状态* 的，我们对容器 能做很多事情。一个镜像，可以产生多个容器，而每个容器之间相互隔离。容器可以有自己的状态。


#### 常用命令（root权限）

1.``` docker run ``` 产生一个容器（拿刚才我们下载的centos6为例）

```
[root@yuanxiaolong ~]# docker run 68edf809afe7 /bin/echo hello world
hello world

[root@yuanxiaolong ~]# docker run centos:centos6 /bin/echo hello world
hello world
```

2.``` docker ps ``` <font color="green">（重要）</font>查看运行中的容器，如果加了 ``` -a ```则显示全部容器，包括已经stop的容器

```
[root@yuanxiaolong ~]# docker ps
CONTAINER ID        IMAGE                                   COMMAND                CREATED             STATUS              PORTS                     NAMES
b800227c5a99        yuanxiaolong/redis-slave:latest         /entrypoint.sh ./sta   5 days ago          Up About a minute   0.0.0.0:49161->6379/tcp   redis_slave
17073302366e        redis:latest                            /entrypoint.sh redis   5 days ago          Up 11 minutes       6379/tcp                  redis-master,redis_slave/redis_master
6fa851fd73a4        yuanxiaolong/java-service:latest        /bin/sh -c 'java -ja   9 days ago          Up 9 days           0.0.0.0:49160->8090/tcp   hungry_goldstine
64e466932bdd        yuanxiaolong/centos-node-hello:latest   node /src/index.js     10 days ago         Up 10 days          8088/tcp                  prickly_shockley
496861d3a50d        yuanxiaolong/centos-node-hello:latest   node /src/index.js     10 days ago         Up 10 days          8088/tcp                  backstabbing_nobel
f7007b2bb4cb        jwilder/nginx-proxy:latest              forego start -r        10 days ago         Up 2 days           0.0.0.0:80->80/tcp        determined_nobel
```

3.``` docker stop ``` 停止一个运行中的容器

4.``` docker start ``` 运行一个已停止的容器

5.``` docker restart ``` 重启一个容器

6.``` docker rm ``` 删除一个容器，加上 ```-f``` 表示无视此容器是否在运行，强制删除

7.``` docker inspect ``` <font color="green">（重要）</font>查看一个容器的具体信息，可以看到很多有用的信息。

8.``` docker logs ``` <font color="green">（重要）</font>查看一个容器的运行日志


#### 命令解析
> <font size="3"> ``` docker run ``` 后面的第一个参数，可以是镜像的id 也可以是镜像的名字加tag，范例中 ``` /bin/echo hello world ``` 表示用 echo程序来执行接下来的参数</font>

#### 小技巧
1.获取help
> <font size="3"> 当我们不清楚 ```docker run ``` 怎么使用的时候，直接输入它，并不加任何参数，即能得到 ```help ```列表。</font>

2.进入交互模式
> <font size="3"> ``` docker run -i -t centos:centos6 /bin/bash ```
把你想进入的容器id替换掉这里的 ``` centos:centos6``` 即可

3.一次性容器
> <font size="3"> 有时候我们只是需要运行一下容器，查看一下结果什么的。如果用```docker run ```则会保存历史记录，这时候在参数里加上```--rm=true``` 则当你退出docker容器时，会删除掉历史记录。

4.后台运行
> <font size="3"> 有时候我们需要后台运行容器，```docker run  -d ``` 表示后台运行


#### 思考题
Q : 我们范例中 2次 ``` docker run ``` 后，产生的2个容器id 是一样的么？
