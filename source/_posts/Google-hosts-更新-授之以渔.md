layout: post
title: Google hosts 更新(授之以渔)
date: 2016-03-10 17:17:39
tags:
- hosts
- Google
category:
- Others
---
此文记录我获取hosts的几个途径.

一. racaljk 的host获取工具
https://github.com/racaljk/hosts
里面有详细的不同平台的获取和更改hosts的方法.
对于linux, 里面提到一个一条命令更新hosts的方法, 简直是福音啊!
```language
bash -c 'wget https://raw.githubusercontent.com/racaljk/hosts/master/hosts -qO /tmp/hosts && sudo mv /tmp/hosts /etc/hosts'
```
@ bash -c 
若用-c参数，则bash从字符串中读入命令，如果字符串后还有变量就被设定为从$0开始的位置参数
@ wget -qO
-q, --quiet不显示输出信息
-O, --output-document=FILE 指定下载目录和文件名

这条命令就是从远端下载定期更新的文件hosts, 保存为/tmp/hosts文件, 并拷贝/tmp/hosts到/etc/hosts. 其实生效的是/etc/hosts文件. 这条语句需要root权限.

这条语句其实不算完美, 因为暴力的用/tmp/hosts替换了/etc/hosts. 其实在做任何配置的更改前, 最好备份原数据. 所以我对上条语句进行了优化.
```language
bash -c 'wget https://raw.githubusercontent.com/racaljk/hosts/master/hosts -O /tmp/hosts && cp /etc/hosts /etc/hosts.bak && sudo mv /tmp/hosts /etc/hosts && echo "127.0.0.1 yourhostname" >> /etc/hosts'
```
我把wget -q去掉了, 这样可以看到下载的信息, 心里有个底儿. 
另外我在替换前备份了原hosts.
最后添加主机名到127.0.0.1, 详情见 http://bejondshao.github.io/2016/03/10/sudo-unable-to-solve-host-bejond/

二. 盒子 整理发布的hosts

http://www.360kb.com/kb/2_150.html
好处是直观简介, 复制下来贴到hosts里就行. 这个域名暂时还没有被和谐, 祝好运.

三. 老D
http://laod.cn/hosts/2016-google-hosts.html
这个hosts更新也比较快, 但是更新hosts需要到百度盘,输入提取码,下载zip文件, 并且zip还带密码, 必须按照他说的做.
奇葩的是, 域名竟然是.cn的, 看来老D网站流量挺大, 祈福这个cn域名不会被和谐吧.

四. 百度google hosts
这些网站的hosts极不稳定, 很多网站为了流量而贴的过期hosts, 有点坑人. 所以要有未雨绸缪的精神, 在你当前hosts好用的情况下, 用google或者bing搜索好的hosts资源.

五. 付费VPN
这个也是最简单,最直接,比较方便的一条途径.
其实VPN也不算太贵, 一个月十到十五块的价格, 平均起来每天也不到一块. 我觉得每次看到google无限风火轮对精神的摧残是远远大于这几毛钱的花费的. 一般是包月, 我推荐你买服务前最好先到VPN测评网站看看VPN口碑, 还有要找准VPN正确的官方网站, 很多山寨网站冒充原主的名字, 等你交了钱却没有服务就傻眼了. 这里就不推荐了. 但是给点建议是买服务可以买三个月试试, 或者半年. 我一次性买了两年就有点后悔, 虽然一次买久了价格能便宜点, 但是速度慢也是比较令人惆怅的, 钱都交了, 你能咋地.

简单是付了钱, 就好像你在国外上网了, 所以所有的网站都畅通了. 但是访问国内网站会很慢, 甚至有的网站无法访问, 因为国外也会屏蔽中国的视频网站,比如土豆, 优酷等等. 还有很多在线音乐无法使用. 还有迅雷下载时很慢. 这样就导致你不得不时不时的切换VPN, 所以说比较方便.