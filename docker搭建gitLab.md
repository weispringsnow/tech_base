[TOC]

####1、 查找镜像、拉取镜像、运行容器

```mysql
1、查找GitLab镜像:
docker search gitlab
2、拉取gitlab docker镜像:
docker pull gitlab/gitlab-ce:latest
3、运行GitLab并运行容器:
  sudo docker run -d  \
  -p 443:443 -p 80:80 -p 222:22 \
  --name gitlab --restart always \
  -v /srv/gitlab/config:/etc/gitlab \
  -v /srv/gitlab/logs:/var/log/gitlab \
  -v /srv/gitlab/data:/var/opt/gitlab \
  --privileged=true gitlab/gitlab-ce:latest
```

#### 2、修改配置文件

```
1、 编辑 gitlab.rb配置文件:gitlab.rb文件内容默认全是注释
    vim /srv/gitlab/config/gitlab.rb
2、添加external_url:配置http协议所使用的访问地址,不加端口号默认为80
    external_url 'http://172.16.2.66'
3、添加访问地址和端口:配置ssh协议所使用的访问地址和端口
    gitlab_rails['gitlab_ssh_host'] = '172.16.2.66'
    此端口是run时22端口映射的222端口
    gitlab_rails['gitlab_shell_ssh_port'] = 222
4、#保存配置文件并退出
5、# 重启gitlab容器
   docker restart gitlab
6、docker查看gitlab启动日志
  docker logs -f gitlab
7、附上配置信息及截图：
    # 配置http协议所使用的访问地址,不加端口号默认为80
    external_url 'http://172.16.2.66'
    # 配置ssh协议所使用的访问地址和端口
    gitlab_rails['gitlab_ssh_host'] = '172.16.2.66'
    gitlab_rails['gitlab_shell_ssh_port'] = 222
```

####3、登录gitlab

```shell
1、在浏览器中访问：http://172.16.2.66/
    此时项目的仓库地址就变了。如果ssh端口地址不是默认的22，就会加上ssh:// 协议头
    打开浏览器输入ip地址(因为我的gitlab端口为80，所以浏览器url不用输入端口号，如果端口号不是80，则打开为：ip:端口号)
2、设置新密码：自定义，输入新密码后，确认即可
3、重新登录页面
    用户名:(默认)root
    密码：刚才设置的密码
    输入用户名密码，登录即可
4、gitlab首页展示
5、创建一个项目
```

#### 4、用户使用

```shell
1.下载git.exe
2、设置ssh
   打开本地git bash,使用如下命令生成ssh公钥和私钥对
   ssh-keygen -t rsa -C 'weicx@gmail.com'
   然后一路回车(-C 参数是你的邮箱地址)
3、然后输入命令：
   cat ~/.ssh/id_rsa.pub
  ~表示用户目录，比如我的windows就是C:\Users\Administrator，并复制其中的内容
4、把公钥添加到远程gitlab仓库中
   打开gitlab,找到Profile Settings–>SSH Keys—>Add SSH Key,并把上一步中复制的内容粘贴到Key所对应的文本框
5、查看ssh key
```

#### 5、从gitlab克隆代码

```shell
1、选择项目
2、复制项目；链接地址
3、进入拉取代码到本地的路径下面
4、右键选择->Git Bash Here
5、设置用户名和邮箱
    git config --global user.name "guobin_wb"
    git config --global user.email "gbly02@gamil.com"
6、查看git全局配置信息
    git config --global --list
7、克隆项目
    git clone ssh://git@172.16.2.66:222/root/gitlab-test.git
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

