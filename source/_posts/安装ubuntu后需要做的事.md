---

layout: post

title: 安装ubuntu后需要做的事

date: 2017-10-27 18:45:10

tags:

- Linux

category:

- Linux

---

@ 安装linux前墙裂建议将/home单独分区，墙裂建议。

@ 修改root用户密码
`$ sudo passwd root`

@ 安装软件需要依赖时可以执行，依赖和软件包就都安装好了
`$ sudo apt-get -f install`

@ 安装过程需要找依赖包，也可以到https://packages.ubuntu.com/#search_contents 找。

@ 安装wineQQ（https://pan.baidu.com/s/1c2Mvxyo）
`$ sudo dpkg -i fonts-wqy-microhei_0.2.0-beta-2_all.deb ttf-wqy-microhei_0.2.0-beta-2_all.deb wine-qqintl_0.1.3-2_i386.deb`

安装后在手机端取消设备锁才能登录

@ WizNote（比较好用的同步笔记）
```
$ sudo add-apt-repository ppa:wiznote-team
$ sudo apt-get update
$ sudo apt-get install wiznote
$ WizNote
```

@ clipman（记录粘贴板历史，墙裂推荐）
`$ sudo apt-get install xfce4-clipman`

@ haroopad（http://pad.haroopress.com/user.html 比较好用的本地markdown编辑器）

@ geany（比较好用的编辑器，不geek。不过还是推荐学习vi/vim，否则关键时刻容易尴尬）
`$ sudo apt-get install geany`

@ ubuntu修改开机等待时间
编辑 /etc/default/grub文件

```
GRUB_DEFAULT=0
#GRUB_HIDDEN_TIMEOUT=0
GRUB_HIDDEN_TIMEOUT_QUIET=true
GRUB_TIMEOUT=10
GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian`
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
GRUB_CMDLINE_LINUX=""
```
GRUB_DEFAULT=0，表示启动默认项是第一个系统
GRUB_TIMEOUT=10表示等待时间，改为你想要的数字
更新grub.cfg，执行 # update-grub 即可。

@ deadbeaf（DSD音乐播放器）

@ git（文件版本管理工具）
`$ sudo apt-get install git`

@ gitk（git图形界面工具）
`$ sudo apt-get install gitk`

@ meld（diff比较工具）
`$ sudo apt install meld`

@ rdesktop（linux to windows远程桌面工具）
`$ sudo apt-get install rdesktop`

@ xrdp（windows to linux远程桌面工具）
`$ sudo apt-get install xrdp`

@ npm（你怕吗）
`$ sudo apt install npm`

@ 安装最新的node
```
$ sudo npm install n -g
$ sudo n stable
```

@ 安装hexo（比较好用的git pages工具，npm最好不要用root执行）
```
$ sudo chmod 777 /usr/local/lib/node_modules
$ sudo chmod 777 /usr/local/lib
$ npm install hexo-cli -g
```

@ systemback（系统备份软件，有了它，下次装系统就不用看这篇文章了）
```
$ sudo add-apt-repository ppa:nemh/systemback
$ sudo apt-get update
$ sudo apt-get install systemback
```

