---
layout: post
title: Wildfly读取classpath文件
date: 2018-03-01 15:58:00
tags:
- Wildfly
- Java
category:
- Java
---

由于Wildfly使用[Virtual File System](https://zh.wikipedia.org/wiki/%E8%99%9B%E6%93%AC%E6%AA%94%E6%A1%88%E7%B3%BB%E7%B5%B1 )

无法通过URL或InputStream直接转换为java.io.File。需要通过VirtualFile转换：

```
URL url = getClass().getClassLoader().getResource("updatesql");
VirtualFile virtualFile = (VirtualFile)url.openConnection().getContent();
File[] folder = virtualFile.getPhysicalFile().listFiles();
```

pom.xml

```

<dependency>
    <groupId>org.jboss</groupId>
    <artifactId>jboss-vfs</artifactId>
    <version>3.2.12.Final</version>
</dependency>
```

参考：[1] [stackoverflow](https://stackoverflow.com/a/4900423/3908814)
