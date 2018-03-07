---
layout: post
title: 在应用启动前更新数据库遇到TransactionRequiredException
date: 2018-03-07 17:41:26
tags:
- Java
- wildfly
category:
- Java
---

在实现项目升级时，自动查找SQL文件并执行脚本，更新数据库表结构或内容时，遇到“javax.persistence.TransactionRequiredException: Executing an update/delete query"。

由于执行更新要在应用启动前执行，所以需要实现ServletContextListener，实现contextInitialized(ServletContextEvent sce)方法。

尝试过多种增加@Transactional注解都无效，最后在[stackoverflow](https://stackoverflow.com/questions/37009257/how-can-i-do-create-function-in-jpa)找到解决方法。