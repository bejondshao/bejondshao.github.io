---
layout: post
title: Debian/Ubuntu安装Docker
date: 2018-03-21 14:00:18
tags:
- Docker
- DevOps
Category:
- DevOps
---
@ 更新资源

 `$ sudo apt-get update`

@ 安装docker

`$ sudo apt-get install docker.io`

@ 启动docker服务

`$ sudo service docker start`

@ 将当前用户添加到docker组（需要注销或重启）

`$ sudo usermod -aG docker $USER`

@ 更改docker image镜像为国内镜像

`$ sudo vim /etc/default/docker`

在文件底部加上

`DOCKER_OPTS="--registry-mirror=https://registry.docker-cn.com"`

重启docker服务

`$ sudo service docker restart`

@ Hello world

`$ docker run hello-world`