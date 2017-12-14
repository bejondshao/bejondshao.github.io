---
layout: post
title: Netty开发环境搭建
date: 2017-12-14 11:39:07
tags:
- Java
- Netty
category:
- Java
---
1. 克隆Netty源码
`$ git clone git@github.com:netty/netty.git`
2. Build dev-tools
```
$ cd dev-tools
$ mvn clean install
```
Note: 此步骤必须，因为build整个项目需要使用dev-tools。感觉netty的build顺序不合理，应该自动build dev-tools。
3. Build整个项目
```
$ cd ..
$ mvn clean install -DskipTests=True
```
Note: `-DskipTests=True`是必须的，因为本地环境没有运行test的能力，很多tests会报错导致build失败。