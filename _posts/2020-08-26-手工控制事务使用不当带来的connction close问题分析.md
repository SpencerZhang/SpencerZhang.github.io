---
layout:     post
title:      手工控制事务使用不当带来的connction close问题分析
subtitle:   connction close
date:       2020-08-26
author:     Spencer
header-img: img/post-bg-swift.jpg
catalog: true
tags:
    - transaction
    - druid

---

# 使用TransactionStatus手工控制事务，发生例外不处理收到rollback带来的connction close问题分析

## 话不多说show code：

```java
		transactionStatus = manager.getTransaction(new DefaultTransactionDefinition());
        Example example = new Example(TestDemo.class, true, true);
        testDemoMapper.deleteByExample(example);
        TestDemo td = new TestDemo();
        td.setCity("demo");
        td.setProvince("demo");
        td.setDescription("demo");
        td.setEnabledFlag("Y");
        td.set__status(DTOStatus.ADD);
        List<TestDemo> tds = new ArrayList<>(1);
        tds.add(td);
        // try {
            self().batchUpdate(requestContext, tds);
            manager.commit(transactionStatus);
        // } catch (Exception e) {
        //     manager.rollback(transactionStatus);
        // }
```

此段代码模拟实际业务代码复现问题，当表中某个新添加字段未更新。业务处理也没有try catch，发生例外时去执行DDL语句添加字段。此时可以观测到此表被锁。因为线上环境不提供kill session权限，干等1个小时等锁自己消失后。再次访问环境出现了connction close错误提示，刷新一下不影响功能使用。但是很隔应人。

## 经过debug区别有无rollback对比发现，当不进行rollback时，连接池Druid connection并没有被回收。但spring已经处理了connection#close。

进行rollback会执行：

``` java
DataSourceTransactionManager#doCleanupAfterCompletion
DataSourceUtils#releaseConnection
DataSourceUtils#doCloseConnection
DruidPooledConnection#close
DruidDataSource#recycle 回收链接
DruidConnectionHolder#reset 重置holder
```

**当不进行rollback时不会执行连接池Druid的相关处理**

## 所以再这里建议大家结合实际业务合理选择事务控制方式

建议使用注解方式处理自治事务：

```JAVA
@Transactional(propagation = Propagation.REQUIRES_NEW)
```

尽量不要自己通过代码实现，像这里手工处理事务。漏掉例外情况事务回滚操作。

扩展阅读：

@see org.springframework.transaction.annotation.Propagation

| 事务传播方式              | 说明                                                         |
| ------------------------- | ------------------------------------------------------------ |
| PROPAGATION_REQUIRED      | 如果当前没有事务，就新建一个事务，如果已经存在一个事务中，加入到这个事务中。这是默认的传播方式 |
| PROPAGATION_SUPPORTS      | 支持当前事务，如果当前没有事务，就以非事务方式执行           |
| PROPAGATION_MANDATORY     | 使用当前的事务，如果当前没有事务，就抛出异常                 |
| PROPAGATION_REQUIRES_NEW  | 新建事务，如果当前存在事务，把当前事务挂起                   |
| PROPAGATION_NOT_SUPPORTED | 以非事务方式执行操作，如果当前存在事务，就把当前事务挂起     |
| PROPAGATION_NEVER         | 以非事务方式执行，如果当前存在事务，则抛出异常               |
| PROPAGATION_SUPPORTS      | 支持当前事务，如果当前没有事务，就以非事务方式执行。         |
| PROPAGATION_NESTED        | 如果当前存在事务，则在嵌套事务内执行。如果当前没有事务，则执行与PROPAGATION_REQUIRED类似的操作。 |

@Transactional 加在private方法上, 无效
@Transactional 加在未加入接口的public方法上, 再通过普通接口方法调用, 无效
@Transactional 加在接口方法上, 无论下面调用的是private或public方法, 都有效
@Transactional 加在接口方法上, 被本类普通接口方法直接调用, 无效
@Transactional 加在接口方法上, 被本类普通接口方法通过接口调用, 有效
@Transactional 加在接口方法上, 被其他类的接口方法调用, 有效
@Transactional 加在接口方法上, 被其他类的私有方法调用后, 有效

Transactional是否生效, 仅取决于是否加载于接口方法, 并且是否通过接口方法调用(而不是本类调用)。这里想想为什么我们要在接口要实现ProxySelf<T>

如有错误的地方欢迎指正。

--EOF--