---
layout:     post
title:      使用GoAccess搭建Nginx可视化实时监控access日志
subtitle:   Nginx可视化实时监控access日志
date:       2018-11-08
author:     Spencer
header-img: img/post-bg-os-metro.jpg
catalog: true
tags:
    - Linux
    - GoAccess
    - Nginx
---

# 安裝GoAccess
## 先安装依赖包
```shell
 wget http://mirror.centos.org/centos/7/os/x86_64/Packages/ncurses-5.9-14.20130511.el7_4.x86_64.rpm
 wget http://mirror.centos.org/centos/7/os/x86_64/Packages/ncurses-base-5.9-14.20130511.el7_4.noarch.rpm
 wget http://mirror.centos.org/centos/7/os/x86_64/Packages/ncurses-libs-5.9-14.20130511.el7_4.x86_64.rpm
 wget http://mirror.centos.org/centos/7/os/x86_64/Packages/ncurses-devel-5.9-14.20130511.el7_4.x86_64.rpm
 wget http://mirror.centos.org/centos/7/os/x86_64/Packages/ GeoIP-devel-1.5.0-11.el7.x86_64.rpm
 yum install *.rpm
```

## 下载安装GoAccess

```shell
 wget https://tar.goaccess.io/goaccess-1.2.tar.gz
 tar -xzvf goaccess-1.2.tar.gz
 cd goaccess-1.2/
 ./configure --prefix=/usr/local/goaccess --enable-utf8 --enable-geoip=legacy
 make
 make install
```
 
## 修改goaccess.conf
```
time-format %H:%M:%S
date-format %d/%b/%Y
log-format %h %^[%d:%t %^] "%r" %s %b "%R" "%u"
log-file /usr/local/nginx/logs/lmp.access.log
port 7890
```
## 修改Nginx conf
### 启用log_format
```shell
log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';
```
### server{}启用日志,配置访问report.html
```shell
access_log  logs/lmp.access.log  main;
#GoAccess
location /report.html {
	allow 192.168.1.0/24;#根据需要配置访问ip段或具体某个ip
    deny all;
	alias /usr/local/nginx/html/report.html;
}

```
## 重启Nginx
```shell
./nginx -s reload
```
## 启动GoAccess，实时写html文件，websocket推送
```shell
goaccess lmp.access.log -o ../html/report.html --real-time-html --time-format='%H:%M:%S' --date-format='%d/%b/%Y' --log-format=COMBINED
```

# 访问http://127.0.0.1/report.html
![](https://spencerzhang.github.io/resource/gn-1.png)
![](https://spencerzhang.github.io/resource/gn-2.png)
![](https://spencerzhang.github.io/resource/gn-3.png)
![](https://spencerzhang.github.io/resource/gn-4.png)

--EOF--

