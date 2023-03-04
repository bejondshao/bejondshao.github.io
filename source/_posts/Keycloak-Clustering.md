layout: post
title: Keycloak Clustering
tags:
  - Java
  - Keycloak
category:
  - Keycloak
categories:
  - DevOps
  - Keycloak
date: 2017-01-22 17:50:00
---
@ Standalone Clustered Configuration
打包好的包邮预定好的给云部署的服务器配置文件，/standalone/configuration/standalone-ha.xml。里面包括所有基本设置，包括网络，数据库，缓存和discovery。配置里有一点没配置。你要配置共享的数据库连接才能启动云架构。同时也需要在云之上部署负载均衡。
启动脚本
```
\bin\standalone.bat --server-config=standalone-ha.xml
```
@ Domain Clustered Mode
Domain mode是集中管理和发布服务器配置的一种方式。
使用standalone方式配置云会慢慢随着节点增多而增加维护成本。每次更改配置文件都需要更改每个节点的内容。Domain mode就解决了这个问题，它提供一个管理中心。配置Domain mode一开始会很复杂，但是是一劳永逸的。这也是为什么Keycloak选择在wildfly上部署。
domain mode有几个概念需要了解一下。
**domain controller**
域控制器是存储，管理发布基本设置的进程。其他节点会从这里获取配置。
**host controller**
主机控制器用于在具体机器上管理节点。你会配置主机控制器运行一个或多个服务器节点。域控制器也和主机控制器交流。为了减少运行的进程，域管理器一般也当做主机管理器使用。
**domain profile**
域档案是有名字的固定配置，用于服务器启动。可以创建多个存档用于不同的服务器。
**server group**
服务器群是服务器的集合，被统一管理和配置。可以分配域档案给一个群，这样群里的服务器都用同样的域档案。
在域模型中，域控制器以主要节点的身份启动。然后主机控制器在每个机器上启动。每个主机控制器部署配置写明有多少个Keycloak服务器会在这个机器上运行。当主机控制器启动时，就会启动相应的keycloak服务器，然后从域控制器拉取配置。
@ Domain Configuration
配置文件是/domain/configuration/domain.xml。auth-server-standalone和auth-server-clustered的profile节点是应该配置的。同样也应该配置网络连接，缓存，数据库。
auth-server profile
```
    <profiles>
        <profile name="auth-server-standalone">
            ...
         </profile>
       <profile name="auth-server-clustered">
           ...
        </profile>
```

auth-server-standalone是非云架构的，auth-server-clustered是云架构的。
另外还有许多socket-binding-groups

```
    <socket-binding-groups>
        <socket-binding-group name="standard-sockets" default-interface="public">
           ...
        </socket-binding-group>
        <socket-binding-group name="ha-sockets" default-interface="public">
           ...
        </socket-binding-group>
        <!-- load-balancer-sockets should be removed in production systems and replaced with a better softare or hardare based one -->
        <socket-binding-group name="load-balancer-sockets" default-interface="public">
           ...
        </socket-binding-group>
    </socket-binding-groups>
```

此配置定义与每个Keycloak服务器实例一起打开的各种连接器的默认端口映射。每个带`${}`符号的值都可以在命令行里用-D作为参数覆盖
`$ domain.sh -Djboss.http.port=80`

@ 给Keycloak定义的server group在server-groups节点里。它确定域档案是用"default"，并且有一些默认的Java VM启动参数。绑定了一个socket-binding-group到服务器组里。
```
    <server-groups>
        <!-- load-balancer-group should be removed in production systems and replaced with a better softare or hardare based one -->
        <server-group name="load-balancer-group" profile="load-balancer">
            <jvm name="default">
                <heap size="64m" max-size="512m"/>
            </jvm>
            <socket-binding-group ref="load-balancer-sockets"/>
        </server-group>
        <server-group name="auth-server-group" profile="auth-server-clustered">
            <jvm name="default">
                <heap size="64m" max-size="512m"/>
            </jvm>
            <socket-binding-group ref="ha-sockets"/>
        </server-group>
    </server-groups>
```

