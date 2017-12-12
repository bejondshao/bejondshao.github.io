---
layout: post
title: Mybatis提示IO Error：Broken pipe或No more data to read from socket错误
date: 2017-12-12 18:32:58
tags:
- Mybatis
category:
- Java
---
mybatis连接数据库查询报

>18:07:25,819 ERROR [stderr] (default task-40) org.apache.ibatis.exceptions.PersistenceException: 

18:07:25,819 ERROR [stderr] (default task-40) ### Error querying database.  Cause: java.sql.SQLRecoverableException: IO Error: Broken pipe (Write failed)

18:07:25,819 ERROR [stderr] (default task-40) ### The error may exist in cn/shijinet/kunlun/kiosk/mybatis/xml/QueryInfoMapper.xml

18:07:25,819 ERROR [stderr] (default task-40) ### The error may involve cn.shijinet.kunlun.kiosk.mybatis.mapper.QueryInfoMapper.queryInfoFromOpera-Inline

18:07:25,819 ERROR [stderr] (default task-40) ### The error occurred while setting parameters

18:07:25,819 ERROR [stderr] (default task-40) ### SQL: SELECT UDFC08         FROM(             SELECT UDFC08 FROM RESERVATION_NAME                                       WHERE RESV_NAME_ID = ?                           ) WHERE ROWNUM = 1

18:07:25,819 ERROR [stderr] (default task-40) ### Cause: java.sql.SQLRecoverableException: IO Error: Broken pipe (Write failed)

18:07:25,821 ERROR [stderr] (default task-40) 	at org.apache.ibatis.exceptions.ExceptionFactory.wrapException(ExceptionFactory.java:30)

18:07:25,821 ERROR [stderr] (default task-40) 	at org.apache.ibatis.session.defaults.DefaultSqlSession.selectList(DefaultSqlSession.java:150)

18:07:25,821 ERROR [stderr] (default task-40) 	at org.apache.ibatis.session.defaults.DefaultSqlSession.selectList(DefaultSqlSession.java:141)

18:07:25,821 ERROR [stderr] (default task-40) 	at org.apache.ibatis.session.defaults.DefaultSqlSession.selectOne(DefaultSqlSession.java:77)

18:07:25,822 ERROR [stderr] (default task-40) 	at org.apache.ibatis.binding.MapperMethod.execute(MapperMethod.java:82)

18:07:25,822 ERROR [stderr] (default task-40) 	at org.apache.ibatis.binding.MapperProxy.invoke(MapperProxy.java:59)

18:07:25,822 ERROR [stderr] (default task-40) 	at com.sun.proxy.$Proxy330.queryInfoFromOpera(Unknown Source)

18:07:25,822 ERROR [stderr] (default task-40) 	at cn.shijinet.kunlun.kiosk.mybatis.QueryInfo.queryInfoFromOpera(QueryInfo.java:42)

﻿

或者提示



> 2017-12-12 17:41:35,730 INFO  [stdout] (default task-17) ==> Parameters: 15271(String)

2017-12-12 17:41:35,733 ERROR [stderr] (default task-17) org.apache.ibatis.exceptions.PersistenceException: 

2017-12-12 17:41:35,733 ERROR [stderr] (default task-17) ### Error querying database.  Cause: java.sql.SQLRecoverableException: No more data to read from socket

2017-12-12 17:41:35,733 ERROR [stderr] (default task-17) ### The error may exist in cn/shijinet/kunlun/kiosk/mybatis/xml/QueryInfoMapper.xml

2017-12-12 17:41:35,733 ERROR [stderr] (default task-17) ### The error may involve cn.shijinet.kunlun.kiosk.mybatis.mapper.QueryInfoMapper.queryInfoFromOpera-Inline

2017-12-12 17:41:35,733 ERROR [stderr] (default task-17) ### The error occurred while setting parameters

2017-12-12 17:41:35,733 ERROR [stderr] (default task-17) ### SQL: SELECT UDFC08         FROM(             SELECT UDFC08 FROM RESERVATION_NAME                                       WHERE RESV_NAME_ID = ?                           ) WHERE ROWNUM = 1

2017-12-12 17:41:35,733 ERROR [stderr] (default task-17) ### Cause: java.sql.SQLRecoverableException: No more data to read from socket

2017-12-12 17:41:35,733 ERROR [stderr] (default task-17) 	at org.apache.ibatis.exceptions.ExceptionFactory.wrapException(ExceptionFactory.java:30)

2017-12-12 17:41:35,733 ERROR [stderr] (default task-17) 	at org.apache.ibatis.session.defaults.DefaultSqlSession.selectList(DefaultSqlSession.java:150)

2017-12-12 17:41:35,733 ERROR [stderr] (default task-17) 	at org.apache.ibatis.session.defaults.DefaultSqlSession.selectList(DefaultSqlSession.java:141)

2017-12-12 17:41:35,733 ERROR [stderr] (default task-17) 	at org.apache.ibatis.session.defaults.DefaultSqlSession.selectOne(DefaultSqlSession.java:77)

2017-12-12 17:41:35,733 ERROR [stderr] (default task-17) 	at org.apache.ibatis.binding.MapperMethod.execute(MapperMethod.java:82)

2017-12-12 17:41:35,733 ERROR [stderr] (default task-17) 	at org.apache.ibatis.binding.MapperProxy.invoke(MapperProxy.java:59)



原因是数据库重启，导致connection pool中的connections都失效了，而mybatis在开库查询数据库内容时使用的旧的数据库，重启应用即可解决。