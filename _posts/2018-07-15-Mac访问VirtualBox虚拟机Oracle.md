---
layout:     post
title:      Mac访问VirtualBox虚拟机Oracle
subtitle:   网络Nat模式
date:       2018-07-15
author:     Spencer
header-img: img/post-bg-coffee.jpeg
catalog: true
tags:
    - Mac
    - VirtualBox
    - Oracle
---

# Mac访问VirtualBox虚拟机Oracle，网络Nat模式,

## 第一步 选择端口转发
![](https://spencerzhang.github.io/resource/15315892439164.jpg)


## 第二步 设置转发规则
我的配置就一条规则：TCP 协议，物理机ip 端口1521 转发到虚拟机ip 端口1521
![](https://spencerzhang.github.io/resource/15315892515031.jpg)


## Tomcat 数据源配置

```xml
<Resource auth="Container" driverClassName="oracle.jdbc.driver.OracleDriver" name="jdbc/xxx" password="xxx" type="javax.sql.DataSource" url="jdbc:oracle:thin:@127.0.0.1:1521/orcl" username="xxx"/>
```

或者

```xml
<Resource auth="Container" driverClassName="oracle.jdbc.driver.OracleDriver" name="jdbc/xxx" password="xxx" type="javax.sql.DataSource" url="jdbc:oracle:thin:@127.0.0.1:1521:orcl" username="xxx"/>
```

--EOF--