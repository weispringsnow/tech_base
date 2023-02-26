[TOC]

#### 1、在宿主机创建svn相关目录

```shell
mkdir -p /data/svn/repo
chmod 777 /data/svn/repo

创建放svn配置及仓库的目录
仓库给予权限为777，否则在svnadmin无法创建目录
```

#### 2、拉取最新镜像

```shell
docker pull elleflorio/svn-server
```

#### 3、运行容器

```shell
docker run --privileged=true -d --name svn-server -p 8000:80 -p 8001:3690  \
-v /data/svn/repo:/home/svn  elleflorio/svn-server 
 
1、 端口8000、8001可修改为自己要使用的端口；
2、 8000对应于容器内httpd的80端口，也是用于外部访问svn的端口
```
#### 4、将容器中的svn配置文件 cp 至宿主机

```shell
docker cp svn-server:/etc/subversion /data/svn/repo/svnconfig
```
#### 5、停止并移除容器

```shell
docker stop svn-server
docker rm svn-server
```
#### 6、安装 SVN 服务器并挂载配置文件

```shell
docker run --privileged=true -d --name svn-server -p 8000:80 -p 8001:3690 \
-v /data/svn/repo:/home/svn \
-v /data/svn/repo/svnconfig/subversion-access-control:/etc/subversion/subversion-access-control \
-v /data/svn/repo/svnconfig/passwd:/etc/subversion/passwd  elleflorio/svn-server
```
#### 7、配置 svnadmin

```shell
http://ays.fit:8000/svnadmin/settings.php
访问http://ays.fit:8000/svnadmin/settings.php 设置 svnadmin 默认密码admin/admin

Subversion authorization file： /etc/subversion/subversion-access-control
User authentication file (SVNUserFile)：/etc/subversion/passwd
Parent directory of the repositories (SVNParentPath)：/home/svn
Subversion client executable：/usr/bin/svn
Subversion admin executable：/usr/bin/svnadmin
```
#### 8、执行容器命令和删除按钮修复

```shell
docker exec -it svn-server /bin/sh

修改配置文件
打开/opt/svnadmin/data，进入data目录，找到config.ini文件
找到[GUI]标签，默认RepositoryDeleteEnabled标签为false
改为true，顺便导出代码库的值RepositoryDumpEnabled也可以改为true
```
