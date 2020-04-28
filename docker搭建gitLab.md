[TOC]

####1、 查找镜像、拉取镜像、运行容器

```mysql
1、查找GitLab镜像：
 docker search gitlab
2、拉取gitlab docker镜像:
 docker pull twang2218/gitlab-ce-zh:latest
 #重新打标签
 docker tag twang2218/gitlab-ce-zh:latest gitlab-zh:20200428
3、运行GitLab并运行容器:
  docker stop gitlab
  docker rm gitlab
  docker run -d -p 8443:443 -p 19280:80 -p 19222:22 \
    --privileged=true --name gitlab --restart always  \
    -v /home/gitlab/config:/etc/gitlab \
    -v /home/gitlab/logs:/var/log/gitlab \
    -v /home/gitlab/data:/var/opt/gitlab \
    gitlab-zh:20200428
```

#### 2、修改配置文件

```
1、 编辑 gitlab.rb配置文件:gitlab.rb文件内容默认全是注释:
    vim /home/gitlab/config/gitlab.rb
2、gitlab.rb文件的初步修改:
    external_url 'http://chenglian.gitlab.com/'
    gitlab_rails['gitlab_ssh_host'] = 'chenglian.gitlab.com'
    gitlab_rails['gitlab_shell_ssh_port'] = 19222
3、#重启gitlab容器:
   docker restart gitlab 
4、docker查看gitlab启动日志
  docker logs -f gitlab
5、进入容器后台终端运行:
   docker exec -it gitlab /bin/bash
   #在容器中可执行的命令
   gitlab-ctl stop
   gitlab-ctl reconfigure
   gitlab-ctl restart
   gitlab-ctl tail 
9、gitlab.rb文件中配置邮件mail服务：
    gitlab_rails['smtp_enable'] = true
    gitlab_rails['smtp_address'] = "smtp.qiye.163.com"
    gitlab_rails['smtp_port'] = 25
    gitlab_rails['smtp_user_name'] = "kefu2@fm086.com"
    gitlab_rails['smtp_password'] = "wwwfm086@cn12"
    gitlab_rails['smtp_domain'] = "163.com"
    gitlab_rails['smtp_authentication'] = "login"
    gitlab_rails['smtp_enable_starttls_auto'] = true
    gitlab_rails['smtp_tls'] = false
    gitlab_rails['smtp_openssl_verify_mode'] = 'none'
10、测试邮件是否可以发送：
    docker exec -it gitlab /bin/bash
    #在容器中可执行的命令
    gitlab-rails console
    Notify.test_email('973664512@qq.com', 'Message Subject', 'Message Body').deliver_now
```

####3、登录gitlab

```shell
1、在浏览器中访问：http://chenglian.gitlab.com/
2、设置新密码：自定义，输入新密码后，确认即可
3、重新登录页面
    用户名:(默认)root
    密码：刚才设置的密码
    输入用户名密码，登录即可
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


#### 6、gitlab占用内存过高的优化

```shell
1、主要是针对gitlab.rb文件
  vim /home/gitlab/config/gitlab.rb
2、gitlab.rb的优化修改
 unicorn['worker_timeout'] = 60
 #减少进程数，默认是cpu核数+1
 unicorn['worker_processes'] = 2
 #内存限制为最小100M最大200M
 unicorn['worker_memory_limit_min'] = "100 * 1 << 20"
 unicorn['worker_memory_limit_max'] = "200 * 1 << 20"
 sidekiq['concurrency'] = 4
 #减少postgresql数据缓存
 postgresql['shared_buffers'] = "128MB"
  #减少postgresql数据库并发数
 postgresql['max_worker_processes'] = 4
 3、修改完运行
   docker exec -it gitlab /bin/bash
   #在容器中可执行的命令
   gitlab-ctl stop
   gitlab-ctl reconfigure
   gitlab-ctl restart
```