---
layout: post
title: Consul介绍
date: 2018-03-15 15:38:45
tags:
- Consul
category:
- Others
---
##### Consul介绍

Consul包含多个组件，是为基础设施提供服务发现和服务配置的工具。特性有：

* 服务发现：Consul的客户端可以提供一个服务，比如api或者mysql服务。其他的客户端可以通过Consul发现已存在的服务（即api或mysql）。使用DNS或者HTTP，应用程序可以很方便的发现它们所依赖的服务。

* 健康度检测：Consul客户端可以提供多种健康度检测，比如和某个service关联（“web服务是否返回200 OK”），或者检查本地节点（“内存使用在90%以下”）。这些信息可以让运维人员监测集群健康，防止服务被不健康的主机使用。

* Keyc/Value存储：支持层级式键值对存储数据，比如动态配置，功能开关，协作等的，并且有HTTP API方便设定。

* 多数据中心：Consul有现成的多数据中心，使用者不需要写抽象层来扩展业务。

##### Consul基本架构

Consul是一个分布式的高可用系统。这是[详细介绍](https://www.consul.io/docs/internals/architecture.html)
每一个为Consul提供服务的节点都运行一个Consul agent（代理）。发现其他服务或读存键值对并不需要运行agent。Agent是用来做健康检测的，检测这个节点和节点提供的服务。

Agents和Consul服务器沟通，Consul服务器存储和复制数据。这些服务器选出一个leader（头儿）。虽然Consul可以运行在一台服务器上，但是建议用3到5台避免数据丢失。建议给每个数据中心建立一个集群环境。

你基础设施中需要发现其他服务的组件可以查询任何一个Consul 的server或者agent。Agent会自动转发请求到server。

每个数据中运行了一个Consul server集群。当一个跨数据中心的服务发现和配置请求创建时，本地Consul Server转发请求到远程的数据中心并返回结果。
