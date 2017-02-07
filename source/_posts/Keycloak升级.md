layout: post
title: Keycloak升级
date: 2017-02-07 13:42:02
tags:
- Java
- Keycloak
category:
- Keycloak
---
@ 升级需要考虑database，keycloak-server.json, providers, themes，adapter，driver和applications。

@ 不建议从很旧的版本升到最新版。升级前要备份数据库。theme拷贝到新服务器中。

@ 不支持从candidate release升级到final。所以还是不要下载candidate release版本的吧。

@ 新适配器要拷贝到application服务器中

keycloak\distribution\adapters是生成的adapters，或者从官网下载对应版本的adapter。

以wildfly-adapter为例：

adapter放在keycloak\distribution\adapters\wildfly-adapter\wildfly-adapter-zip\target\unpacked\org\keycloak\下，将所有文件夹拷贝到
应用服务器wildfly-10.0.0.Final\modules\system\add-ons\keycloak\org\keycloak\内。

@ 数据库jdbc驱动要拷贝到keycloak服务器中

postgresql驱动

* 创建文件夹modules\system\layers\base\org\postgresql\main 
* 拷贝postgresql驱动 postgresql-9.4-1204.jdbc42.jar到main文件夹下
* 创建module.xml文件
```
<?xml version="1.0" encoding="UTF-8"?>
<module xmlns="urn:jboss:module:1.3" name="org.postgresql">
  <resources>
    <resource-root path="postgresql-9.4-1204.jdbc42.jar"/>
        <!-- Insert resources here -->
  </resources>
  <dependencies>
    <module name="javax.api"/>
    <module name="javax.transaction.api"/>
  </dependencies>
</module>
```
###### Note：可以使用wildfly/bin/jboss-cli.sh脚本添加module。

@standalone.xml的数据库配置
```
        <subsystem xmlns="urn:jboss:domain:datasources:4.0">
            <datasources>
				<datasource jndi-name="java:jboss/datasources/ExampleDS" pool-name="ExampleDS" enabled="true" use-java-context="true">
                    <connection-url>jdbc:postgresql://localhost:5432/keycloak_ExampleDS</connection-url>
                    <driver>postgresql</driver>
                    <security>
                        <user-name>postgres</user-name>
                        <password>369</password>
                    </security>
                </datasource>
				<datasource jndi-name="java:jboss/datasources/KeycloakDS" pool-name="KeycloakDS" enabled="true" use-java-context="true">
                    <connection-url>jdbc:postgresql://localhost:5432/keycloak</connection-url>
                    <driver>postgresql</driver>
                    <security>
                        <user-name>postgres</user-name>
                        <password>369</password>
                    </security>
                </datasource>
                <drivers>
                    <driver name="h2" module="com.h2database.h2">
                        <xa-datasource-class>org.h2.jdbcx.JdbcDataSource</xa-datasource-class>
                    </driver>
					<driver name="postgresql" module="org.postgresql">
                        <xa-datasource-class>org.postgresql.xa.PGXADataSource</xa-datasource-class>
                    </driver>
                </drivers>
            </datasources>
        </subsystem>
```

@ 转移keycloak-server.json文件

这个文件在2.2.0版还存在，在2.5.0时看不到了。暂时还好用，keycloak建议在standalone.xml，standalone-ha.xml或domain.xml配置。转换方式略。