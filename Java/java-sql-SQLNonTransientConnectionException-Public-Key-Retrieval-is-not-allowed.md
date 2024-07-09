---
title: >-
  java.sql.SQLNonTransientConnectionException: Public Key Retrieval is not
  allowed
date: 2021-10-11 15:06:29
tags: Java
---
# 解决方案
在url参数中加上allowPublicKeyRetrieval=true，例如：
```
spring.datasource.url=jdbc:mysql://localhost:3306/test?characterEncoding=utf-8&useSSL=false&allowPublicKeyRetrieval=true
```