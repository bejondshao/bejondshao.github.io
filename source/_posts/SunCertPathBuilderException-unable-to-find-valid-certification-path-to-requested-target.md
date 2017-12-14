---
layout: post
title: >-
  SunCertPathBuilderException: unable to find valid certification path to
  requested target
date: 2017-12-14 11:47:22
tags:
- Java
- SSL
category:
- Java
---
[原文](https://confluence.atlassian.com/kb/connecting-to-ssl-services-802171215.html)
产生`SunCertPathBuilderException: unable to find valid certification path to
  requested target`的原因是要以SSL方式连接目标，而目标的证书是自签名的，所以会导致这个问题。
通常异常如下：
> javax.net.ssl.SSLHandshakeException: sun.security.validator.ValidatorException: PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
 at com.sun.mail.imap.IMAPStore.protocolConnect(IMAPStore.java:441)
 at javax.mail.Service.connect(Service.java:233)
 at javax.mail.Service.connect(Service.java:134)

当我们使用浏览器通过https访问自签名的站点时，也会提示安全信息:
{% asset_img insecure-connection.png Insecure Connection %}

###### 产生原因
当客户端要通过SSL（比如：HTTPS, IMAPS, LDAPS）访问服务器时，只会连接受信任的应用。在Java领域里被称为keystore，通常存储在`$JAVA_HOME/lib/security/cacerts`。这个文件包含了一系列受信用的证书。

##### 解决方法
1. 获取证书
**Linux**
`$ openssl s_client -connect google.com:443 < /dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > public.crt`
**Windows**(需要安装[sed for Windows](gnuwin32.sourceforge.net/packages/sed.htm)和[OpenSSL](https://www.openssl.org/))
`> openssl s_client -connect google.com:443 < NUL | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > public.crt`
上面命令是从google.com的443端口获取证书，存储为public.crt。
2. 导入证书到cacerts文件中
`$JAVA_HOME/bin/keytool -import alias google.com -keystore $JAVA_HOME/jre/lib/security/cacerts -file public.crt`