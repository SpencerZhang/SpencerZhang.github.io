---
layout:     post
title:      CATALINA_OPTS与JAVA_OPTS的区别
subtitle:   vm参数设置问题
date:       2018-01-22
author:     Spencer
header-img: img/post-bg-desk.jpg
catalog: true
tags:
    - Java
    - Tomcat
    - vm
---

# vm参数设置CATALINA_OPTS与JAVA_OPTS的区别

## IDE里配置tomcat8.5.24启动失败

### tomcat catalina.sh里设置了
```
export JAVA_OPTS="-server -Xms2g -Xmx2g -Xmn1g -Xss512K -XX:MetaspaceSize=512m 
 -XX:MaxMetaspaceSize=1g -XX:MaxDirectMemorySize=512M"
```
#### 把设置的JAVA_OPTS参数注释掉，启动正常。但是为什么呢？

我们来看下catalina.sh里对这两个参数的解释
```
#   CATALINA_OPTS   (Optional) Java runtime options used when the "start",
#                   "run" or "debug" command is executed.
#                   Include here and not in JAVA_OPTS all options, that should
#                   only be used by Tomcat itself, not by the stop process,
#                   the version command etc.
#                   Examples are heap size, GC logging, JMX ports etc.

#   JAVA_OPTS       (Optional) Java runtime options used when any command
#                   is executed.
#                   Include here and not in CATALINA_OPTS all options, that
#                   should be used by Tomcat and also by the stop process,
#                   the version command etc.
#                   Most options should go into CATALINA_OPTS.
```

#### 然后又在网络查询是否有同样遇到这个问题。
找到了<https://www.zhihu.com/question/35626538#answer-21186674/>,其中R大还做了另外2个环境变量参数以及优先级。

推荐R大 <https://www.zhihu.com/people/rednaxelafx/activities/>

--EOF--