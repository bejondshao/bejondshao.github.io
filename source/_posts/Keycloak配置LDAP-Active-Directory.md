layout: post
title: Keycloak配置LDAP(Active Directory)
tags:
  - Keycloak
  - DevOps
categories:
  - DevOps
  - Keycloak
date: 2018-07-18 22:40:00
---
@ Keycloak后台配置如下图，相关内容根据实际情况修改
{% asset_img ldaps-work.png Keycloak LDAP配置 %}

* 其中Connection URL中的ip修改一下。
* Users DN：根据AD的域定义，能指定到Users目录
* Bind DN：填写管理员的mapping。比如这里keycloak1在AD里属于管理员组。
* Bind Credential是keycloak1的密码

@ Active Directory创建管理员账户：

* 在AD中创建用户keycloak1，组设置为Administrators, Domain Admins, Domain Users。如图所示
{% asset_img keycloak1组.png Keycloak User %}

@ 由于Keycloak要更新AD用户密码，AD要求使用SSL安全连接，所以要配置AD SSL连接。

@ AD配置证书服务

* 开始菜单->管理工具->服务器管理
* 角色->添加角色
{% asset_img Roles-Summary.png Roles Summary  %}
* 点击“下一步”，在“服务器角色”，选择“Active Directory 证书服务”，点击“下一步”
{% asset_img Select-Server-Roles.png Select Server Roles %}
* 点击“下一步”，默认，选择“证书授权”
{% asset_img Select-Role-Services.png Select Role Services %}
* 点击“下一步”，默认，选择“企业”证书
{% asset_img Specify-Setup-Type.png Specify Setup Type %}
* 点击“下一步”，默认，选择“根证书”
{% asset_img Specify-CA-Type.png Specify CA Type  %}
* 下一步，默认，选择“创建新的私有秘钥”
{% asset_img Set-Up-Private-Key.png Set Up Private Key %}
* 下一步，默认内容即可
{% asset_img Configure-CA-Name.png Configure CA Name  %}
* 下一步，证书有效期，5年到99年都可以
{% asset_img Set-Validity-Period.png Set Validity Period %}
* 下一步，证书数据地址，默认即可
{% asset_img Configure-Certificate-Database.png Configure Certificate Database  %}
* 下一步，确认信息，安装
{% asset_img Confirm-Installation-Selections.png Confirm Installation Selections  %}
{% asset_img Installation-Results.png Installation Results %}
* 重启Windows Server 2008

@ 安装完证书，将证书导出，供客户端（Keycloak所在的机器）使用
打开一个cmd，执行命令`certutil -ca.cert ad-client.crt`，到"C:\Users\Administrator"文件夹，将ad-client.crt拷贝到Keycloak主机。

@ 开启”对安全通道数据进行数字加密或数字签名“
* 开始->管理工具->组策略管理器。然后依次打开”林：bejond.org"->域->bejond.org->Domain Controller->Default Domain Controllers Policy，右键，编辑。
{% asset_img 组策略管理器.png  %}
{% asset_img domain controller.png  %}
* 在“组策略管理编辑器”中，依次打开，计算机配置->策略->Windows 设置->本地策略->安全选项，找到”域成员：对安全通道数据进行数字加密或数字签名（始终）“，双击，选择”已启用“。确定。
{% asset_img 域成员.png  %}

* 打开cmd，执行`gpupdate`，更新域控制器的策略。

@ 在Keycloak主机，打开cmd，执行命令`keytool -importcert -keystore "%JAVA_HOME%/jre/lib/security/cacerts" -file ad-client.crt`。其中%JAVA_HOME%是Java环境变量，如果没有配置或找不到变量，到Java安装目录，定位到cacerts文件，将路径拷贝到命令里就行，比如`keytool -importcert -keystore C:\jdk1.8\jre\lib\security\cacerts -file ad-client.crt`。然后会提示输入证书密码，输入"changeit"。然后会询问是否添加证书，输入"y"即可。

* 此时，已经配置完成。重启Keycloak。