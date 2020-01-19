[TOC]

## 一、网络设置

####1、配置DNS

```sh
vim   /etc/resolv.conf
nameserver 192.168.0.1 
nameserver 8.8.8.8
nameserver 8.8.4.4
```

#### 2、配置网关

```shell
route add default gw 192.168.1.1 dev eno*
这样就把网关修改为192.168.1.1了，这种修改只是临时的，当你重新启动系统或网卡之后，还是会变回原来的网关。要想永久性修改，就要修改/etc/sysconfig/network 这个文件，这个文件的主要内容如下（你的文件中没有的项，你可以手动添加）：

vim  /etc/sysconfig/network
NETWORKING=yes #表示系统是否使用网络，一般设置为yes。如果设为no，则不能使用网络。
HOSTNAME=centos #设置本机的主机名，这里设置的主机名要和/etc/hosts中设置的主机名对应
GATEWAY=192.168.1.1 #设置本机连接的网关的IP地址。
```

####3、配置ip地址

```shell
vim  /etc/sysconfig/network-scripts/ifcfg-eno*
HWADDR=00:0C:29:8D:24:73
 TYPE=Ethernet
 BOOTPROTO=static  #启用静态IP地址
 DEFROUTE=yes
 PEERDNS=yes
 PEERROUTES=yes
 IPV4_FAILURE_FATAL=no
 IPV6INIT=yes
 IPV6_AUTOCONF=yes
 IPV6_DEFROUTE=yes
 IPV6_PEERDNS=yes
 IPV6_PEERROUTES=yes
 IPV6_FAILURE_FATAL=no
 NAME=eno16777736
 UUID=ae0965e7-22b9-45aa-8ec9-3f0a20a85d11
 ONBOOT=yes  #开启自动启用网络连接
 IPADDR0=192.168.21.128  #设置IP地址
 PREFIXO0=24  #设置子网掩码
 GATEWAY0=192.168.21.2  #设置网关
 DNS1=8.8.8.8  #设置主DNS
 DNS2=8.8.4.4  #设置备DNS
 :wq!  #保存退出
```

#### 4、设置主机名为www

```shell
vim /etc/hostname #编辑配置文件
www   #修改localhost.localdomain为www
vim /etc/hosts #编辑配置文件
127.0.0.1   localhost  www   #修改localhost.localdomain为www
```

#### 5、重新启动服务

```shell
ip link set eth0 upi          # 开启网卡
ip link set eth0 down         # 关闭网卡
service network restart
```

#### 6、补充命令

```shell
ip  [选项]  操作对象{link|addr|route...}

# ip link show                  # 显示网络接口信息
# ip link set eth0 upi          # 开启网卡
# ip link set eth0 down         # 关闭网卡
# ip link set eth0 promisc on   # 开启网卡的混合模式
# ip link set eth0 promisc offi # 关闭网卡的混个模式
# ip link set eth0 txqueuelen 1200    # 设置网卡队列长度
# ip link set eth0 mtu 1400     # 设置网卡最大传输单元
# ip addr show                  # 显示网卡IP信息
# ip addr add 192.168.0.1/24 dev eth0 # 设置eth0网卡IP地址192.168.0.1
# ip addr del 192.168.0.1/24 dev eth0 # 删除eth0网卡IP地址

# ip route list                 # 查看路由信息
# ip route add 192.168.4.0/24  via  192.168.0.254 dev eth0 # 设置192.168.4.0网段的网关为192.168.0.254,数据走eth0接口
# ip route add default via  192.168.0.254  dev eth0    # 设置默认网关为192.168.0.254
# ip route del 192.168.4.0/24    # 删除192.168.4.0网段的网关
# ip route del default    # 删除默认路由
```
##二、防火墙和端口

####1、查看对外开放的端口状态

```sh
yum安装rzsz
yum -y install lrzsz

查询已开放的端口： 
netstat -anp
查询指定端口是否已开：
firewall-cmd --query-port=666/tcp
提示 yes，表示开启；no表示未开启。

```

#### 2、查看防火墙状态

```shell
查看防火墙状态 ：
systemctl status firewalld
开启防火墙 ：
systemctl start firewalld  
关闭防火墙 ：
systemctl stop firewalld
开启防火墙 service firewalld start 
若遇到无法开启：
先用：
systemctl unmask firewalld.service 
然后：
systemctl start firewalld.service
```

####3、对外开发端口

```shell
查看想开的端口是否已开：
firewall-cmd --query-port=6379/tcp
添加指定需要开放的端口：
firewall-cmd --add-port=123/tcp --permanent
重载入添加的端口：
firewall-cmd --reload
查询指定端口是否开启成功：
firewall-cmd --query-port=123/tcp
移除指定端口：
firewall-cmd --permanent --remove-port=123/tcp
```

#### 4、安装iptables-services ：

```shell
yum install iptables-services 
```

#### 5、关闭防火墙

```shell
查看防火墙状态：
systemctl status firewalld.service
绿的running表示防火墙开启
执行关闭命令： 
systemctl stop firewalld.service
再次执行查看防火墙命令：
systemctl status firewalld.service

执行开机禁用防火墙自启命令  ：
systemctl disable firewalld.service
关于防火墙的其他命令：
启动：
systemctl start firewalld.service
防火墙随系统开启启动  ：
systemctl enable firewalld.service
```

#### 6、网络重启

```shell
service network restart #重启网络
```