@ 主机控制器配置
Keycloak有两个主机控制器文件/domain/configuration/host-master.xml和host-slave.xml。host-master.xml是配置域控制器，负载均衡，以及一个keycloak实例。host-slave.xml用于和域控制器交流，启动一个keycloak实例。
Note: 负载均衡不是必须的服务。使用负载均衡可以在开发机器上方便的测试云架构。在生产环境，你可以用其他负载均衡的软硬件代替它。
可以移除load-balancer项禁用负载均衡
```
    <servers>
        <!-- remove or comment out next line -->
        <server name="load-balancer" group="loadbalancer-group"/>
        ...
    </servers>
```

另外一个需要注意的是声明验证服务器实例。有个属性叫port-offset。其他在socket-binding-group或server group里定义的端口都会加上port-offset，防止端口冲突。
```
    <servers>
        ...
        <server name="server-one" group="auth-server-group" auto-start="true">
             <socket-bindings port-offset="150"/>
        </server>
    </servers>
```

@ 服务器实例工作目录
每个在host files定义的keycloak服务器实例都在/domain/servers/创建一个server_name目录。额外的配置可以放在那里，临时数据，日志也会放在哪里。结构就像其他wlidfly服务器内容管理一样。
@ Domain Boot Script

```
$ .../bin/domain.sh --host-config=host-master.xml
> ...\bin\domain.bat --host-config=host-slave.xml
```

@ Clustered Domain Example
你可以使用配置好的domain.xml，启动它会在一个机器上启动：
一个域控制器
一个HTTP负载均衡
两个Keycloak实例
如果想在两个机器启动实例，需要执行domain.sh两次，来启动两个独立的主机管理器。第一个作为主机会启动域控制器，HTTP负载均衡和一个keycloak服务器。第二个从属主机控制器会启动一个keycloak服务器。
@ 将从属与域控制器关联
在启动从属之前，要配置从属的主机控制器以便于安全的和域控制器通讯。如果不配置，从属主机就无法从域控制器获取配置。配置安全连接需要创建一个服务器管理员，在主机和从属机共享。通过/bin/add-user.sh脚本添加。
执行脚本，选择a，Management User，当它询问这个新用户是否是用于AS进程连接另一个，回复yes。然后会生成一个密码，将密码复制到../domain/configuration/host-slave.xml里

```
$ add-user.sh
 What type of user do you wish to add?
  a) Management User (mgmt-users.properties)
  b) Application User (application-users.properties)
 (a): a
 Enter the details of the new user to add.
 Using realm 'ManagementRealm' as discovered from the existing property files.
 Username : admin
 Password recommendations are listed below. To modify these restrictions edit the add-user.properties configuration file.
  - The password should not be one of the following restricted values {root, admin, administrator}
  - The password should contain at least 8 characters, 1 alphabetic character(s), 1 digit(s), 1 non-alphanumeric symbol(s)
  - The password should be different from the username
 Password :
 Re-enter Password :
 What groups do you want this user to belong to? (Please enter a comma separated list, or leave blank for none)[ ]:
 About to add user 'admin' for realm 'ManagementRealm'
 Is this correct yes/no? yes
 Added user 'admin' to file '/.../standalone/configuration/mgmt-users.properties'
 Added user 'admin' to file '/.../domain/configuration/mgmt-users.properties'
 Added user 'admin' with groups to file '/.../standalone/configuration/mgmt-groups.properties'
 Added user 'admin' with groups to file '/.../domain/configuration/mgmt-groups.properties'
 Is this new user going to be used for one AS process to connect to another AS process?
 e.g. for a slave host controller connecting to the master or for a Remoting connection for server to server EJB calls.
 yes/no? yes
 To represent the user add the following to the server-identities definition <secret value="bWdtdDEyMyE=" />
 <management>
         <security-realms>
             <security-realm name="ManagementRealm">
                 <server-identities>
                     <secret value="bWdtdDEyMyE="/>
                 </server-identities>

```


*Note: add-user.sh不是添加keycloak用户，是添加JBoss Enterprise Application Platform。*
@ 由于我们在一个机器上运行两个节点，就需要启动两次脚本：
```
$ domain.sh --host-config=host-master.xml
$ domain.sh --host-config=host-slave.xml
```

然后尝试访问http://localhost:8080/auth