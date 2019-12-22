layout: post
title: Keycloak应用所需工作
tags:
  - Java
  - Keycloak
category:
  - Java
categories:
  - DevOps
  - Keycloak
date: 2017-12-11 17:46:00
---

一、学习并了解keycloak项目以及相关概念，验证流程等

二、部署配置keyclaok服务器
  * 下载并解压[最新keyclaok](http://www.keycloak.org/downloads.html)
  * 配置keyclaok数据库和数据库驱动
  * 配置项目服务器中的adapter

三、禁用现有项目中的权限模块，注释相关权限验证代码
  * pickedlink
  * .Net相关权限模块
  * User，Role等model可暂时保留，待keycloak应用成功后移除。

四、在keycloak进行相关配置
  * 配置realm, client, resource, scope, permission, policy, role, user, group等以满足项目实际需要。（user和group支持attribute扩展，以符合实际项目需要）
  * 设置用户密码策略
  * 配置keycloak admin后台管理角色和用户

五、更改项目代码，应用keycloak
java项目：  
  * 在项目页面中调用keycloak相关API获取用户权限，进行权限验证
.Net项目：
  * 通过解码RPT或rest API来验证用户权限
  * 通过angularJS调用 （[示例](https://github.com/keycloak/keycloak/tree/master/examples/authz/photoz/photoz-html5-client)）
服务端项目：
  * keycloak有针对其他项目rest接口的身份验证，应移除服务端接口验证
客户端项目：
  * 增加获取token的拦截器（或将token保存在缓存中以便使用，token需要定时更新），在请求头中添加验证信息

六、自定义
keycloak方面：
  * 制作keycloak主题，包括图标，界面显示语言（keycloak支持多语言，只需要增加新key以及相关显示信息）。
     所需技术：ftl ([示例](https://github.com/keycloak/keycloak/tree/master/examples/themes))
项目方面：
  * 创建相关类和工具类，用于与keycloak交互，并将所获取内容进行缓存，减少与keycloak的沟通频率。

七、附加功能
  * keycloak升级
  * keycloak支持realm和authorization导出，可以快速便捷移植项目配置。
  * clustering，多节点keycloak服务器，加快验证效率。
  * 第三方授权登录，如果应用可以开方相关请求验证模块
  * https访问
  * DLAP，可以从active directory, Red Hat Directory等服务器获取用户信息
  * Admin REST API，支持远程管理keycloak服务器信息，可以开发客户端角色，权限管理模块。
  * CORS
  * Client Template

八、注意
  * keycloak源码中的权限角色检查使用的是常量string验证，不便于以后更改角色名称。在开发过程中应避免使用string验证，应该使用常量。