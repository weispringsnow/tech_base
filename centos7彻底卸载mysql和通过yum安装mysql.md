[TOC]

###一、彻底卸载mysql

####1、查看是否有安装的mysql

```sh
rpm -qa | grep -i mysql
yum list install mysql*
```

#### 2、卸载mysql安装包

```shell
yum remove -y mysql mysql-server mysql-libs compat-mysql51  mysql80-community-release-el8-1.noarch
yum remove -y mysql-community-release
rpm -e --nodeps mysql-community-libs-5.7.22-1.el7.x86_64
rpm -e –nodeps mysql57-community-release-el7-11.noarch
```

####3、删除残留的mysql目录或文件

```shell
查询mysql安装目录
whereis mysql
find / -name mysql
删除查询出的目录
rm -rf /var/lib/mysql
rm -rf /var/lib/mysql/mysql
```

#### 4、删除mysql配置文件

```shell
rm –rf /usr/my.cnf
rm -rf /root/.mysql_sercret  
```

#### 5、删除mysql开机自启动服务

```shell
删除mysql开机自启动服务
chkconfig --list | grep -i mysql
chkconfig --del mysqld  // 服务名为你设置时候自己设置的名字
```

### 二、安装mysql

#### 1、下载并安装mysql的YUM源：

```shell
mkdir soft
cd soft
wget http://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
rpm -ivh mysql57-community-release-el7-11.noarch.rpm

yum repolist enabled | grep "mysql.*-community.*"
 vim /etc/yum.repos.d/mysql-community.repo
```

####2、接下在就是正式安装mysql了

```shell
yum -y install mysql-community-server
```

#### 3、启动mysql

```shell
service mysqld start
首先停止mysql服务
service mysqld stop
再以不检查权限的方式启动
mysqld --skip-grant-tables &
执行命令以root权限启动
mysqld --user=root --skip-grant-tables &

shell> systemctl start mysqld
开机启动
shell> systemctl enable mysqld
shell> systemctl daemon-reload

```

#### 4、登录 mysql

```shell
 1）查看mysql密码
shell> grep 'temporary password' /var/log/mysqld.log
mysql –uroot
设置密码
UPDATE mysql.user SET authentication_string=PASSWORD('密码') where USER='root';

set global validate_password_policy=0;
set global validate_password_length=4;

ALTER USER 'root'@'localhost' IDENTIFIED BY '密码';
SET PASSWORD FOR root=PASSWORD('密码');
flush privileges; // 刷新设置立即生效
mysql -uroot –p // 会提示输入密码
设置root权限的远程访问
grant all privileges on *.* to root@"%" identified by "密码";
flush privileges; // 刷新设置立即生效

### 安装mysql

#### 1、下载并安装mysql的YUM源：

​```shell
mkdir soft
cd soft
wget http://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
rpm -ivh mysql57-community-release-el7-11.noarch.rpm

yum repolist enabled | grep "mysql.*-community.*"
 vim /etc/yum.repos.d/mysql-community.repo
```
### 三、安装mysql1.8.0

#### 1、下载并安装mysql的YUM源：

```shell
首先卸载mariaDB，命令及操作截图如下：
Step 1 查询当前系统中安装的mariaDB：
rpm -qa | grep mariadb
Step 2 无视依赖卸载已安装的mariaDB：
rpm -e --nodeps mariadb-libs-5.5.60-1.el7_5.x86_64
mkdir soft
cd soft
wget http://mirrors.sohu.com/mysql/MySQL-8.0/mysql-8.0.18-1.el7.x86_64.rpm-bundle.tar、
```

####2、接下在就是正式安装mysql了

```shell
tar -xvf mysql-8.0.18-1.el7.x86_64.rpm-bundle.tar
把tar文件移除或删除
sudo rpm -Uvh mysql-community-{server,client,common,libs}-*-U代表升级
或者
sudo rpm -ivh mysql-community-{server,client,common,libs}-*-i代表安装
遇到问题确实2.18
curl -O http://ftp.gnu.org/gnu/glibc/glibc-2.18.tar.gz
curl -O http://mirrors.ustc.edu.cn/gnu/libc/glibc-2.18.tar.gz
tar zxf glibc-2.18.tar.gz 
cd glibc-2.18/
mkdir build
cd build/
../configure --prefix=/usr
make -j2
make install
```

#### 3、启动mysql

```shell
systemctl start mysqld
systemctl status mysqld
开机启动
service mysqld restart
shell> systemctl enable mysqld
shell> systemctl daemon-reload
```

#### 4、登录 mysql

```mysql
 1）查看mysql密码
