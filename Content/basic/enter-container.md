# 如何进入运行中的容器？

容器是有状态的，因此进入一个运行中的容器是有必要的。例如，部署了一个redis的容器，此时我们想较快捷的用redis-cli查看一下redis-server里的数据，就需要进入这个redis-server容器。

进入容器的方法有很多，我们这里介绍的是nsenter。利用 nsenter 来进入一个正在运行的docker容器，可能系统没有自带 nsenter这个执行程序，需要编译源码获取。

编译依赖C编译器，建议 yum install gcc 安装一个gcc，如果已有则忽略。

#### 编译nsenter步骤

1.获取源码，解压，进入解压后文件夹。

```
[root@yuanxiaolong tmp]# wget https://www.kernel.org/pub/linux/utils/util-linux/v2.24/util-linux-2.24.tar.gz
--2014-10-22 23:08:50--  https://www.kernel.org/pub/linux/utils/util-linux/v2.24/util-linux-2.24.tar.gz
Resolving www.kernel.org... 199.204.44.194, 198.145.20.140, 149.20.4.69, ...
Connecting to www.kernel.org|199.204.44.194|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 7630306 (7.3M) [application/x-gzip]
Saving to: “util-linux-2.24.tar.gz”

100%[=========================================================>] 7,630,306   3.36M/s   in 2.2s

2014-10-22 23:08:53 (3.36 MB/s) - “util-linux-2.24.tar.gz” saved [7630306/7630306]

[root@yuanxiaolong tmp]# ls
util-linux-2.24.tar.gz
[root@yuanxiaolong tmp]# tar -zxf util-linux-2.24.tar.gz
[root@yuanxiaolong tmp]# ls
util-linux-2.24  util-linux-2.24.tar.gz
[root@yuanxiaolong tmp]# cd util-linux-2.24
```

2.检测，编译。

```
[root@yuanxiaolong util-linux-2.24]# ./configure --without-ncurses

## 如果没有C编译器会报错如下：
## checking for gcc... no
## checking for cc... no
## checking for cl.exe... no
## configure: error: in `/root/app/tmp/util-linux-2.24':
## configure: error: no acceptable C compiler found in $PATH
## See `config.log' for more details

[root@yuanxiaolong util-linux-2.24]# make
```

3.拷贝到可执行程序 到/usr/local/bin，这样就不用配置PATH环境变量了。

```
[root@yuanxiaolong util-linux-2.24]# cp nsenter /usr/local/bin
```
#### 编写bash脚本 ```docker-enter.sh```

```
[root@yuanxiaolong tmp]# vi docker-enter.sh

#!/bin/sh

if [ -e $(dirname "$0")/nsenter ]; then
    # with boot2docker, nsenter is not in the PATH but it is in the same folder
    NSENTER=$(dirname "$0")/nsenter
else
    NSENTER=nsenter
fi

if [ -z "$1" ]; then
    echo "Usage: `basename "$0"` CONTAINER [COMMAND [ARG]...]"
    echo ""
    echo "Enters the Docker CONTAINER and executes the specified COMMAND."
    echo "If COMMAND is not specified, runs an interactive shell in CONTAINER."
else
    PID=$(docker inspect --format "" "$1")
    if [ -z "$PID" ]; then
        exit 1
    fi
    shift

    OPTS="--target $PID --mount --uts --ipc --net --pid --"

    if [ -z "$1" ]; then
        # No command given.
        # Use su to clear all host environment variables except for TERM,
        # initialize the environment variables HOME, SHELL, USER, LOGNAME, PATH,
        # and start a login shell.
        "$NSENTER" $OPTS su - root
    else
        # Use env to clear all host environment variables.
        "$NSENTER" $OPTS env --ignore-environment -- "$@"
    fi
fi

[root@yuanxiaolong tmp]# chmod +x docker-enter.sh
```
2.查看正在运行的容器，然后利用脚本进入
```
[root@yuanxiaolong ~]# docker ps
CONTAINER ID        IMAGE                                   COMMAND              CREATED             STATUS              PORTS                NAMES
64e466932bdd        yuanxiaolong/centos-node-hello:latest   node /src/index.js   3 hours ago         Up 3 hours          8088/tcp             prickly_shockley
496861d3a50d        yuanxiaolong/centos-node-hello:latest   node /src/index.js   3 hours ago         Up 3 hours          8088/tcp             backstabbing_nobel
f7007b2bb4cb        jwilder/nginx-proxy:latest              forego start -r      3 hours ago         Up 3 hours          0.0.0.0:80->80/tcp   determined_nobel

[root@yuanxiaolong tmp]# sh ~/docker-enter.sh f7007b2bb4cb
root@f7007b2bb4cb:~#
```

#### 小技巧
1.脚本的位置
> <font size="3"> 个人喜欢把 ```docker-enter.sh``` 这个脚本 放到 ~ 目录下，这样每次用 ```sh ~/docker-enter 容器ID``` 就可以直接进入了。</font>


