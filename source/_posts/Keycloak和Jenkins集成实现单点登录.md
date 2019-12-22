layout: post
title: Keycloak和Jenkins集成实现单点登录
tags:
  - Keycloak
  - Jenkins
category:
  - DevOps
categories:
  - DevOps
  - Keycloak
date: 2018-03-26 17:06:00
---
##### 安装Jenkins（[参考](https://pkg.jenkins.io/debian-stable/ )）

@ 将key添加到系统中

`wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -`

@ 将下面一行添加到/etc/apt/sources.list中：

`deb https://pkg.jenkins.io/debian-stable binary/ `

@ 安装

```
sudo apt-get update
sudo apt-get install jenkins

```

##### 配置Jenkins

@ 修改Jenkins运行端口，编辑/etc/default/jenkins文件，将HTTP_PORT改为另外一个端口，比如8880

@ 启动Jenkins

`sudo service start jenkins`

@ 打开http://localhost:8880  ，就可以访问，刚开启，会询问admin初始密码，到`/var/lib/jenkins/secrets/initialAdminPassword`拷贝过来即可。

@ 下载[keycloak-plugin](https://github.com/jenkinsci/keycloak-plugin/releases )到本地。

@ 打开http://localhost:8880/pluginManager/advanced ，使用Upload Plugin，上传keycloak-plugin，并安装。

@ 重启Jenkins

`sudo service restart jenkins`



##### 配置Keycloak

@ 创建Client，名称jenkins，保存。
{% asset_img Client client.png %}

@ 打开jenkins client的Installation页签，选择Keycloak OIDC JSON，拷贝内容留作到Jenkins的配置

{% asset_img Installation installation.png %}

@ 在Realm中创建用于登录的用户。

@ 访问 http://localhost:8880/configure ，找到Keycloak JSON框，将Keycloak OIDC JSON内容拷贝到这里，保存。
{% asset_img Jenkins Configure jenkins-configure.png %}

@ 访问http://localhost:8880/configureSecurity/ ， 勾选"Enable security"，Security Realm选择"Keycloak Authentication Plugin"，保存。
{% asset_img Configure Security jenkins-configureSecurity.png %}

@ 登出管理员用户，点击log in。会跳转到Keycloak登录界面，验证Keycloak用户成功后，跳回Jenkins。至此完成单点登录。

@ 点击log out，即跳转到Keycloak登录页，说明可以实现Jenkins登出，同时使Keycloak的session失效。



##### 遗留问题

1. 在Keycloak主动使session失效，Jenkins用户仍可以访问Jenkins功能。

2. 而如果在http://localhost:8880/configure 配置页选中"Validate Token on each request"，并选中"Keep login session open until access token times out?"，其效果仍然和问题1一样。如果取消选中"Keep login session open until access token times out?"，则在Keycloak使session失效后，Jenkins请求页面会一直卡住，无法做其他操作。只能重启Jenkins服务。
{% asset_img Validate token check-validate-token.png %}
{% asset_img Configure Security jenkins-configureSecurity2.png %}
3. 不要在开启Keycloak验证后Authorization选"Legacy mode"。