layout: post
title: Issues met after installing xubuntu 16.04.2
date: 2017-04-08 18:32:38
tags:
- Linux
category:
- Linux
---
@ 安装无线网卡（Wireless network）
http://jingyan.baidu.com/article/9989c7463cf43cf648ecfe21.html?st=5&os=1&bd_page_type=1&net_type=3
{% asset_img dkms.png dkms %}
简短来说就是插入ubuntu安装U盘，打开/pool/main/d/dkms/安装dkms_xxx_all.deb文件 (In short, plug in the ubuntu installation disk, open /pool/main/d/dkms folder, install dkms_xxx_all.deb file)
`$ sudo dpkg -i xxx.deb`

{% asset_img bcmwl.png bcmwl %}
打开/pool/restricted/b/bcmwl/安装bcmwl-kernel-source_xxx.deb文件 ( go to /pool/restricted/b/bcmwl folder, install bcmwl_kernel-source_xxx.deb file)

@ 安装为知笔记 (Wiznote)
```
$ sudo add-apt-repository ppa:wiznote-team
$ sudo apt-get update
$ sudo apt-get install wiznote
```

@ 关闭防火墙 (turn off firewall)
`$ sudo ufw disable`

@ 在/etc/下没有samba文件夹，需要安装samba (samba)
`$ sudo apt-get install samba samba-common system-config-samba winbind`

@ 安装smbtree (smbtree)
`$ sudo apt install smbclient`

@ sogoupinyin
重启后好用了

@ 安装软件时提示lock文件被锁住(You get the lock issue while installing packages)
```
$ sudo rm /var/lib/dpkg/lock
$ sudo dpkg --configure -a
$ sudo apt-get -f install
```

@ install git-man
got error 
*Depends: git-man (< 1:2.7.4-.) but 1:2.11.0-2~ppa0~ubuntu12.04.1 is to be installed
e unable to correct problems you have held broken packages. debian*
try this: 
`$ sudo apt-get install git-man`
 got *Err:1 http://ppa.launchpad.net/fcitx-team/nightly/ubuntu xenial/main i386 git-man all 1:2.11.0-2~ppa0~ubuntu12.04.1
  404  Not Found*
 disable fcitx-team，rerun 
` $ sudo apt-get install git-man`
got *Sub-process /usr/bin/dpkg returned an error code (1)*
```
$ sudo rm /etc/apt/sources.list
$ sudo apt-get install git-man
```
*WARNING:root:could not open file '/etc/apt/sources.list'*
`$ software-properties-gtk`
check the repositories
`$ sudo apt-get update`
`$ sudo apt-get install git`
总结，归根结底是sources.list出问题了，移除后执行software-properties-gtk添加sources.list。在安装软件时切记不要着急，同时安装两个软件，或者在执行更新时安装软件。
（In conclude, there's sth wrong with sources.list, after I remove it. I need to regenerate sources.list. Please be patient when installing packages, DO NOT install packages or do update in parallel.）

@ 如果执行 `$sudo dpkg -i xxx.deb`
出现依赖问题，执行
`$ sudo apt-get -f install`
会安装依赖并重新安装deb包
（if you get dependencies issue while `$ sudo dpkg -i xxx.deb`, you need to do `$sudo apt-get -f install`. It'll install all dependencies and the target package.）
