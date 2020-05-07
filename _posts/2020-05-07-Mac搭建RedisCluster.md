---
layout:     post
title:      Mac搭建Redis Cluster
subtitle:   Redis Cluster
date:       2020-05-07
author:     Spencer
header-img: img/post-bg-desk.jpeg
catalog: true
tags:
    - Redis Cluster
    - Mac
---

# Mac搭建Redis Cluster
## 首先需要安装Redis，本次我使用的Redis-6.0.1。

参考：[官网集群文档](https://redis.io/topics/cluster-tutorial)

Redis 集群由多个运行在集群模式（cluster mode）下的 Redis 实例组成， 实例的集群模式需要通过配置来开启， 开启集群模式的实例将可以使用集群特有的功能和命令。

以下是一个包含了最少选项的集群配置文件示例：

```
port 7000
cluster-enabled yes
cluster-config-file nodes-7000.conf
cluster-node-timeout 5000
appendonly yes
```

我直接copy的redis.conf.

创建集群目录：

```
mkdir -p redis-cluster/7000
mkdir -p redis-cluster/7001
mkdir -p redis-cluster/7002
mkdir -p redis-cluster/7003
mkdir -p redis-cluster/7004
mkdir -p redis-cluster/7005

cp redis.conf ~/xxx/redis-cluster/7000/7000.conf
cp redis.conf ~/xxx/redis-cluster/7001/7001.conf
cp redis.conf ~/xxx/redis-cluster/7002/7002.conf
cp redis.conf ~/xxx/redis-cluster/7003/7003.conf
cp redis.conf ~/xxx/redis-cluster/7004/7004.conf
cp redis.conf ~/xxx/redis-cluster/7005/7005.conf
```

7000.conf ... 7005.conf 配置文件里配置实例里注意修改port 及 cluster-config-file那个参数。

启动redis：

```
redis-server ~/xxx/redis-cluster/7000/7000.conf & redis-server ~/xxx/redis-cluster/7001/7001.conf & redis-server ~/xxx/redis-cluster/7002/7002.conf & redis-server ~/xxx/redis-cluster/7003/7003.conf & redis-server ~/xxx/redis-cluster/7004/7004.conf & redis-server ~/xxx/redis-cluster/7005/7005.conf
```

查看是否启动成功：

```
ps -ef |grep redis
502 42532 38791   0 10:16AM ttys000    0:00.01 redis-server 127.0.0.1:7000 [cluster]
502 42533 38791   0 10:16AM ttys000    0:00.01 redis-server 127.0.0.1:7001 [cluster]
...
```



## 创建集群

现在已经有了六个正在运行中的 Redis 实例， 接下来需要使用这些实例来创建集群， 并为每个节点编写配置文件。

通过使用 Redis 集群命令行工具 `redis-trib` ， 编写节点配置文件的工作可以非常容易地完成： `redis-trib` 位于 Redis 源码的 `src` 文件夹中， 它是一个 Ruby 程序， 这个程序通过向实例发送特殊命令来完成创建新集群， 检查集群， 或者对集群进行重新分片（reshared）等工作。

执行以下命令来创建集群：

```
./redis-trib.rb create --replicas 1 127.0.0.1:7000 127.0.0.1:7001
WARNING: redis-trib.rb is not longer available!
You should use redis-cli instead.

All commands and features belonging to redis-trib.rb have been moved
to redis-cli.
In order to use them you should call redis-cli with the --cluster
option followed by the subcommand name, arguments and options.

Use the following syntax:
redis-cli --cluster SUBCOMMAND [ARGUMENTS] [OPTIONS]

Example:
redis-cli --cluster create 127.0.0.1:7000 127.0.0.1:7001 --cluster-replicas 1

To get help about all subcommands, type:
redis-cli --cluster help
```

根据提示的建议：

```
redis-cli --cluster create 127.0.0.1:7000 127.0.0.1:7001 127.0.0.1:7002 127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005 --cluster-replicas 1
```

命令的意义如下：

- 命令 `create` ， 表示创建一个新的集群。
- 选项 `--replicas 1` 表示为集群中的每个主节点创建一个从节点。
- 之后跟着的其他参数则是实例的地址列表， 程序使用这些地址所指示的实例来创建新集群。

简单来说， 以上命令的意思就是创建一个包含三个主节点和三个从节点的集群。

接着会打印出一份预想中的配置给你看， 如果你觉得没问题的话， 就可以输入 `yes` ， 会将配置应用到集群当中：

```
>>> Performing hash slots allocation on 6 nodes...
Master[0] -> Slots 0 - 5460
Master[1] -> Slots 5461 - 10922
Master[2] -> Slots 10923 - 16383
Adding replica 127.0.0.1:7004 to 127.0.0.1:7000
Adding replica 127.0.0.1:7005 to 127.0.0.1:7001
Adding replica 127.0.0.1:7003 to 127.0.0.1:7002
>>> Trying to optimize slaves allocation for anti-affinity
[WARNING] Some slaves are in the same host as their master
M: a240e263230744e308ec64b5f10196e3eb3a2810 127.0.0.1:7000
   slots:[0-5460] (5461 slots) master
M: 2e0b18c44e4a8d45eff71a0b9bc0f124b76b4ff6 127.0.0.1:7001
   slots:[5461-10922] (5462 slots) master
M: 08b23c66553bab971d8135bc7192d0a7645ca6ff 127.0.0.1:7002
   slots:[10923-16383] (5461 slots) master
S: 92c3594e0a713d35cfd2d656558202f5df7a0b4f 127.0.0.1:7003
   replicates 2e0b18c44e4a8d45eff71a0b9bc0f124b76b4ff6
S: 4c238467fc7614d52c05d1ed618a494a6aee42ad 127.0.0.1:7004
   replicates 08b23c66553bab971d8135bc7192d0a7645ca6ff
S: b2c06632c411871937cd7165bac6ffc3782e99f1 127.0.0.1:7005
   replicates a240e263230744e308ec64b5f10196e3eb3a2810
Can I set the above configuration? (type 'yes' to accept): yes
```

输入 `yes` 并按下回车确认之后， 集群就会将配置应用到各个节点， 并连接起（join）各个节点 —— 也即是， 让各个节点开始互相通讯：

```
>>> Nodes configuration updated
>>> Assign a different config epoch to each node
>>> Sending CLUSTER MEET messages to join the cluster
Waiting for the cluster to join
...
>>> Performing Cluster Check (using node 127.0.0.1:7000)
M: a240e263230744e308ec64b5f10196e3eb3a2810 127.0.0.1:7000
   slots:[0-5460] (5461 slots) master
   1 additional replica(s)
S: b2c06632c411871937cd7165bac6ffc3782e99f1 127.0.0.1:7005
   slots: (0 slots) slave
   replicates a240e263230744e308ec64b5f10196e3eb3a2810
M: 2e0b18c44e4a8d45eff71a0b9bc0f124b76b4ff6 127.0.0.1:7001
   slots:[5461-10922] (5462 slots) master
   1 additional replica(s)
S: 92c3594e0a713d35cfd2d656558202f5df7a0b4f 127.0.0.1:7003
   slots: (0 slots) slave
   replicates 2e0b18c44e4a8d45eff71a0b9bc0f124b76b4ff6
S: 4c238467fc7614d52c05d1ed618a494a6aee42ad 127.0.0.1:7004
   slots: (0 slots) slave
   replicates 08b23c66553bab971d8135bc7192d0a7645ca6ff
M: 08b23c66553bab971d8135bc7192d0a7645ca6ff 127.0.0.1:7002
   slots:[10923-16383] (5461 slots) master
   1 additional replica(s)
```

一切正常的话， 将输出以下信息：

```
[OK] All nodes agree about slots configuration.
>>> Check for open slots...
>>> Check slots coverage...
[OK] All 16384 slots covered.
```

此时本地Mac环境Redis Cluster环境已经搭建完毕。

--EOF--

