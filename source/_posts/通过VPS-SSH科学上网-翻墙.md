---
layout: post
title: 通过VPS+SSH科学上网(翻墙)
date: 2017-10-23 17:21:22
tags:
- Google
category:
- Others
---
之前一直用的蓝灯，免费的一个终端每月500M流量。的确很好用，可以"管理系统代理"，即电脑内的软件都可以畅通无阻，也可以设定https或socks代理，这样浏览器配置后就可以访问了，系统其他软件不受影响。蓝灯另外个优点是"代理部分流量"，就是只在访问被封的资源时走蓝灯，其他走默认网络，这样访问国内资源快，且不会被限制使用国内服务。免费的使用谷歌速度尚可，看视频是不可能的。

后来，蓝灯不好用了，*也不知道哪天能好用*。

有好多VPN服务商，也有好多山寨VPN，好多就是试用前期嗷嗷快，付费后就要么登不上，要么总掉线，要么很卡，甚至多一段时间停止服务跑路了。当然也有靠谱的VPN，一个月100左右，用不起。所以只能自己租个VPS来用了。

##### VPS的选择

我只有一条建议：不要过分相信推荐，更不要选排名靠前的服务商。排名靠前的弊端是用户扎堆，服务器超额销售，网络带宽不足，流量明显，可能整个ip段被加入TGW中。

我举个反面教材，vultr，有Japan的主机，但是压根ping不通，所以不得不用美国的ip。

##### 最简单的科学上网，通过SSH连接


1.选个服务商，组个主机，安装个linux系统。

2.如果你使用的是linux，执行

```
$ ssh -D 7701 root@43.70.102.19
```

-D是dynamic的意思，7701是SSH访问的端口（任意），root改成vps里的用户名，@后面的ip改成vps的固定ip。

输入后，会提示你输入密码（使用ssh证书就不需要输入密码了），登录成功，就连接到了远端vps。
3.打开firefox, 安装foxyproxy standard插件。

4.打开foxyproxy settings, 编辑Default proxy，在Proxy Details页签中，选中"Direct internet connection(no proxy)"，点击OK

   {% asset_img disable-default-proxy.png [Disable default proxy] %}

5.创建新的proxy，"Add new proxy", 名为"proxy"，打开Proxy Details页签， 选中"Manual Proxy Configuration", Server or IP Address填入127.0.0.1，port填入7701。选中SOCKS proxy, 选择SOCKS v5，点击OK
{% asset_img add-proxy.png [Add new proxy] %}

6.添加Pattern Subscriptions，这一步通过增加订阅pattern，用于过滤国内ip不使用ssh访问。
点击"Add New Pattern Subscription"，选中Enable, Subscription URL填入"https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt "。在Proxies一栏中，点击"Add Porxies"，选中上一步创建的proxy。Refresh Rate填1440。Format选择FoxyProxy。Obfuscation选择Base64，点击OK。
{% asset_img add-pattern-subscriptions.png [Add Pattern Subscriptions] %}
7.编辑proxy pattern, 打开URL Patterns, Add New Pattern。选中Enable, Pattern name随意，URL pattern填入"https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt "，URL Inclusion/Exclusion选中Whitelist。点击OK。
{% asset_img add-new-pattern.png [Add New Pattern] %}
8.右键foxyproxy，选中"Use proxies based on their pre-defined patterns and priorities"

至此，就可以访问谷歌了。