[TOC]

####1、 安装subversion

```shell
# yum install subversion
# which svnserve
# svnserve --version
```

#### 2、安装maven

```shell
mkdir -p /temp/tools
cd /temp/tools/
wget https://mirrors.cnnic.cn/apache/maven/maven-3/3.6.3/binaries/apache-maven-3.6.3-bin.tar.gz --no-check-certificate
tar zxf apache-maven-3.6.3-bin.tar.gz 
mv apache-maven-3.6.3 /usr/local/maven3

vi /etc/profile
#在适当的位置添加
export M2_HOME=/usr/local/maven3
export PATH=$PATH:$JAVA_HOME/bin:$M2_HOME/bin
保存退出后运行下面的命令使配置生效，或者重启服务器生效。
source /etc/profile
验证版本
mvn -v
出现maven版本即成功
```

####3、安装expect

```shell
https://pan.baidu.com/s/1wcWALr2eNsJevASYbVaLAw#list/path=%2F
密码: i4ec 
#rpm -ivh tcl-8.5.7-6.el6.x86_64.rpm \
expect-5.44.1.15-5.el6_4.x86_64.rpm
# which expect


set timeout ：定义timeout的时间为-1(永不超时)，也可以输入正常数值例如100（如果脚本命令都执行正常情况下，等待多少秒后退出脚本）
spawn ：后面跟具体要执行的命令
expect ：定义字符内容用于匹配上面spawn后面执行命令的返回内容
send ：如果spawn后面运行命令的返回内容，匹配expect上面定义的。就发送send定义的内容到上面（相当于输入了密码）。
; exp_continue ：处于expect代码段内，表示重新回到expect开始出执行命令。这样就会一直循环下去，当autossh断开重连的时候会重新提示输入密码，expect匹配到后会自动输入密码。
```

#### 4、expect下命令不能解析通配符*的问题

```shell
spawn bash -c "scp *.zip $USER@$HOST:$BOOKFILE"
```

#### 5、Linux下subversion的操作

```shell
1、Linux下保存的SVN帐号及密码
cd ~/.subversion/auth/svn.simple
ls  vi
修改密码要删除这个文件
rm -rf ~/.subversion/auth/svn.simple
```

#### 6、linux下安装了dos2unix

```shell
yum -y install dos2unix*
dos2unix 脚本名  
```
