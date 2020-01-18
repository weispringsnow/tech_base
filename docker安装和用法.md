[TOC]

####1、 安装 Docker

```mysql
卸载:
1.查询安装过的包
yum list installed | grep docker
2.删除安装的软件包
yum -y remove docker-engine.x86_64 
3.删除镜像/容器等
rm -rf /var/lib/docker
CentOS7 系统 CentOS-Extras 库中已带 Docker，可以直接安装：
$ sudo yum install docker			##不是最新版本
#最新版安装
$ sudo yum install -y yum-utils device-mapper-persistent-data lvm2
$ sudo yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo 
sudo yum -y install docker-ce 

```

#### 2、查看docker版本、启动docker、设置随系统启动

```shell
docker version
docker info

sudo service docker start

sudo chkconfig docker on
```

####3、Docker基本操作

```shell
容器操作
docker [run|start|stop|restart|kill|rm|pause|unpause]
•	run/create[镜像名]：  创建一个新的容器并运行一个命令
•	start/stop/restart[容器名]：启动/停止/重启一个容器
•	kill [容器名]： 直接杀掉容器，不给进程响应时间
•	rm[容器名]：删除已经停止的容器
•	pause/unpause[容器名]：暂停/恢复容器中的进程
docker [ps|inspect|exec|logs|export|import]
•	ps：查看容器列表（默认查看正在运行的容器，-a查看所有容器）
•	inspect[容器名]：查看容器配置元数据
•	exec -it [容器名] /bin/bash：进入容器环境中交互操作
•	logs --since="2019-02-01" -f --tail=10 [容器名]:查看容器日志 
•	cp path1 [容器名]:path 容器与主机之间的数据拷贝
•	export -o test.tar [容器名] / docker export [容器名]>test.tar : 文件系统作为一个tar归档文件
•	import test.tar [镜像名:版本号]:导入归档文件，成为一个镜像
镜像操作
docker images|rmi|tag|build|history|save|load]
•	images：列出本地镜像列表
•	rmi [镜像名：版本]：删除镜像
•	tag [镜像名：版本] [仓库]/[镜像名：版本]：标记本地镜像，将其归入某一仓库
•	build -t [镜像名：版本] [path]：Dockerfile 创建镜像
•	history [镜像名：版本]: 查看指定镜像的创建历史
•	save -o xxx.tar [镜像名：版本] /  save [镜像名：版本]>xxx.tar : 将镜像保存成 tar 归档文件
•	load --input  xx.tar / docker load<xxx.tar : 从归档文件加载镜像
```

#### 4、镜像与容器原理及用法探究

```shell
history命令查看镜像层
例：docker history hello-world
查看镜像文件
镜像存放在imagedb里
一般在/var/lib/docker/image/overlay2/imagedb/content/sha256下
打开一个镜像文件查看其内容：
cat f09fe80eb0e75e97b04b9dfb065ac3fda37a8fac0161f42fca1e6fe4d0977c80
```

#### 5、容器创建详解

```shell
docker run -it --name centos centos /bin/bash（前台进程） 
------------------------exit退出也关闭容器; Ctrl+P+Q退出不关闭容器 
后台启动容器：docker run -d --name nginx nginx 
进入已运行的容器：
docker exec -it nginx /bin/bash
查看容器的元数据： docker inspect nginx  
绑定容器端口到主机： 
docker run -d -p 8080:80 --name nginx nginx:latest
挂载主机文件目录到容器内：
 docker run -dit -v /root/peter_dir/:/pdir --name cent centos
复制主机文件到容器内：
docker cp anaconda-ks.cfg cent:/var 
```

