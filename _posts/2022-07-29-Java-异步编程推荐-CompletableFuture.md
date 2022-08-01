---
layout:     post
title:      Java 异步编程推荐
subtitle:   CompletableFuture
date:       2022-07-29
author:     Spencer
header-img: img/post-bg-desk.jpg
catalog: true
tags:
    - Java
    - Async
    - CompletableFuture
---

# Java 异步编程推荐 CompletableFuture

### Java8里类似ES里Promise的**CompletableFuture**相比Java5添加的Future获取结果不便（线程堵塞或轮询获取），新增很多方法串联异步事件，例如常用：**thenApply**、**thenCompose**、**thenAccept**等。

#### 如果不引入任何第三方库，CompletableFuture 仍是目前 Java 上最好的异步编程方式。去年11月份左右用于某服务解决性能问题(ps:从5-6m优化到3s)，用runAsync居多(for循环增强)，然后allOf().join()，CompletableFuture 虽然麻烦但是能做任何事情。

* 参考[文章1](https://colobu.com/2016/02/29/Java-CompletableFuture/)、[文章2](https://ericfu.me/completable-future-not-so-bad/)


### 伪代码1

```java
// 1、初始化CompletableFuture集合
List<CompletableFuture<Void>> cfList = new ArrayList<>();
// 2、循环业务数据，创建CompletableFuture异步无返回值runAsync，再添加到cfList中
for (xxx x : xList) {
  CompletableFuture<Void> cf = CompletableFuture.runAsync(() -> {
    ... // 业务逻辑处理
  }, taskExecutor).exceptionally(throwable -> {
		... // 异常处理
	});
  cfList.add(cf);
};
// 3、等待所有CompletableFuture执行完成
CompletableFuture.allOf(cfList.toArray(new CompletableFuture[0])).join();

... // 4、接下来其他业务逻辑处理

```
### 伪代码2

```java
// 1、创建CompletableFuture异步有返回值supplyAsync，使用thenApplyAsync处理返回结果
  return CompletableFuture.supplyAsync(() -> {
    ... // 业务逻辑处理
  }, taskExecutor).thenApplyAsync(x ->{
    ... // 结果x逻辑处理
  }, taskExecutor).exceptionally(throwable -> {
		... // 异常处理
	});

```
### 踩坑

- 1、变量线程安全问题，使用线程安全实现类
- 2、代理自己问题，目前的方案是先获取，传递给cf，完成后清理
- 3、当前用户获取问题，处理方式同2

* 参考[文章3](https://tech.meituan.com/2022/05/12/principles-and-practices-of-completablefuture.html)

### 结尾

大力推荐大家使用CompletableFuture，真香。

如有错误的地方欢迎指正。

--EOF--