---
layout: post
title: 如何减少firefox内存用量
date: 2017-12-12 17:51:32
tags:
- Firefox
category:
- Others
---
一. 在浏览器地址栏输入about:config

* browser.cache.memory.enable 默认值true

缓存图片内容到内存，我设置成false

* browser.sessionhistory.max_entries 默认值50

减少session历史记录，我设置成5

* config.trim_on_minimize

添加属性，用于在最小化时释放内存，True

二. 打开设置，在Performance中取消选中“Use recommended performance settings”

然后Content process limit设置为1或2。

三. 禁用主题，禁用非必须插件

尤其是Adblock Plus，虽然这个插件很好用，但是如果你觉得浏览器很慢而又找不到原因时，很可能是这个插件导致的。电脑内存8G，下面是我测试插件的结果。

- 在浏览器关闭时，内存用量为66%：

{% asset_img nofirefox.png %}

- 开启浏览器，Content process limit为4，开启adblock plus，内存为72%：

{% asset_img 4process-adb-empty.png %}

- 开启浏览器，Content process limit为4，禁用adblock plus，内存为70%，差距还不是很明显：

{% asset_img 4process-no-adb-empty.png %}

- 测试如下10个页签，所有测试都设置Content process limit为2：
    * https://stackoverflow.com/
    * http://www.163.com/
    * http://www.sina.com/
    * http://www.people.com.cn/
    * http://www.xinhuanet.com/
    * https://www.jd.com/
    * https://www.mozilla.org/en-US/
    * https://www.davidtan.org/tips-reduce-firefox-memory-cache-usage/
    * https://stackoverflow.com/questions/5227675/good-location-for-android-keystore
    * https://security.stackexchange.com/questions/100584/where-should-a-keystore-jks-be-stored-in-a-repository

开启adblock plus内存使用为82%，关闭adblock plus内存使用为77%，可以推测页签越多影响越大，我一般会开启20到30个页签。

{% asset_img 2process-adb.png 两个线程，开启adb %}

{% asset_img 2process-no-adb.png 两个线程，关闭adb %}

顺便，我又测试了一个线程关闭adb的情况，内存使用为76%，可见每个线程数对内存占用影响大约为1%，多线程会加快页签处理速度。

{% asset_img 1process-no-adb.png 一个线程，关闭adb %}

