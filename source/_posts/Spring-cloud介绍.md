---
layout: post
title: Spring cloud介绍
date: 2018-03-16 14:02:54
tags:
- Spring cloud
- Java
- DevOps
category:
- Java
---

Spring cloud为开发者提供便利的工具，在分布式系统快速构建常用的模式。（比如配置管理，服务发现，断路器，智能路由，微代理，总线控制，一次性token，全范围锁，自动任命leader，分布式session，云声明等）开发者可以快速构实现上述模式的建服务和应用。这些服务和应用可以在笔记本，数据中心，云主机运行。

Spring cloud是基于spring boot构建的，只不过提供了一些类库，这些类库在将应用放到classpath时附加了一些行为。在基础行为上可以快速配置或扩展应用。

###### 特性

Spring cloud专注于提供现成的基本用例，支持扩展其他用例。

* 分布式/版本式的配置

* 服务注册与发现

* 路由

* 服务到服务之间调用

* 负载均衡

* 断路器

* 全应用锁

* 自动任命leader和云声明

* 分布式消息

pom.xml

```

<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.0.0.M7</version>
</parent>
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>Finchley.M7</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
<dependencies>
    <dependency>
        <groupId></groupId>
        <artifactId>spring-cloud-starter-config</artifactId>
    </dependency>
    <dependency>
        <groupId></groupId>
        <artifactId>spring-cloud-starter-eureka</artifactId>
    </dependency>
</dependencies><repositories>
    <repository>
        <id>spring-milestones</id>
        <name>Spring Milestones</name>
        <url>https://repo.spring.io/libs-milestone</url>
        <snapshots>
            <enabled>false</enabled>
        </snapshots>
    </repository>
</repositories>
```

