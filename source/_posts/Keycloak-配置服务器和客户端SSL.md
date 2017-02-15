layout: post
title: Keycloak-配置服务器和客户端SSL
date: 2017-02-15 17:16:09
tags:
- Keycloak
- Java
category: 
- Keycloak
---
@ SSL配置原理[参考](https://docs.oracle.com/cd/E54932_01/doc.705/e54936/cssg_create_ssl_cert.htm)
@ 在每个realm配置中，有HTTPS应用范围配置。

* external request：在私有IP如localhost, 127.0.0.1, 10.0.x.x, 192.168.x.x以及172.16.x.x不会要求SSL。外部IP访问会要求HTTPS
* none：所有IP访问都不需要HTTPS
* all request：所有IP访问都需要HTTPS (在Master realm设置all request需要确保SSL已经搭建成功，否则会无法访问keycloak。解决方法是去keycloak数据库，更改realm表，“update realm set ssl_required = 'EXTERNAL' where name = 'master';”)

@ 如果代理服务器(apache或nginx)不配置SSL，可以在keycloak配置SSL。总共分两步：

1. 获取或生成keystore
2. 在keycloak服务器配置keystore

###### 以下介绍自生成keystore的方法，从认证机构申请的方法略过。
@ 在keycloak服务器主机keytool生成jks证书:

`keytool -genkeypair -alias aliasName -keyalg RSA -keystore keycloak.jks -validity 10950
`
{% asset_img genkeypair.png genkeypair %}
注意：CN必须是主机名，也可以是ip但是ip容易变，必须是主机名或域名。因为在后续客户端配置auth server url中也要配置主机名，这两个地方的名称要相同。在httprequest中会验证。
口令是为保护证书，自定义。
validity有效时间，单位天
是否正确：y不是yes
alias是给证书起别名，防止重复，证书存储在keycloak.jks中

@ 在keycloak服务器配置keystore
 将keycloak.jks复制到configuration文件夹内，然后编辑standalone.xml，standalone-ha.xml或domain.xml文件：
```
<security-realm name="UndertowRealm">
    <server-identities>
        <ssl>
            <keystore path="keycloak.jks" relative-to="jboss.server.config.dir" keystore-password="changeit" />
        </ssl>
    </server-identities>
</security-realm>
```
Note: 如果是domain.xml，relative-to="jboss.domain.config.dir"

然后找到 urn:jboss:domain:undertow关键字：
```
<subsystem xmlns="urn:jboss:domain:undertow:3.0">
   <buffer-cache name="default"/>
   <server name="default-server">
      <https-listener name="https" socket-binding="https" security-realm="UndertowRealm"/>
   ...
</subsystem>
```
keycloak服务器已经配置完成，重启服务器即可访问
https://host:8443


下面介绍如何在客户端通过https访问keycloak: 

@ 用keycloak服务器配置的keystore导出证书，供客户端使用：
keytool -export -alias shiji-server -keystore keycloak.jks -rfc -file shiji-server.crt
alias建议和生成时的别名相同，根据keycloak.jks导出证书到shiji-server.crt中，导出时会要求输入keycloak.jks的密码。

@ 将crt导入到客户端中：
[参考](https://docs.oracle.com/javase/tutorial/security/toolfilex/rstep1.html)
`
keytool -importcert -alias shiji-server -file shij-server.crt -keystore truststore.jks
`
将crt证书导入到truststore.jks中，导入时会要求输入truststore.jks的密码
@ 在客户端的keycloak.json中配置
```
{
  "realm": "hello-world-authz",
  "auth-server-url": "https://shiji-server:8443/auth",
  "ssl-required": "all",
  "resource": "hello-world-authz-service",
  "credentials": {
    "secret": "secret"
  },
  "policy-enforcer": {},
  "truststore": "E:/truststore.jks",
  "truststore-password": "changeit"
}
```
Note: truststore的配置项可以指定“classpath:/truststore.jks",例子使用的是系统文档位置，因为在我测试的时候classpath方式无法找到truststore。在研究配置时不断的测试，后来改成系统方式。在后来配置好SSL后debug发现，原因是Keycloak源码中的KeycloakUtil使用的classloader错误。参考[Keycloak JIRA](https://issues.jboss.org/browse/KEYCLOAK-4427)和[github pull request](https://github.com/keycloak/keycloak/pull/3867)。

Note: 如果导入后启动客户端出现 
```
Certificate for <192.168.1.100> doesn't match common name of the certificate subject: localhostCertificate for <192.168.1.100> doesn't match common name of the certificate subject: localhost
```
说明证书的CN是localhost，而远端主机是192.168.1.100。说明证书的common name起错了，应该是ip或者主机名，建议使用主机名

