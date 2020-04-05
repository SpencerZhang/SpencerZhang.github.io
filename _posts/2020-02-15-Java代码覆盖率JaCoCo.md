---
layout:     post
title:      Java代码覆盖率JaCoCo
subtitle:   JaCoCo
date:       2020-02-15
author:     Spencer
header-img: img/post-bg-desk.jpeg
catalog: true
tags:
    - Java
    - JaCoCo
---

# Java代码覆盖率JaCoCo
## [官网获取JaCoCo](https://www.jacoco.org/jacoco/)
## 配置JaCoCo

![Maven中配置pom.xml](https://spencerzhang.github.io/resource/jacoco-1.jpg)

![Maven中设置使用的版本](https://spencerzhang.github.io/resource/jacoco-2.jpg)

## Maven加载完依赖，在

![Idea右侧Maven右侧功能栏](https://spencerzhang.github.io/resource/jacoco-3.jpg)

## 执行clean package命令后查看生成的target/site/index.html

![Maven中设置使用的版本](https://spencerzhang.github.io/resource/jacoco-4.jpg)

## 打开index.html效果如下：展示当前工程下所有package的测试覆盖率

![Maven中设置使用的版本](https://spencerzhang.github.io/resource/jacoco-5.jpg)

## 查看某个packeage展示所属的类的测试覆盖率

![Maven中设置使用的版本](https://spencerzhang.github.io/resource/jacoco-6.jpg)

## 点开NumberUtils类可以查看每个方法测试覆盖率

![Maven中设置使用的版本](https://spencerzhang.github.io/resource/jacoco-7.jpg)

JaCoCo配置简单方便使用，推荐大家使用。

--EOF--

