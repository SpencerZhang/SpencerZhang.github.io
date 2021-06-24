---
layout:     post
title:      ReviewCode之foreach
subtitle:   ReviewCode Java foreach
date:       2021-06-10
author:     Spencer
header-img: img/post-bg-desk.jpg
catalog: true
tags:
    - Java
    - ReviewCode
    - foreach

---

# ReviewCode之foreach

### 实际上我不想写这篇的，因为我的要求在idea中安装SonarLint和Alibaba Java Coding Guidelines两个插件，平时开发中个人认为已经能够很好的提升代码质量。那为什么我写了呢？因为在RC时发现了内存溢出问题。

* 场景：校验某个厂商下属所有进件申请的合计融资额是否在可控范围内


### 源实现伪代码：

```java
// 1、获取厂商下属所有进件
List projectList = projectMapper.select(厂商id);
// 2、循环厂商下属所有进件，找到报价，再放到List中
List quotationListTmp = new ArrayList();
projectList.foreach(p -> {
  List quotationList = quotationMapper.select(进件id);
  quotationListTmp.addAll(quotationList);
});
// 3、循环找到的报价，计算合计融资额
BigDecimal all = new BigDecimal("0");
quotationListTmp.foreach(q -> {
  all = all.add(q.financeAamount());
});

```

为什么说这样会有问题？1、首先不符合规范；2、性能差；3、当我们系统存量单据达到一定规模后会有内存溢出问题。

问题有：1、查报价表全表数据

2、循环多次请求数据库

没有结合实际情况分析，在我们系统中报价表中有个clob大字段。我们仅仅是对financeAamount融资额进行校验，上面写法在第2步中addAll方法是内存溢出点。

### 修复后伪代码：

```java
// 1、获取厂商下属所有进件
List projectList = projectMapper.select(厂商id);
// 2、把进件pk组成数组传入查询
List projectIdListTmp = new ArrayList();
projectList.foreach(p -> {
  projectIdListTmp.add(p.getProjectId());
});
// 3、在sql中sum求和financeAamount，
List quotationList = quotationMapper.selectSumAmount(pk数组);
```

### 建议：

1、有些可以在一次请求数据库sql中完成的，就在sql中完成。

2、结合场景和最小范围（不要直接大而全的想select * from tableName）来书写合适的代码。

如有错误的地方欢迎指正。

--EOF--