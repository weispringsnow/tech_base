[TOC]

#### 1、安装redis并部署

```shell
 docker pull redis:latest
 docker images
 docker stop redis
 docker rm redis
 docker run -d --name redis --restart=always -p 6379:6379 redis --requirepass 123456
 docker exec -it redis /bin/bash
```

