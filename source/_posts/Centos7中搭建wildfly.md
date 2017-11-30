---
layout: post
title: Centos7中搭建wildfly
date: 2017-11-30 20:00:55
tags:
- Linux
- Centos
- Wildfly
category:
- Linux
---
（一）wildfly配置

1 将wildfly文件移至/opt目录
`$ sudo mv /tmp/wildfly /opt`

2 创建用户。创建名为wildfly的group，创建名为wildfly的不可登录的用户，将用户加入组中。
`$ sudo groupadd -r wildfly useradd -r -g wildfly -d /opt/wildfly -s /sbin/nologin wildfly`

3 以下都在systemd目录下执行

```
$ cd /opt/wildfly
$ sudo mkdir -p /etc/wildfly
$ sudo cp wildfly.conf /etc/wildfly/
$ sudo cp wildfly.service /etc/systemd/system/
$ sudo cp launch.sh /opt/wildfly/bin/
$ sudo chmod +x /opt/wildfly/bin/launch.sh
$ sudo chmod +x /opt/wildfly/bin/standalone.sh
$ sudo chown -R wildfly:wildfly /opt/wildfly
```

4 启动/自启

- 创建服务
`$ sudo systemctl enable wildfly`

- 启动服务
`$ sudo systemctl start wildfly`

- 停止服务
`$ sudo systemctl stop wildfly`

- 查看服务状态
`$ sudo systemctl status wildfly`

（二）防火墙相关配置

1. 查看防火墙状态
`$ sudo firewall-cmd --state`

2. 开启http
`$ sudo firewall-cmd --zone=public --permanent --add-service=http`

3. 开启https
`$ sudo firewall-cmd --zone=public --permanent --add-service=https`

4. 开放8080端口
`$ sudo firewall-cmd --zone=public --permanent --add-port=8080/tcp`

5. 开放8443端口
`$ sudo firewall-cmd --zone=public --permanent --add-port=8443/tcp`

6. 开启防火墙
`$ sudo systemctl start firewalld.service`

7. 重新加载防火墙配置
`$ sudo firewall-cmd --reload`

Note: 如果重新加载防火墙配置没有应用配置，重启防火墙：

```
$ sudo systemctl stop firewalld.service
$ sudo systemctl status firewalld.service
$ sudo systemctl start firewalld.service

```

（三）nginx安装及网站应用配置

1 安装nginx前，安装前需先安装nginx依赖PCRE

- 安装在usr/local/src目录下下载pcre(版本自行选择稳定版)。执行：
`$ sudo wget  http://ftp.csx.cam.ac.uk/pub/software/programming/pcre/pcre-8.40.tar.gz `

- 解压
`tar zxvf pcre-8.40.tar.gz`

- 进入安装目录
`$ cd pcre-8.40

- 编译
`$ sudo ./configure`

- 安装
`$ sudo make && make install`

- 查看版本
`$ pcre-config --version`

2 安装nginx

```
$ sudo wget http://nginx.org/download/nginx-1.6.2.tar.gz

$ sudo tar zxvf nginx-1.6.2.tar.gz
$ cd nginx-1.6.2
$ sudo ./configure --prefix=/usr/local/webserver/nginx --with-http_stub_status_module --with-http_ssl_module --with-pcre=/usr/local/src/pcre-8.40
$ sudo make && make install
$ /usr/local/webserver/nginx/sbin/nginx -v
```

3 配置nginx

- 进入目录/usr/local/webserver/nginx/conf

- 相关配置nginx.conf检查配置是否正确

- 启动Nginx 
`/usr/local/webserver/nginx/sbin/nginx -t`

- 修改配置后重新加载生效
`/usr/local/webserver/nginx/sbin/nginxnginx -s reload`

- 重新打开日志文件
`nginx -s reopen`

- 测试nginx配置文件是否正确
`nginx -t -c /path/to/nginx.conf`

4 访问站点配置nginx.conf

```
location /application {
proxy_pass http://127.0.0.1:8080/application;
proxy_set_header Host $host;
$proxy_add_x_forwarded_for;
proxy_set_header X-Forwarded-Proto "https";
proxy_set_header X-Forwarded-Port "443";
proxy_set_header X-SSL-Client-Cert $ssl_client_cert;
}
```