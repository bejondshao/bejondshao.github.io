---
layout: post
title: Keycloak基本功能
date: 2017-12-07 18:09:10
tags:
- Keycloak
- Java
category:
- Java
---
Keycloak是一个致力于解决应用和服务身份验证与访问管理的开源工具。可以通过简单的配置达到保护应用和服务的目的。

##### 用户管理

你的应用不需要开发登录模块，验证用户和保存用户。Keycloak开发了用户管理，登录，注册，密码策略，安全问题，二步验证，密码重置等功能。登录，注册界面所需字段都是可配置，可自定义的。

用户角色，权限管理功能，用户组功能。用户sessions管理。

##### 单点登录/登出（Single-Sign On/Out）

用户通过Keycloak验证身份而非应用本身。当用户通过Keycloak的身份验证后，访问其他应用则不需要登录。

登出同样适用。Keycloak也提供单点登出。

**Kerberos对接**

如果用户验证是通过Kerberos（LDAP或Active Directory）的方式，在登录工作站后，也能自动的通过Keycloak验证，而不需要再次提供用户名密码。

##### 身份代理和社交应用登录（Identity Brokering and Social Login）

{% asset_img dia-identity-brokering.png Identity Brokering %}

在后台管理配置社交应用可以实现第三方应用授权。

Keycloak也能通过现成的OpenID Connect或SAML2.0 Identity Providers验证用户。只需要在后台配置即可。

##### 用户联盟（User Federation）

{% asset_img dia-user-fed.png User Federation %}

Keycloak有内置的连接LDAP或AD的功能。你也可以实现自己的IdP，比如用户存储在关系型数据库。

##### 客户端适配器（Client Adapters）

适配器让保护应用和服务变得很简单。已经有很多平台和编程语言的[适配器](www.keycloak.org/downloads.html)。如果没有与你的平台相适应的适配器，别担心，Keycloak是建立在标准的协议之上，你可以使用任何OpenID Connect Resource Library或SAML 2.0 Service Provider library来实现。

你可以使用代理服务器来保护你的应用，那就根本不需要修改应用了。

##### 后台管理（Admin Console）

{% asset_img screen-admin.png Admin Console %}

通过后台管理，管理员可以管理Keycloak所有方面的配置。可以开启关闭多种功能，配置身份代理和用户联盟。可以定义应用和服务，定义细粒的授权策略。管理用户，包括角色，权限，sessions等。

##### 账户管理界面（Account Management Console）

{% asset_img screen-account.png Account Management Console %}

用户可以通过账户管理界面管理自己的信息，更新，设定二次验证。用户也可以管理自己的sessions。

##### 标准协议（Standard Protocols）

{% asset_img dia-protocols.png Standard Protocols %}

Keycloak是基于标准协议开发的，支持OpenID Connect, OAuth 2.0和SAML。

##### 授权服务（Authorization Services）

如果基于role的授权无法满足需求，Keycloak提供细粒的授权服务。允许你通过后台管理所有的服务，可以定义满足你的需求的策略。

##### Admin REST API

Keycloak提供REST API用于后台管理，包括对Clients，Groups, Users, Roles等一系列资源的管理。这就为应用与Keycloak交互提供可能，应用可以在业务中实现对用户角色权限的增减。

##### 自定义主题

Keycloak为web页面和email信息提供主题支持，更换符合公司产品风格的样式，可以自定义内容包括：
* 账户管理界面
* 后台管理界面
* 邮件
* 登录界面
* 欢迎界面

##### 自定义用户字段（Custom User Attributes）
我们可以在用户属性页添加自定义属性，并在注册界面或用户信息页展示。

##### 集群环境（Clustering）

Keycloak支持集群环境，保障Keycloak的稳定运行，减小服务器压力。集群的搭建可以选择standalone模式或domain模式。domain模式经官方提供的文档使用软件负载均衡已经搭建成功。

##### 服务器缓存（Server Cache）

Keycloak有两种缓存，一种缓存数据库内容到内存，用以减少获取数据的时间，包括realm, client, role和用户元数据。这种缓存是本地缓存，本地缓存是不会在集群环境中复制的。如果数据被更新了，一个失效消息会发送给剩下所有的节点服务器，用于更新缓存。当然有的缓存可以支持集群，需要告诉集群服务器，将特定的条目从本地缓存中移除。
另一种缓存用于处理用户sessions，离线tokens以及跟踪登录失败次数，用于检测网络攻击。这种缓存是临时的，只在内存中保存，但是也可以复制到集群环境中。

##### HTTPS/SSL

Keycloak支持HTTPS协议传输，这也是官方推荐的方式。不过实际产品为了安全和负载均衡，可以考虑反响代理，对代理服务器做SSL，代理服务器与Keycloak交互仍使用HTTP协议。

##### 服务提供者接口（Service Provider Interfaces）

Keycloak设计为包括大多数使用场景，但也支持定制化。Keycloak设计了SPI来允许开发者实现自己的服务提供者。Keycloak可用的SPI有Connections JPA，Email Sender，Email Listener，Login Protocol，Realm等。

##### 扩展服务器（Extending Server）

Keycloak SPI框架提供实现或覆盖已有的providers。Keycloak也提供扩展核心功能的方式，包括：
* 增加自定义REST服务
* 增加自定义SPI
* 增加自定义JPA实体到Keycloak数据模型中

##### 验证身份SPI（AUthentication SPI）

Keycloak自带不同的验证机制：kerberos, 密码和一次性密码。这些机制可能无法满足你的需求，你可能会增加自己的验证机制。Keycloak提供一个验证身份SPI供创建新的插件时使用。