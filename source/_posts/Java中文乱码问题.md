layout: post
title: Java中文乱码问题
date: 2016-03-01 17:03:00
tags: 
- java
- encoding
category:
- SSH
---

@ jsp页面乱码, 在jps中加
`<%@ page contentType="text/html;charset=UTF-8" language="java" %>`

@ jsp传值到Action的乱码.
struts.xml
*不适用于struts2.1.6 (这个版本有这个bug, 其他版本可以通过这样配置), 可以用spring的filter*
`<constant name="struts.i18n.encoding" value="UTF-8" />`

@ 持久化到数据库内乱码,
配置数据库连接时, 后面参数加上编码, 并且数据库一开始创建时就应该指定编码格式.
```
jdbc.driverClassName=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/spring?useUnicode=true&characterEncoding=utf-8

jdbc.username=root
jdbc.password=
```

@ Tomcat中对于post方法提交的表单采用的默认编码为ISO-8859-1，而这种编码格式不支持中文字符。对于这个问题可以采用转换编码格式的方法来解决
在web.xml配置
```
<filter>
    <filter-name>Set Character Encoding</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
    <init-param>
        <param-name>forceEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>Set Character Encoding</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

######注意, 这个filter应该在struts filter前, 并且不能和
`<constant name="struts.i18n.encoding" value="UTF-8" />`
######冲突