---
layout:     post
title:      Docker 安裝CentOS7 搭建Java Web 運行環境
subtitle:   Docker搭建Java Web 運行環境
date:       2017-11-04
author:     Spencer
header-img: img/post-bg-desk.jpg
catalog: true
tags:
    - Docker
    - CentOS
    - Java
    - Web
---

# 安裝Docker

## 推薦： <http://www.widuu.com/chinese_docker/>

## 安裝Docker之後執行命令，查看所有的鏡像

```shell
docker images

REPOSITORY                  TAG                 IMAGE ID            CREATED             SIZE
swaggerapi/swagger-editor   latest              c1aef7811f23        7 weeks ago         10.7MB
swaggerapi/swagger-ui       latest              784bf5d51a3f        7 weeks ago         8.32MB
node                        latest              36dc1bb7a52b        11 months ago       656MB
nginx                       latest              0d409d33b27e        17 months ago       183MB
```

## 安裝CentOS7

```shell
docker pull centos

Using default tag: latest
latest: Pulling from library/centos
d9aaf4d82f24: Pull complete
Digest: sha256:4565fe2dd7f4770e825d4bd9c761a81b26e49cc9e3c9631c58cfc3188be9505a
Status: Downloaded newer image for centos:latest
```

## 查看所有鏡像

```shell
docker images

REPOSITORY                  TAG                 IMAGE ID            CREATED             SIZE
centos                      latest              d123f4e55e12        11 hours ago        197MB
swaggerapi/swagger-editor   latest              c1aef7811f23        7 weeks ago         10.7MB
swaggerapi/swagger-ui       latest              784bf5d51a3f        7 weeks ago         8.32MB
node                        latest              36dc1bb7a52b        11 months ago       656MB
nginx                       latest              0d409d33b27e        17 months ago       183MB
```

## 啓動Docker容器

```shell
docker run -i -t -v /Users/Spencer/works/software/:/mnt/software/ d123f4e55e12 /bin/bash
```

### 命令詳解

```
docker run <相关参数> <镜像 ID> <初始命令>
其中，相关参数包括：

-i：表示以“交互模式”运行容器
-t：表示容器启动后会进入其命令行
-v：表示需要将本地哪个目录挂载到容器中，格式：-v <宿主机目录>:<容器目录>
```

## 安裝JDK，Tomcat

```shell
[root@cc22a7d4b30e /]# cd opt/
[root@cc22a7d4b30e opt]# tar -zxf /mnt/software/jdk-8u151-linux-x64.tar.gz
[root@cc22a7d4b30e opt]# ls
jdk1.8.0_151
[root@cc22a7d4b30e opt]# mv jdk1.8.0_151/ jdk8/
[root@cc22a7d4b30e opt]# ls
jdk8
[root@cc22a7d4b30e opt]# tar -zxf /mnt/software/apache-tomcat-8.5.23.tar.gz
[root@cc22a7d4b30e opt]# ls
apache-tomcat-8.5.23  jdk8
[root@cc22a7d4b30e opt]# mv apache-tomcat-8.5.23/ tomcat8/
[root@cc22a7d4b30e opt]# ls
jdk8  tomcat8
```

## 配置環境變量

```shell
[root@cc22a7d4b30e opt]# vi ~/.bashrc

export JAVA_HOME=/opt/jdk8
export PATH=$PATH:$JAVA_HOME

[root@cc22a7d4b30e opt]# source  ~/.bashrc
```

## 創建啓動腳本

```shell
[root@cc22a7d4b30e opt]# vi /root/run.sh

#!/bin/bash
source ~/.bashrc
sh /opt/tomcat/bin/catalina.sh run
```

## 修改啓動腳本權限

```shell
chmod u+x /root/run.sh
```

## 退出Docker容器

```shell
exit
```

## 執行命令查看所有的容器

```shell
 docker ps -a

CONTAINER ID        IMAGE                       COMMAND                  CREATED             STATUS                      PORTS               NAMES
cc22a7d4b30e        d123f4e55e12                "/bin/bash"              8 minutes ago       Exited (0) 16 seconds ago                       vibrant_heisenberg
64b57fb99de5        d123f4e55e12                "/bin/bash"              16 minutes ago      Exited (0) 13 minutes ago                       gifted_wescoff
58ec145c6528        d123f4e55e12                "/bin/bash"              17 minutes ago      Created                                         blissful_agnesi
fb7d238ed15e        swaggerapi/swagger-editor   "sh /usr/share/ngi..."   6 weeks ago         Exited (0) 6 weeks ago                          happy_beaver
033d4b79c5cc        swaggerapi/swagger-ui       "sh /usr/share/ngi..."   6 weeks ago         Exited (0) 6 weeks ago                          blissful_wozniak
13d557e9be2d        nginx                       "nginx -g 'daemon ..."   14 months ago       Exited (0) 14 months ago                        webserver
```

## 創建Java Web 鏡像
```shell
docker commit cc22a7d4b30e spencerchang/javaweb:0.1a
sha256:c1cba0221a2b7081f6d9c6308f48505d2fd27bee43c4929f6035fa3ecaf438ff
```

## 查看所有的鏡像

```shell
 docker images
REPOSITORY                  TAG                 IMAGE ID            CREATED             SIZE
spencerchang/javaweb        0.1a                c1cba0221a2b        30 seconds ago      594MB
centos                      latest              d123f4e55e12        11 hours ago        197MB
swaggerapi/swagger-editor   latest              c1aef7811f23        7 weeks ago         10.7MB
swaggerapi/swagger-ui       latest              784bf5d51a3f        7 weeks ago         8.32MB
node                        latest              36dc1bb7a52b        11 months ago       656MB
nginx                       latest              0d409d33b27e        17 months ago       183MB
```

## 啓動Java Web 容器

```shell
 docker run -d -p 58080:8080 --name javaweb spencerchang/javaweb:0.1a /root/run.sh
93a74b95174cdc44667b4094906ce65f1ccc08678f727c0d50f0a02424066b17
```

### 命令詳解

```
-d：表示以“守护模式”执行/root/run.sh脚本，此时 Tomcat 控制台不会出现在输出终端上。
-p：表示宿主机与容器的端口映射，此时将容器内部的 8080 端口映射为宿主机的 58080 端口，这样就向外界暴露了 58080 端口，可通过 Docker 网桥来访问容器内部的 8080 端口了。
--name：表示容器名称，用一个有意义的名称命名即可。
```

## 查看當前正在運行的容器

```shell
 docker ps
CONTAINER ID        IMAGE                       COMMAND             CREATED             STATUS              PORTS                     NAMES
93a74b95174c        spencerchang/javaweb:0.1a   "/root/run.sh"      17 seconds ago      Up 16 seconds       0.0.0.0:58080->8080/tcp   javaweb
```

## 訪問已啓動Tomcat
http://127.0.0.1:58080

## 參考
<https://my.oschina.net/huangyong/blog/372491/>

