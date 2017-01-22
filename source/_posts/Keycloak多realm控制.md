layout: post
title: Keycloak多realm控制
date: 2017-01-22 18:05:00
tags:
- Java
- Keycloak
category:
- Keycloak
---

@ 一个应用可以被多个realms保护，这些realms可以是一个keycloak server，也可以是多个。
也就需要多个keycloak.json，不同的名称。
@ 首先需要实现org.keycloak.adapters.KeycloakConfigResolver
{% asset_img resolver.jpg Resolver %}
@ 然后在web.xml里配置新的Resolver
```
<web-app>
. . .
<context-param>
<param-name>keycloak.config.resolver</param-name>
<param-value>example.PathBasedKeycloakConfigResolver</param-value>
</context-param>
</web-app>
```