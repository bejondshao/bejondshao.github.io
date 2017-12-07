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

##### 单点登录/登出（Single-Sign On/Out）

用户通过Keycloak验证身份而非应用本身。应用不需要登录模块，验证用户和保存用户。当用户通过Keycloak的身份验证后，访问其他应用则不需要登录。

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

适配器让保护应用和服务变得很简单。已经有很多平台和编程语言的适配器。如果没有与你的平台相适应的适配器，别担心，Keycloak是建立在标准的协议之上，你可以使用任何OpenID Connect Resource Library或SAML 2.0 Service Provider library来实现。

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