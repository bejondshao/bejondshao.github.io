---
layout: post
title: Keycloak配置邮箱和忘记密码功能
date: 2018-07-18 22:17:16
tags:
- Keycloak
- Java
- Security
categories:
- Keycloak
---
服务系统中的发送邮件功能都是依靠某个邮件服务器来发邮件的。一般是用第三方的smtp邮件服务器，比如`smtp.gmail.com`, `smtp.163.com`。本文以163邮箱为例。

一、首先，要注册一个163邮箱。Keycloak发送邮件其实是借用这个邮箱发送的。

二、进入Keycloak后台，进入相关realm，`Realm Settings` -> `Email`。按照如下图配置：
{% asset_img email.png Email Setting %}
{% asset_img email-ssl.png Email Setting with SSL %}
@ `Host`就是发送邮件服务器，填`smtp.163.com`。

@ 端口默认是25，如果是ssl则是465

@ `From Display Name`，发件人姓名

@ `From`，显示的发件人地址。这个地址会显示在发件人地址，可以是别的邮箱，也可以是不存在的邮箱地址。
{% asset_img fake.png From %}

@ `Reply To`，当收件人想要回复邮件时，收件人的名字。
{% asset_img replyto.png Reply To %}

@ `Envelop From`，表示邮件是由谁发的。这个邮件地址必须和`Username`一样。

@ `Username`，真实邮件的发件人，填第一步注册的邮箱。

@ `Password`，这个密码，有的邮件服务商要求填写邮件密码。而163服务器要求填写授权码。获取授权码见步骤三

三、设定授权码。进入163邮箱，点击右上角`设置` -> `POP3/SMTP/IMAP`。
{% asset_img authorization-code.png 设置POP3/SMTP/IMAP %}

然后在新页面选中`IMAP/SMTP服务`
{% asset_img check.png 开启IMAP/SMTP服务 %}

选中后会弹窗，接着下一步，验证身份，设定授权码。授权码会以短信发送给你。这里我认为163设计得很不合理，授权码这类敏感信息应该加密保存，程序不应该能获取到明文，更不应该通过第三方服务（短信服务器）发送，这说明授权码已经暴露了。所以记得把短信删除。

四、将获取的授权码填入步骤二中的Password中。

五、到 http://localhost:8080/auth/realms/test1/login-actions/authenticate?execution=28375a24-caf5-478d-b3af-e665db7956c7&client_id=security-admin-console&tab_id=epQ00ae34Go 点击`Forgot Password`，会跳转到页面，输入用户邮箱。页面跳转到登录页，说明邮件发送成功。登录刚才用户的邮箱，就可以找到验证邮件。点击其中链接，就跳转到重置密码页。