[TOC]

##一、使用docker搭建nexus并配置docker私有仓库

####1、 docker从容器里面拷文件到宿主机或从宿主机拷文件到docker容器里面

```shell
docker cp nexus:/nexus-data /home/
docker cp /opt/test.js testtomcat：/usr/local/tomcat/webapps/test/js
```

#### 2、查找镜像、拉取镜像

```shell
docker search nexus
docker pull sonatype/nexus3
docker images
```

####3、启动容器

```shell
docker stop nexus3
docker rm nexus3
docker run -d --name nexus3 \
 --restart=always \
-p 9999:8081 \
-p 7777:7777 \
-v /home/nexus-data:/nexus-data \
sonatype/nexus3
路径必须不断给chmod 777 -R /home/nexus-data
查看容器日志
docker logs -f nexus3
进入容器
docker exec -it nexus3 bash
```

#### 4、配置docker私有仓库

```mysql
把Outreach: Management disable 更改密码 允许匿名访问
选择Repositories，点击Create repository
选择仓库类型 这里选择hosted类型命名为mydocker
vim /etc/docker/daemon.json
  {
    "insecure-registries": ["172.16.2.66:7777" ]
    }
systemctl daemon-reload
systemctl restart docker
docker login -u admin -p 123456 172.16.2.66:7777  #注意这里的端口是配置仓库时选择的端口号
docker tag mysql:latest 172.16.2.66:7777/mysql:0.1
docker push 172.16.2.66:7777/mysql:0.1
docker pull 172.16.2.66:7777/mysql:0.1
docker search 172.16.2.66:7777/mysql:0.1
```

#### 二、使用docker搭建 jenkins蓝海

```shell
--拉取镜像：
docker pull jenkinsci/blueocean
--通过镜像生成容器运行
docker stop jenkins-blueocean
docker rm jenkins-blueocean
docker run \
  -u root \
  --rm \
  -d \
  --name jenkins-blueocean \
  --restart=always \
  -p 9090:8080 \
  -p 50000:50000 \
  -v /var/jenkins_home:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /usr/local/maven3:/usr/local/maven3 \       ---用的本地maven的Path
  -v /usr/java/jdk1.8.0_212:/usr/java/jdk1.8.0_212 \    ---用的本地java的Path
  jenkinsci/blueocean
或
docker stop jenkins-blueocean
docker rm jenkins-blueocean
docker run \
  -d \
  --name jenkins-blueocean \
  --restart=always \
  -p 9090:8080 \
  -p 50000:50000 \
  -v /var/jenkins_home:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /usr/local/maven3:/usr/local/maven3 \    
  -v /usr/java/jdk1.8.0_212:/usr/java/jdk1.8.0_212 \
  jenkinsci/blueocean
宿主机给足/var/jenkins_home路径权限：chmod 777 -R /var/jenkins_home
查看日志：
docker logs -f jenkins-blueocean 
进入容器查看：
docker exec -it jenkins-blueocean /bin/bash
```

三、docker按照的程序的备份

```shell
1、备份当前版本的镜像防止镜像版本变化造成的影响
2、备份挂载之后的文件。这样在重新运行镜像还是以前配置好的配置。
```

