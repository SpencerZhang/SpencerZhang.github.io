---
layout:     post
title:      Oracle VPD 实现细粒度访问数据
subtitle:   VPD 策略简介
date:       2018-07-17
author:     Spencer
header-img: img/post-bg-coffee.jpeg
catalog: true
tags:
    - Oracle
    - VPD
---

# Oracle VPD 实现细粒度访问数据

## VPD 全称Virtual Private Database，这个技术提供了对数据库信息的细粒度访问控制。 是数据库层面的一种非常成熟的数据访问控制技术，通过策略函数来实现的具体的控制。

## 先创建策略函数

```PL/SQL
create or replace function fn_getEmployees(p_schema in varchar2,
                                           p_object in varchar2)
  return varchar2 is
  v_result varchar2(32767);
begin
  v_result := 'manager_id is not null';
  return(v_result);
end fn_getEmployees;
```

## 添加策略
### 策略定义在系统中，由DBA维护，应用开发时可根据需要定义策略。例如根据上面的表和策略函数，定义策略如下：
#### 添加策略

```PL/SQL
declare
Begin
  DBMS_RLS.Add_Policy(Object_Schema   => 'oracle_train', --数据表(或视图)所在的Schema名称
                      Object_Name     => 'EMPLOYEES', --数据表(或视图)的名称
                      Policy_Name     => 'T_EMPLOYEES', --POLICY的名称，主要用于将来对Policy的管理
                      Function_Schema => 'oracle_train', --返回Where子句的函数所在Schema名称
                      Policy_Function => 'fn_getEmployees', --返回Where子句的函数名称
                      Statement_Types => 'Select', -- DML类型，如 'Select,Insert,Update,Delete'
                      Enable          => True --是否启用，值为'True'或'False'
                      );
end;
```

#### 启用或失效策略

```PL/SQL
declare
Begin
  DBMS_RLS.enable_policy(Object_Schema   => 'oracle_train', --数据表(或视图)所在的Schema名称
                      Object_Name     => 'EMPLOYEES', --数据表(或视图)的名称
                      Policy_Name     => 'T_EMPLOYEES', --POLICY的名称，主要用于将来对Policy的管理
                      Enable          => FALSE --是否启用，值为'True'或'False'
                      );
end;
```
#### 其他存储过程详见PACKAGE:DBMS_RLS
#### 查看当前用户拥有的策略

```sql
SELECT * FROM dba_policies WHERE object_owner = 'ORACLE_TRAIN';
```

## 策略添加前
![](https://spencerzhang.github.io/resource/15317985880423.jpg)

## 策略添加后
![](https://spencerzhang.github.io/resource/15317983817528.jpg)



