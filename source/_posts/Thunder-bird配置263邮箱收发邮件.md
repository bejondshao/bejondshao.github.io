layout: post
title: Thunder bird配置263邮箱收发邮件
date: 2017-08-23 22:54:42
tags:
category:
- Linux
---
好久没写博客了，最近看xubuntu自带的thunder bird邮件工具不用有点浪费，所以就研究了下。其实之前有研究过，只是只能收邮件，没法发，就放弃了。今天成功了，网上也没找到相关教程，记录下。

###### Account Settings
{% asset_img 1.png [Account Settings] %}
Account Name，随便写
Your Name，就是发邮件时显示的名字
Signature text，选中“Use HTML”即可插入html的签名。如果签名有图片，使用相对路径是不好用的，需要到263网页邮箱中，inspect element，找到图片网络路径，拷贝替换相对路径。

###### Server Settings
{% asset_img 2.png [Server Settings] %}
Server Name，填写邮件收发的服务器，端口默认993
Connection security，连接服务器的方式，选SSL/TLS
Authentication method，Normal password，第一次连接服务器会询问

###### Outgoing Server(SMTP)
{% asset_img 3.png [Outgoing Server] %}
Server Name，发邮件使用的服务器，一般是smtp协议，端口默认465
User Name，发邮件时所使用的邮箱
Authentication method，Normal password
Connection Security，选择STARTTLS，而不是SSL/TLS

至此，收发邮件功能都好用了，开机自启动，再也不用每次到网页输入账号密码了。

早点睡，别熬夜，我知道你体格好，但那也不是铁打的。