layout: post
title: 'CentOS报错: Could not resolve host: mirrorlist.centos.org; Unknown error'
tags:
  - Linux
  - CentOS
categories:
  - Linux
  - CentOS
date: 2018-05-09 10:40:00
---
由于其他错误，执行了
```
$ sudo yum clean metadata
$ sudo yum clean all
```
在更新资源时报
```
Could not retrieve mirrorlist http://mirrorlist.centos.org/?release=7&arch=x86_64&repo=os&infra=stock32 error was
14: curl#6 - "Could not resolve host: mirrorlist.centos.org; Unknown error"
 
One of the configured repositories failed (Unknown),
and yum doesn't have enough cached data to continue. At this point the only
safe thing yum can do is fail. There are a few ways to work "fix" this:
 
 1. Contact the upstream for the repository and get them to fix the problem.
 
 2. Reconfigure the baseurl/etc. for the repository, to point to a working
    upstream. This is most often useful if you are using a newer
    distribution release than is supported by the repository (and the
    packages for the previous distribution release still work).
 
 3. Disable the repository, so yum won't use it by default. Yum will then
    just ignore the repository until you permanently enable it again or use
    --enablerepo for temporary usage:
 
        yum-config-manager --disable <repoid>
 
 4. Configure the failing repository to be skipped, if it is unavailable.
    Note that yum will try to contact the repo. when it runs most commands,
    so will have to try and fail each time (and thus. yum will be be much
    slower). If it is a very temporary problem though, this is often a nice
    compromise:
 
        yum-config-manager --save --setopt=<repoid>.skip_if_unavailable=true
```
配置下网络即可：
1. 查看网卡配置文件：
```
$ cd /etc/sysconfig/network-scripts
$ ls
```
```
ifcfg-ens32      ifdown-post      ifup-eth     ifup-sit
ifcfg-ens32.bak  ifdown-ppp       ifup-ib      ifup-Team
ifcfg-lo         ifdown-routes    ifup-ippp    ifup-TeamPort
ifdown           ifdown-sit       ifup-ipv6    ifup-tunnel
ifdown-bnep      ifdown-Team      ifup-isdn    ifup-wireless
ifdown-eth       ifdown-TeamPort  ifup-plip    init.ipv6-global
ifdown-ib        ifdown-tunnel    ifup-plusb   network-functions
ifdown-ippp      ifup             ifup-post    network-functions-ipv6
ifdown-ipv6      ifup-aliases     ifup-ppp
ifdown-isdn      ifup-bnep        ifup-routes
```
第一个`ifcfg-ens32`就是网卡配置文件，不同机器略有不同。
2. 备份：
```
$ sudo cp /etc/sysconfig/network-scripts/ifcfg-ens32 /etc/sysconfig/network-scripts/ifcfg-ens32.bak
```
3. 编辑，然后修改内容。(内容大致如下)
```
TYPE=Ethernet
BOOTPROTO=static
IPADDR=192.168.1.74
GATEWAY=192.168.1.1
NETMASK=255.255.255.0
DNS1=8.8.8.8
DEFROUTE=yes
PEERDNS=yes
PEERROUTES=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=ens32
UUID=fbe7146b-693d-4047-b73c-fd685a76337b
DEVICE=ens32
ONBOOT=yes
```
针对动态配置，需要设置：
```
BOOTPROTO=dhcp
ONBOOT=yes
```
针对静态配置，需要设置：
```
BOOTPROTO=static
ONBOOT=yes
```
静态配置设置固定IP，网关，DNS：
```
IPADDR=192.168.1.74
GATEWAY=192.168.1.1
NETMASK=255.255.255.0
DNS1=8.8.8.8
```
4. 重启网络服务并更新资源
```
$ sudo systemctl restart network
$ sudo yum update
```

参考文章： [[1]](https://www.cnblogs.com/xixihuang/p/5404517.html)

