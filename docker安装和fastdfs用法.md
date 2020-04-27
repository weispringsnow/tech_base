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

docker version
docker info

sudo service docker start

sudo chkconfig docker on

#断电重启

systemctl daemon-reload
systemctl start docker

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

#### 6、对镜像的操作

```shell
docker save -o gyb:0109am1
docker save nexus:20200119 > nexus20200119.tar   将镜像保存成 tar 归档文件
docker load --input qs01.tar   从归档文件加载镜像
docker load < nexus20200119.tar
```
#### 7、从运行中docker容器生成镜像并上传的阿里云私服

```shell
docker commit -m "nexus 20200119" -a "weicx" 4e852d7b09c5 nexus:20200119
登录到阿里云上传：
sudo docker login --username=13426381800 registry.cn-hangzhou.aliyuncs.com
docker tag 37bb9c63c8b2 registry.cn-hangzhou.aliyuncs.com/weicx/nexus3:20200119
docker push registry.cn-hangzhou.aliyuncs.com/weicx/nexus3:20200119
docker pull registry.cn-hangzhou.aliyuncs.com/weicx/nexus3:20200119
```

#### 8、docker 配置镜像加速

```shell
docker commit -m "nexus 20200119" -a "weicx" 4e852d7b09c5 nexus:20200119
登录到阿里云上传：
sudo docker login --username=13426381800 registry.cn-hangzhou.aliyuncs.com
docker tag 37bb9c63c8b2 registry.cn-hangzhou.aliyuncs.com/weicx/nexus3:20200119
docker push registry.cn-hangzhou.aliyuncs.com/weicx/nexus3:20200119
docker pull registry.cn-hangzhou.aliyuncs.com/weicx/nexus3:20200119
```
#### 9、fastdfs 安装和使用

```shell
docker pull registry.cn-hangzhou.aliyuncs.com/weicx/fastdfs:20200121
docker tag registry.cn-hangzhou.aliyuncs.com/weicx/fastdfs:20200121 delron/fastdfs
-- 跟踪
docker stop tracker
docker rm tracker
docker run -d --network=host \
--name tracker \
--restart=always \
-v /home/docker/fdfs/tracker:/var/fdfs \
delron/fastdfs
-- 存储
docker stop storage
docker rm storage
docker run -d --network=host \
--restart=always \
--name storage \
-e TRACKER_SERVER=172.16.2.246:22122 \
-e GROUP_NAME=group1 \
-e PORT=23000 \
-v /home/docker/fdfs/storage:/var/fdfs \
delron/fastdfs
创建一些目录：
mkdir -p /home/docker/fdfs/tracker
mkdir -p /home/docker/fdfs/storage
```
#### 10、错误处理

```shell
错误
Error response from daemon: endpoint with name gitlab already exists in network bridge
清理此容器的网络占用
格式：docker network disconnect --force 网络模式 容器名称
示例：docker network disconnect --force bridge gitlab
如果清除网络占用后,启动报
docker: Error response from daemon: Conflict. The container name "/gitlab" is already in use by container "98a83b26615c732c91a8ad6e4653122a9cc54a24de48f27081f803af2d75b84b". You have to remove (or rename) that container to be able to reuse that name.
则先删除已停止的容器
 docker rm $(docker ps -a -q)
在清除一次网络占用
```

####11、HikariCP 连接池

```
HikariCP 连接池
Spring Boot 如果发现 Tomcat 连接池不可用，则尝试选择 HikariCP 作为默认连接池。HikariCP 连接池常用的属性：
属性	描述	默认值
dataSourceClassName	JDBC 驱动程序提供的 DataSource 类的名称，如果使用了jdbcUrl则不需要此属性	-
jdbcUrl	数据库连接地址	-
username	数据库账户，如果使用了jdbcUrl则需要此属性	-
password	数据库密码，如果使用了jdbcUrl则需要此属性	-
autoCommit	是否自动提交事务	true
connectionTimeout	连接超时时间（毫秒），如果在没有连接可用的情况下等待超过此时间，则抛出 SQLException	30000（30秒）
idleTimeout	空闲超时时间（毫秒），只有在minimumIdle<maximumPoolSize时生效，超时的连接可能被回收，数值 0 表示空闲连接永不从池中删除	600000（10分钟）
maxLifetime	连接池中的连接的最长生命周期（毫秒）。数值 0 表示不限制	1800000（30分钟）
connectionTestQuery	连接池每分配一条连接前执行的查询语句（如：SELECT 1），以验证该连接是否是有效的。如果你的驱动程序支持 JDBC4，HikariCP 强烈建议我们不要设置此属性	-
minimumIdle	最小空闲连接数，HikariCP 建议我们不要设置此值，而是充当固定大小的连接池	与maximumPoolSize数值相同
maximumPoolSize	连接池中可同时连接的最大连接数，当池中没有空闲连接可用时，就会阻塞直到超出connectionTimeout设定的数值	10
poolName	连接池名称，主要用于显示在日志记录和 JMX 管理控制台中	auto-generated
```

