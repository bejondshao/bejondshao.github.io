layout: post
title: Keycloak和Grafana集成实现单点登录
tags:
  - Grafana
  - Keycloak
  - DevOps
category:
  - DevOps
date: 2018-03-26 11:26:12
---
##### Keycloak配置

@ 登录Keycloak后台http://localhost:8080/auth/admin/master/console/#/realms/master

@ Add Realm，Name为"abc"

@ 创建Client，Name为"grafana"，grafana默认端口是3000, 因此Root URL填写http://localhost:3000
{% asset_img Client client.png %}

@ 开启Authorization Enabled，保存。（该步骤非必须，建议开启，可以提供鉴权功能）

@ 打开Credentials，留存Secret，用于稍后配置grafana.ini。

@ 到Users模块创建用户
{% asset_img User user.png %}
@ 到用户的Credentials页签设定密码，关闭Temporary选项。
{% asset_img Credentials credentials.png %}

##### 安装（[参考](http://docs.grafana.org/installation/debian/))

```
wget https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_5.0.3_amd64.deb
sudo apt-get install -y adduser libfontconfig
sudo dpkg -i grafana_5.0.3_amd64.deb
```

@ 启动服务

`sudo service grafana-server start`

@ 访问http://localhost:3000即可看到登录界面

##### Grafana配置Oauth

@ 编辑/etc/grafana/grafana.ini配置文件

```

[auth.generic_oauth]
enabled = true
name = abc
allow_sign_up = true
client_id = grafana
# secret从keycloak的grafana client，Credentials页签的Secret获取。
client_secret = 9a134c1a-e8f2-46b0-b351-d2ffeccaa289
;scopes = user:email,read:org
scopes = openid email name
auth_url = http://localhost:8080/auth/realms/abc/protocol/openid-connect/auth
token_url = http://localhost:8080/auth/realms/abc/protocol/openid-connect/token
;api_url = https://foo.bar/user
;team_ids =
;allowed_organizations =

```
同时修改`[database]`和`[security]`节点内容。

@ 重启服务

`sudo service grafana-server restart`

@ 再次访问http://localhost:3000  ，登录界面能看到"Sign in with abc"，点击即跳转到Keycloak登录界面，Realm为abc。使用bejond.shao登录，登录成功跳转回grafana，实现单点登录。同时grafana会创建同名用户，并存储基本信息，如邮箱。

##### 存在的问题

@ Session无法同步

* 在keycloak登出用户的所有sessions，grafana并不知晓，session仍有效。

* 在grafana登出用户，再次登录，点击"Sign in with abc"，没有登录界面，而是直接跳转回grafana。说明grafana登出功能未做到通知IdP。详见[sign out url for generic oauth](https://github.com/grafana/grafana/issues/9847 )