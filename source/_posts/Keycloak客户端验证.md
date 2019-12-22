layout: post
title: Keycloak客户端验证
tags:
  - Java
  - Keycloak
  - DevOps
category:
  - Keycloak
categories:
  - DevOps
  - Keycloak
date: 2017-01-23 18:25:00
---
@ 当机密的OIDC客户端需要向keycloak发送请求（比如请求token, refresh token)，就需要被Keycloak验证。有两种方法：
1. Clinet ID和Client Secret
这是传统的OAuth2方式
secret是从keycloak admin console生成的，放到adapter配置的credentials里

```
"credentials":
{
    "secret": "19666a4f-32dd-4049-b082-684c74115f28"
}
```
2. Client authentication with signed JWT
这是基于RFC7523，工作原理：

* 客户端需要有private key和certificate。这可以从keystore文件获取。
* 当客户度启动，它允许你从http://myhost/myapp/k_jwks 下载public key。
* 在验证过程中，客户端用private key签名，生成JWT token，用client_assertion参数发给keycloak
* keycloak必须有public key或客户端的certificate，用来验证JWT的签名。在keycloak admin console, client，Credentials页签，需要把验证方式改为Signed JWT。可以设置JWKS URLhttp://myhost/myapp/k_jwks 。这样keycloak会自动下载新的key。
* 也可以上传jks文件，也可以在admin console生成新文件，放到客户端里。
在keycloak.json文件里:

```
"credentials": {
"j wt": {
"client- keystore- file": "classpath: keystore- client. j ks",
"client- keystore- type": "JKS",
"client- keystore- password": "storepass",
"client- key- password": "keypass",
"client- key- alias": "clientkey",
"token- expiration": 10
         }
}
```
如果不用classpath:，可以指定任意路径的文件
@ 增加自己的客户端验证方式
需要实现客户端和服务端的providers在Server Developer Guide的Authentication SPI有详细介绍。