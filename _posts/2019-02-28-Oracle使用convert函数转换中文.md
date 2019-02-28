---
layout:     post
title:      convert函数转换中文
subtitle:   使用convert函数转换中文在Oracle中
date:       2019-02-28
author:     Spencer
header-img: img/post-bg-desk.jpg
catalog: true
tags:
    - convert
    - Oracle
---

# 使用convert函数转换中文在Oracle中

## 需要再套用个转换函数 utl_raw.cast_to_raw，否则直接转换，中文会有乱码
![convert](https://spencerzhang.github.io/resource/14531915558650.jpg)


## convert函数支持的字符集代码，可以通过以下sql语句查找：

```sql
SELECT * FROM V$NLS_VALID_VALUES WHERE parameter = 'CHARACTERSET';
```

--EOF--