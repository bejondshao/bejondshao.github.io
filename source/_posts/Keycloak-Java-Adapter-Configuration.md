layout: post
title: Keycloak Java Adapter Configuration
tags:
  - Java
  - Keycloak
category:
  - Keycloak
categories:
  - DevOps
  - Keycloak
date: 2017-01-05 11:43:00
---
@ keycloak adapter可以在war包里添加keycloak.json配置，也可以在standalone.xml里配置。 开发过程推荐使用keycloak.json，生产环境推荐使用standalone.xml。 下面是standalone的配置格式：
```<subsystem xmlns="urn:jboss:domain:keycloak:1.1"></subsystem>```

###### 以下是配置的属性

@ **resource**, 应用的client id。Required

@ **realm-public-key**, PEM格式的realm public key。可以从console获取，不建议设置。如果不设置，Keycloak会每次下载新的public key。

@ **auth-server-url**，Keycloak服务器的url。所有Keycloak页面和REST服务都是从这个地址转发的。 http://host:port/auth。 Required

@ ssl-required，确保所有沟通都通过HTTPS，在产品中应该设置为all。默认值external，意思HTTPS对外部请求是必须的。可用值all, external, none.

@ use-resource-role-mappings，如果是true，adapter会在token里找application级别的role mappings。如果是false, 会看realm级别的role mappings。默认是false

@ public-client, 如果是true，适配器不会为client向Keycloak发送证书。默认是false

@ enable-cors，默认值false。如果是true，会处理CORS 前置请求，同时会从access token检查valid origins。

@ cors-max-age。如果CORS是enabled，这个值会被设置到Access-Control-Max-Age头中。如果没设置，这个头设定就不会在CORS响应里。

@ cors-allowed-methods, 如果cors开启，设定Access-Control-Allow-Methods

@ bearer-only, 针对services，应该设为true。如果开启，adapter不会授权给用户，只会检查bearer tokens。默认是false

@ autodetect-bearer-only, 如果应用服务器既是web应用，又是web服务（SOAP or REST），就应该设为true。这样会对未授权的用户跳转到登录页，对未授权的SOAP/REST客户端会返回401。Keycloak根据头X-Requested-With, SOAPAction或Accept来确定是SOAP还是REST客户端。默认值false

@ enable-basic-auth, 告诉adapter支持basic验证。如果开启，必须提供secret。默认值false。

@ expose-token，如果是true，被授权的浏览器客户端（通过JS HTTP调用）可以通过root/k_query_bearer_token获取signed access token。默认是false

@ **credentials**, 确定应用的凭证。这是个对象符合，key是credential类型，value是credential类型的值。现阶段支持password和jwt。对‘Confidential’访问方式来说是Required

@ connection-pool-size, Adapter会创建额外的HTTP调用Keycloak Server来讲access code转为access token。这个配置多少个到Keycloak server的连接池装多少个连接，默认是20

@ disable-trust-manager, 如果Keycloak server要求HTTPS，并且这个属性设置为true。那么你就不需要配置truststore。这个设置只用在开发模式中，production环境应该设置成false。默认是false

@ allow-any-hostname, 如果Keycloak server要求HTTPS，并且这个属性设置为true。那么Keycloak server的颁发证书就通过truststore，但是host name验证就去掉了。这个设置只用在开发模式中，production环境应该设置成false。默认是false

@ truststore, 指向keystore文件的路径。如果在路径前加classpath:，那么truststore会从部署的classpath找文件。用于向外与Keycloak server做HTTPS沟通。客户端创建HTTPS请求需要一种验证服务器主机的方式，truststore就是做这个工作的。keystore包含一到多个受信主机证书或凭证管理中心。你可以解压Keycloak server的SSL keystore的public certificate来创建truststore。除非ssl-required是none或disable-trust-manager是true，那么这个属性就是Required。

@ truststore-password, truststore keystore的密码。如果设定了truststore，并且truststore需要密码，那么这个属性就是Required。

@ client-keystore, 指向keystore的路径。这个keystore包含客户端双向SSL验证，用于adapter创建对Keycloak server的HTTPS请求。

@ client-keystore-password, keystore的密码。如果client-keystore设定了，这个就是必须的。

@ client-key-password, 客户端key的密码，如果client-keystore设定了，这个就是必须的。

@ **always-refresh-token**，如果是true，适配器会在每次请求时都刷新token （这个在开发测试阶段很有必要）

@ register-node-at-startup, 如果是ture，adapter会想Keycloak发送注册请求。这个属性默认是false。在cluster环境中起作用。

@ register-node-period, 定期在Keycloak重新注册adapter。cluster环境中使用。

@ token-store, 可以是session或cookie。默认是session，adapter将账户信息存储在HTTP session中。

@ principal-attribute, OpenID Connection ID Token attribute to populate the UserPrincipal name with. 如果token属性是null，默认值就是sub. 可能的值有sub, preferred_username, email, name, nickname, given_name, family_name.

@ turn-off-change-session-id-on-login, 默认情况下，session id 会在登录成功后被改变，来插入一个安全攻击容器。设置成true，如果你想关闭这个功能。默认值是false

@ token-minimum-time-to-live, 大多数情况下，会在access token失效前刷新。尤其是将access token发送给REST client，token可能会在验证它之前失效，所以刷新很有必要。这个值不应该大于realm's access token寿命。默认是0（秒）， 所以adapter会在它失效时刷新它。

@ min-time-between-jwks-request, 向Keycloak发送请求新的public key的间隔，单位秒。默认是10。当adapter不认识kid时，会下载尝试新的public key。10秒内不会重复请求，防止有DoS攻击，发送许多错误的kid。

@ public-key-cache-ttl, 向Keycloak发送请求新的public key的最大间隔，默认是86400秒（1天）。当adapter不认识kid时，会下载尝试新的public key。如果认识kid，会使用已存在的public key。超过这个期限，会下载新的key。