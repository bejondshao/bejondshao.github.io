---
layout: post
title: Keycloak开发环境搭建
date: 2017-11-01 09:55:43
tags:
- Java
- Keycloak
category:
- Keycloak
---
1. 克隆Keycloak源码
`git clone git@github.com:keycloak/keycloak.git`
2. Build项目
`mvn clean install -Pdistribution -DskipTests=True`
Keycloak包会编译到distribution中，在server-dist/target/keycloak-<version>会找到一个wildfly结构的keycloak server。
3. Build出的包直接启动会报moudule keycloak-server-subsystem not found，需要在/keycloak-xxx/modules/目录下创建layers.conf文件，文件内容为：
`layers=keycloak`
4. 修改Keycloak数据库，以Postgresql为例：
a. 在keycloak-<version>/modules/system/layers/base/org/下创建文件夹postgresql，在postgresql下创建文件夹main。
b. 到maven库下载postgresql对应版本的驱动，比如https://mvnrepository.com/artifact/org.postgresql/postgresql/9.4.1212 ，放到main目录下。
c. 在main目录下创建module.xml文件，内容为：
```
<?xml version="1.0" encoding="UTF-8"?>
<module xmlns="urn:jboss:module:1.3" name="org.postgresql">
  <resources>
    <resource-root path="postgresql-9.4-1212.jar"/>
        <!-- Insert resources here -->
  </resources>
  <dependencies>
    <module name="javax.api"/>
    <module name="javax.transaction.api"/>
  </dependencies>
</module>
```
d. 编辑keycloak-<version>/standalone/configuration/standalone.xml文件，更改数据库：
找到`<subsystem xmlns="urn:jboss:domain:datasources:5.0">`，将datasources替换为：
```<datasources>
                <datasource jndi-name="java:jboss/datasources/ExampleDS" pool-name="ExampleDS" enabled="true" use-java-context="true">
                    <connection-url>jdbc:postgresql://127.0.0.1:5432/keycloak_ExampleDS</connection-url>
                    <driver>postgresql</driver>
                    <pool>
                        <min-pool-size>10</min-pool-size>
                        <max-pool-size>20</max-pool-size>
                        <prefill>false</prefill>
                        <use-strict-min>false</use-strict-min>
                        <flush-strategy>FailingConnectionOnly</flush-strategy>
                    </pool>
                    <security>
                        <user-name>postgres</user-name>
                        <password>369</password>
                    </security>
                    <validation>
                        <check-valid-connection-sql>SELECT 1</check-valid-connection-sql>
                        <validate-on-match>false</validate-on-match>
                        <background-validation>false</background-validation>
                        <use-fast-fail>false</use-fast-fail>
                    </validation>
                </datasource>
                <datasource jndi-name="java:jboss/datasources/KeycloakDS" pool-name="KeycloakDS" enabled="true" use-java-context="true">
                    <connection-url>jdbc:postgresql://127.0.0.1:5432/keycloak</connection-url>
                    <driver>postgresql</driver>
                    <pool>
                        <min-pool-size>10</min-pool-size>
                        <max-pool-size>20</max-pool-size>
                        <prefill>false</prefill>
                        <use-strict-min>false</use-strict-min>
                        <flush-strategy>FailingConnectionOnly</flush-strategy>
                    </pool>
                    <security>
                        <user-name>postgres</user-name>
                        <password>369</password>
                    </security>
                    <validation>
                        <check-valid-connection-sql>SELECT 1</check-valid-connection-sql>
                        <validate-on-match>false</validate-on-match>
                        <background-validation>false</background-validation>
                        <use-fast-fail>false</use-fast-fail>
                    </validation>
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
```
其中connection-url改为相应的server和数据库。
5. 至此，配置完成，进入keycloak-<version>/bin/，启动 standalone.sh（.bat）即可访问http://localhost:8080/auth 。

6. 如果在启动时出现

```
Caused by: org.jboss.modules.ModuleNotFoundException: org.keycloak.keycloak-server-subsystem
	at org.jboss.modules.ModuleLoader.loadModule(ModuleLoader.java:294)
	at org.jboss.modules.ModuleLoader.loadModule(ModuleLoader.java:280)
	at org.jboss.as.controller.parsing.DeferredExtensionContext.loadModule(DeferredExtensionContext.java:111)
	... 10 more

```
问题，参考[Stackoverflow解答](https://stackoverflow.com/a/45700683/3908814)，需要在keycloak-<version>/modules/目录下创建文件layers.conf，内容如下：
```
layers=keycloak
```
下面有人解释说：这样做是在wildfly中开启keycloak subsystem模块。否则所有的keycloak类都无法被加载。这个文件在编译时没有被拷贝，很是奇怪。