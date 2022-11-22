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

### CompletableFuture初始

#### 结构如下：
![Future](https://spencerzhang.github.io/resource/cf-1.png)
![CompletionStage](https://spencerzhang.github.io/resource/cf-1.png)

#### 包括38个方法，其中36个是如下形式

```java
public CompletionStage<?> somethingAsync(...,Executor executor);
public CompletionStage<?> somethingAsync(...);
与somethingAsync(...,ForkJoinPool.comonPool());一样
public CompletionStage<?> something(...);
```

#### 余下12个方法，其中9个有3种形成

Apply ==> function from input to R, result is a CompletableFuture<R>Accept ==> consumer of input , result is a CompletableFuture<Void>Run ==> just execute a Runnable, result is a CompletableFuture<Void>

##### 单参数方法，如下：thenApply ， thenAccept ， thenRun##### 二元 <<or>>，如下：applyToEither , acceptEither , runAfterEither##### 二元 <<and>>，如下：thenCombine , thenAcceptBoth , runAfterBoth

#### 余下3类方法，如下：##### thenCompose : Function from input to CompletableFuture<R>, result CompletableFuture<R>; **a.k.a flatMap**  ##### handle :Function from input and exception to R, result CompletableFuture<R> ##### whenComplete :Consumer from input and exceptionSimilar to Accept methods aboveResult – the same as input

#### 余下2个方法，无异步方法，如下：
public CompletionStage<T> exceptionally(Function<Throwable, ? extends T> fn);public CompletableFuture<T> toCompletableFuture();

## 使用简例
```java
// 调用方
CompletableFuture<String> cf = doSomething();
// 等待执行完成
String result = cf.get();

// 创建CompletableFuture有返回值
CompletableFuture<String> cf1 = CompletableFuture.supplyAsync(()-> "do biz1", taskExecutor);

// 创建CompletableFuture无返回值
CompletableFuture<Void> cf1 = CompletableFuture.runAsync(()->{
//do biz1
}, taskExecutor);

// nesting(嵌套)和composing(组合)
CompletableFuture<String> cf2 = cf1.thenApply(result -> "cf2"+result);
CompletableFuture<String> cf3 = cf2.thenApply(result -> "cf3"+result);
CompletableFuture<String> cf4 = cf3.thenApply(result -> "cf4"+result);
CompletableFuture<String> fcpe = cf4.thenCompose(result -> "composing"+result);

// combining(合并)
CompletableFuture<String> f1 = CompletableFuture.supplyAsync(()->"do biz1", taskExecutor);
CompletableFuture<String> f2 = CompletableFuture.supplyAsync(()->"do biz2", 

CompletableFuture<String> fcbe = f1.thenCombine(f2,(result1,result2) -> {
// do biz
});

//返回其中一个，不关心哪个先执行完成
CompletableFuture<String> f1 = doSomething1();
CompletableFuture<String> f2 = doSomething2();
CompletableFuture<String> fs = f1.applyToEither(f2, c -> {
// do biz
});

// 最终结果
CompletableFuture<String> cf2 = cf1.thenApply(result -> "cf2"+result);
cf2.thenAccept(result -> "final value"+result);

// 错误响应
cf2.exceptionally(e -> alertMessage(e));
// 或者
cf2.handle((result, e) -> {
    if(e != null) {
        alertMessage(e);
        return null;
    } else {
        return result;
    }
});
```

## 结合去年10月份性能优化
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
#### 一、AopProxy 

```java
// 多线程异步之前获取当前的代理对象
 CompletableFuture.runAsync(() -> {
   // 业务逻辑
   self().xxx();
 });
```
- 3、当前用户获取问题，处理方式
#### 推荐解决方案，使用线程池并且使用Srping Security的DelegatingSecurityContextExecutorService包装一层即可实现
```java
new DelegatingSecurityContextExecutorService(executor)
```

* 参考[文章3](https://tech.meituan.com/2022/05/12/principles-and-practices-of-completablefuture.html)

### 结尾

大力推荐大家使用CompletableFuture，真香。

如有错误的地方欢迎指正。

--EOF--

