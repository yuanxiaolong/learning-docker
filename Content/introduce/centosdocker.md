# centos上安装docker

1. 先安装 ``` yum install epel-release```

2. 再 ``` yum install docker-io```

3. 启动docker服务  ``` sudo service docker start```

4. 查看状态或版本 ``` docker version``` 或 ```docker info```


``` bash
[root@yuanxiaolong ~]# docker version
Client version: 1.1.2
Client API version: 1.13
Go version (client): go1.2.2
Git commit (client): d84a070/1.1.2
Server version: 1.1.2
Server API version: 1.13
Go version (server): go1.2.2
Git commit (server): d84a070/1.1.2

```
