layout: post
title: Google hosts 更新(授之以渔)
date: 2016-03-10 17:17:39
tags:
- Hosts
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

这条命令就是从远端下载定期更新的文件hosts, 保存为/tmp/hosts文件, 并移动/tmp/hosts到/etc/hosts. 其实生效的是/etc/hosts文件. 这条语句需要root权限.

这条语句其实不算完美, 因为暴力的用/tmp/hosts替换了/etc/hosts. 其实在做任何配置的更改前, 最好备份原数据. 所以我对上条语句进行了优化.
```language
bash -c 'wget https://raw.githubusercontent.com/racaljk/hosts/master/hosts -O /tmp/hosts && cp /etc/hosts /etc/hosts.bak && sudo mv /tmp/hosts /etc/hosts && echo "127.0.0.1 yourhostname" >> /etc/hosts'
```
我把wget -q去掉了, 这样可以看到下载的信息, 心里有个底儿. 
另外我在替换前备份了原hosts.
最后添加主机名到127.0.0.1, 详情见 http://bejondshao.github.io/2016/03/10/sudo-unable-to-solve-host-bejond/

对于Mac OS，https://github.com/racaljk/hosts 推荐了手动更改，或者用Gas Mask。手动更改不推荐，因为mac下的hosts保存在/private/etc/hosts，这个位置无法通过Finder直接进入，需要用快捷键"Cmd Shift G"，然后在弹出的窗口里输入"/private/etc"，这样找到hosts，并且还要更改hosts文件的权限，很麻烦。另外，我也试了Gas Mask，这个工具很好，因为它直接获取了hosts，直接打开，我们编辑后保存就可以了。

Mac OS其实是like Unix的，所以Linux能运行的命令基本上在Mac上也能运行。所以我把上面的命令更改了下，大体上差不多，只不过需要注意：1. 运行命令时要确保Gas Mask处于关闭状态，因为Gas Mask监控hosts，会限制其他操作对hosts的修改。我在运行命令一开始总是无法更新hosts，后来关闭Gas Mask就能更新成功了。
```
bash -c 'wget https://raw.githubusercontent.com/racaljk/hosts/master/hosts -O /tmp/hosts && mv /private/etc/hosts /private/etc/hosts.bak && cp /tmp/hosts /private/etc/hosts && echo "127.0.0.1 bejond" >> /private/etc/hosts' 
```
一个命令，就完成了更新hosts的操作，很方便。至于Windows理论上也能做到，只不过 bash, wget, mv, cp, echo等命令需要更改为适合Windows的。

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

还有很重要一点，除了Google，还有维基百科，很多国外的说不定什么时候会用到的网站，以及Youtube上的技术教程，sourceforge.net等等，尤其是第三方jar，maven库这类资源往往无法通过设定hosts来解决网络问题。这时候还必须使用VPN了。

还有一个方法是自己搭建VPS来实现VPN，亚马逊AWS有一年免费试用主机。我曾试过，速度挺好，比较稳定，也是一种实现翻墙的方式。这类实现的好处是自己使用服务，不会出现速度慢的情况，并且数据也不会被截取。

好了，以上的几种方式就是实现翻墙的，其实最终目的不是去看外面的世界，我并没有觉得墙外的风景更美，而是技术需求。在这里还是提醒下大家，不要为了翻墙而翻墙，要想想自己为什么这么做，目的是什么。