shell> grep 'temporary password' /var/log/mysqld.log
mysql –uroot -p
设置密码
ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass4!';
1、查看 mysql 初始的密码策略，
输入语句 “ SHOW VARIABLES LIKE 'validate_password%'; ” 进行查看，
2、首先需要设置密码的验证强度等级，设置 validate_password_policy 的全局参数为 LOW 即可，
输入设值语句 “ set global validate_password_policy=LOW; ” 进行设值，
 set global validate_password.policy=LOW;
3、当前密码长度为 8 ，如果不介意的话就不用修改了，按照通用的来讲，设置为 6 位的密码，设置 validate_password_length 的全局参数为 6 即可，
输入设值语句 “ set global validate_password_length=6; ” 进行设值，
  set global validate_password.length=6;
```
#### 5、设置远程访问
```mysql
-需要使用root权限登录mysql，更新mysql.user表，设置指定用户的Host字段为%，默认一般为localhost。 
1.登录数据库 
mysql -u root -p 
输入密码:
mysql> use mysql;
2.查询host 
mysql> select user,host from user;
3.创建host 
如果没有”%”这个host值,就执行下面这两句: 
mysql> update user set host=’%’ where user=’root’; 
mysql> flush privileges;
4.授权用户
alter user 'root'@'%' identified with mysql_native_password by '密码';
grant all privileges on *.* to 'root'@'%';
flush privileges;
% 允许从任何ip登录 
x.x.x.x 允许从指定ip访问

vim my.cnf
skip-grant-tables
service mysqld restart
```

### 四、docker安装mysql1.8.0

#### 1、拉取镜像、运行镜像

```shell
docker pull mysql
sudo docker login --username=13426381800 registry.cn-hangzhou.aliyuncs.com
sudo docker pull registry.cn-hangzhou.aliyuncs.com/weicx/mysql:20200121
docker stop mysql
docker rm mysql
docker run -d --name mysql \
--restart=always \
-p 33060:3306  \
-v /home/docker/mysql/data:/var/lib/mysql  \
-v /home/docker/mysql/logs:/var/log/mysql \
-v /etc/localtime:/etc/localtime \
-e MYSQL_ROOT_PASSWORD=8ach4b6f9c \
registry.cn-hangzhou.aliyuncs.com/weicx/mysql:20200121

docker stop mysql
docker rm mysql
docker run -d --name mysql \
--restart=always \
-p 33060:3306  \
-v /home/mysql/docker/data:/var/lib/mysql  \
-v /home/mysql/docker/logs:/var/log/mysql \
-v /etc/localtime:/etc/localtime \
-e MYSQL_ROOT_PASSWORD=8ach4b6f9c \
mysql

mkdir -p /home/docker/mysql/data
mkdir -p /home/docker/mysql/logs
查看运行日志
docker logs -f mysql
进入容器
docker exec -it mysql bash
docker cp mysql:/var/lib/mysql/ /home/docker/mysql/data
docker cp mysql:/var/log/mysql/ /home/docker/mysql/logs
```
#### 2、mysql-binlog日志删除

```mysql
第一种方法： 　　
mysql> show binary logs; 查看mysql bin-log日志，除了这个以外的，其它都可以使用删除。 　　
mysql> purge binary logs to 'binlog.000058'; （删除mysql bin-log日志,不包括binlog.000058） 
show binlog events in 'binlog.000001'
```







