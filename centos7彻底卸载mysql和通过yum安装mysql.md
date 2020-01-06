[TOC]

###彻底卸载mysql

####1、查看是否有安装的mysql

```sh
rpm -qa | grep -i mysql
yum list install mysql*
```

#### 2、卸载mysql安装包

```shell
yum remove mysql mysql-server mysql-libs compat-mysql51
yum remove mysql-community-release
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

### 安装mysql

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
```
#### 4、MySQL8允许远程连接

```mysql
-- 需要使用root权限登录mysql，更新mysql.user表，设置指定用户的Host字段为%，默认一般为localhost。 
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
flush privileges;
% 允许从任何ip登录 
x.x.x.x 允许从指定ip访问
```










