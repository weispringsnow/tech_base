[TOC]

#### 1、安装jdk8并部署

```shell
docker search jdk
docker pull ascdc/jdk8  
docker stop jdk1.8
docker rm jdk1.8
docker run -di --name=jdk1.8 kdvolder/jdk8
docker exec -it jdk1.8 /bin/bash
java -version 
```
#### 2、安装tomcat8并部署

```shell
docker search tomcat
docker pull tomcat:8.5.40
docker images
docker run --name tomcat -d -p 8080:8080 tomcat:8.5.40
docker ps
docker logs 2ab1e4a82216
docker exec -it tomcat /bin/bash
/usr/local/tomcat/webapps/
```
#### 3、部署项目

```shell
docker build -t crm:v1 .docker build -t crm:v1 .Dockerfile

from tomcat:8.5.40
MAINTAINER 973664512@qq.com
RUN rm -rf /usr/local/tomcat/webapps/*
COPY button.war /usr/local/tomcat/webapps
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone


docker build -t goods:v1 .
docker run --name goods --restart=always -d -p 8080:8080 goods:v1 


Dockerfile
FROM kdvolder/jdk8
VOLUME /tmp
ADD cciaBack.jar cciaBack.jar
RUN ln -sf /usr/share/zoneinfo/Asia/Shanghai  /etc/localtime
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/cciaBack.jar"]


docker build -t crm:v1 .
docker run --name crm --restart=always -v /etc/localtime:/etc/localtime -v /home/static:/home/static -v /home/logs:/home/logs -d -p 8088:14000 crm:v1

docker logs -f   id
yum groupinstall chinese-support 
启动：
systemctl start docker
守护进程重启：
systemctl daemon-reload
重启docker服务：
systemctl restart docker / service docker restart
关闭：
docker service docker stop / docker systemctl stop docker

内存管理
echo 1 > /proc/sys/vm/drop_caches
echo 2 > /proc/sys/vm/drop_caches
echo 3 > /proc/sys/vm/drop_caches


方法二
设置虚拟内存方法：

1：free -m查看内存状态
然后你会发现两列，下面的Swap也就是虚拟内存全为0
输入dd if=/dev/zero of=/opt/swap bs=1024 count=1024000
    dd if=/dev/zero of=/opt/swap bs=1024 count=1024000
   #该命令表示在opt分区建立名为swap，大小为1G的虚拟内存文件
    ---
    1024000+0 records in
    1024000+0 records out
1048576000 bytes (1.0 GB) copied, 16.6877 s, 62.8 MB/s

1
2
3
4
5
6
7
接着：将swap文件设置为swap分区文件

chmod 600 /opt/swap    //注意更改swap文件的权限
mkswap /opt/swap
---
Setting up swapspace version 1, size = 1023996 KiB
no label, UUID=fc47f29e-31af-401e-856d-0fec5262179e
1
2
3
4
5
最后：激活swap,启用分区交换文件

swapon /opt/swap
1
现在看下结果

free -m
              total        used        free      shared  buff/cache  available
Mem:          1839          792          874         70     172     851   
Swap:          999          0        999
1
2
3
4
如何关闭虚拟内存呢？

首先停止swap分区 swapoff /opt/swap

 swapoff /opt/swap
 free -m
              total        used        free      shared  buff/cache  available
Mem:            992        191          63          13        738        626
Swap:            0          0          0
1
2
3
4
5
其次删除掉swap文件即可 swapoff /opt/swap
首先看一下磁盘大小

df -h
rm -rf /opt/swap
df -h

```



