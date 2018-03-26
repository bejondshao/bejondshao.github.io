---
layout: post
title: Grafana和Keycloak集成
date: 2018-03-26 11:26:12
tags:
- Grafana
- Keycloak
- DevOps
category:
- DevOps
---
##### Keycloak配置

@ 登录Keycloak后台http://localhost:8080/auth/admin/master/console/#/realms/master

@ Add Realm，Name为"Kunlun"

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

##### 

@ 编辑/etc/grafana/grafana.ini配置文件

```

[auth.generic_oauth]

enabled = true

name = Kunlun

allow_sign_up = true

client_id = grafana

// secret从keycloak的grafana client，Credentials页签的Secret获取。

client_secret = 9a134c1a-e8f2-46b0-b351-d2ffeccaa289

;scopes = user:email,read:org

scopes = openid email name

auth_url = http://localhost:8080/auth/realms/Kunlun/protocol/openid-connect/auth

token_url = http://localhost:8080/auth/realms/Kunlun/protocol/openid-connect/token

;api_url = https://foo.bar/user

;team_ids =

;allowed_organizations =

```

@ 重启服务

`sudo service grafana-server restart`

@ 再次访问http://localhost:3000  ，登录界面能看到"Sign in with Kunlun"，点击即跳转到Keycloak登录界面，Realm为Kunlun。使用bejond.shao登录，登录成功跳转回grafana，实现单点登录。同时grafana会创建同名用户，并存储基本信息，如邮箱。

##### 存在的问题

@ Session无法同步

* 在keycloak登出用户的所有sessions，grafana并不知晓，session仍有效。

* 在grafana登出用户，再次登录，点击"Sign in with Kunlun"，没有登录界面，而是直接跳转回grafana。说明grafana登出功能未做到通知IdP。详见[sign out url for generic oauth](https://github.com/grafana/grafana/issues/9847 )