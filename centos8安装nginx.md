[TOC]

#### 1、安装nginx 并部署

```shell
1.$ yum install gcc-c++
2.$ yum install -y pcre pcre-devel
3.$ yum install -y zlib zlib-devel
4.使用wget命令下载（推荐）。确保系统已经安装了wget，如果没有安装，执行 yum install wget 安装。
  $ wget -c https://nginx.org/download/nginx-1.12.0.tar.gz
5.解压
  $ tar -zxvf nginx-1.12.0.tar.gz
  $ cd nginx-1.12.0
6.配置 使用默认配置
  $ ./configure
7.编译安装
  $ make
  $ make install
    查找安装路径：
  $ whereis nginx
8.启动、停止nginx
  $ cd /usr/local/nginx/sbin/
  $ ./nginx 
  $ ./nginx -s stop
  $ ./nginx -s quit
  $ ./nginx -s reload
9.启动、停止nginx
 ps -ef | grep nginx
 find / -name nginx
```
#### 2、错误报告

```shell
1.报错一
cc1：所有的警告都被当作是错误
make[1]: *** [objs/Makefile:431：objs/src/core/ngx_murmurhash.o] 错误 1
make[1]: 离开目录“/opt/nginx-1.8.1”
make: *** [Makefile:8：build] 错误 2
原因：将警告当成错误处理
解决错误1：进入到nginx-1.6.3目录下（解压的目录）
找到当前目录下找到objs文件夹，并进入，打开文件Makefile,找到有一下内容的这行：
# vim objs/Makefile
CFLAGS =  -pipe  -O -W -Wall -Wpointer-arith -Wno-unused-parameter -Werror -g　
-Werror: gcc将所有的警告当成错误进行处理把这行内容中的 “-Werror”去掉
2.报错二
src/os/unix/ngx_user.c: In function ‘ngx_libc_crypt’:
src/os/unix/ngx_user.c:36:7: error: ‘struct crypt_data’ has no member named ‘current_salt’
cd.current_salt[0] = ~salt[0];
  ^
make[1]: *** [objs/Makefile:732: objs/src/os/unix/ngx_user.o] Error 1
make[1]: Leaving directory '/usr/local/java/nginx-1.9.9'
make: *** [Makefile:8: build] Error 2
解决方案：
找到目录/usr/local/java/nginx-1.9.0/src/os/unix/ngx_user.c 文件。
将这行代码注释掉即可。cd.current_salt[0] = ~salt[0]; （亲测）
```
#### 3、错误：为仓库 ‘appstream‘ 下载元数据失败 : Cannot prepare internal mirrorlist: No URLs in mirrorlist

```shell
cd /etc/yum.repos.d
mkdir back
mv CentOS-Linux-* back
wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-vault-8.5.2111.repo
yum makecache
问题解决完毕 
```



