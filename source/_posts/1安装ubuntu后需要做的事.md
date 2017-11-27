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

@ docky（桌面panel工具）
`$ sudo apt-get install docky`

@ filezilla（ftp客户端）
`$ sudo apt-get install filezilla`

@ unar 解压中文名的windows下打包的zip文件无乱码
`$ sudo apt-get install unar`

@ [Nutstore](https://www.jianguoyun.com/s/downloads/linux)（跨平台网盘）

@ 搜狗输入法（原文连接：http://blog.topspeedsnail.com/archives/6955）
Linux版的搜狗输入法和Fcitx有冲突，在安装前移除fcitx：
```
$ sudo apt remove fcitx*
$ sudo apt purge fcitx*
$ sudo apt autoremove
$ rm -rf .sogouinput/ .config/fcitx .config/fcitx-qimpanel/ .config/sogou-qimpanel/ .config/SogouPY .config/SogouPY.users/
$ reboot
```
[官网](https://pinyin.sogou.com/linux/)下载安装包。
安装输入法
```
$ sudo dpkg -i sogoupinyin*.deb
$ sudo apt -f install
$ reboot
```

@ 谷歌拼音，如果上面搜狗输入法安装总是不起作用，无法输入中文或者输入后选项是乱码，只能使用谷歌输入法了。
$ sudo apt-get install fcitx-googlepinyin
安装后重启fcitx，在Configure里配置谷歌拼音。

@ cmake（C/C++编译打包工具）
`sudo apt install cmake`

@ fcitx-cloudpinyin（fcitx云拼音插件，非必须）
1. 源码编译方式安装
下载源码
`git clone git@github.com:fcitx/fcitx-cloudpinyin.git`
编译
```
cd fcitx-cloudpinyin
mkdir _build
cd _build
cmake ..
```
如果执行cmake .. 报下面错：

> CMake Error at CMakeLists.txt:5 (find_package):
  By not providing "FindFcitx.cmake" in CMAKE_MODULE_PATH this project has
  asked CMake to find a package configuration file provided by "Fcitx", but
  CMake did not find one.

>  Could not find a package configuration file provided by "Fcitx" (requested
  version 4.2.8) with any of the following names:

>    FcitxConfig.cmake
>    fcitx-config.cmake

>  Add the installation prefix of "Fcitx" to CMAKE_PREFIX_PATH or set
  "Fcitx_DIR" to a directory containing one of the above files.  If "Fcitx"
  provides a separate development package or SDK, be sure it has been
  installed.

需要安装fcitx-libs-dev
`sudo apt-get install fcitx-libs-dev`
再执行`cmake ..`遇到
> -- Checking for module 'libcurl'
  --   No package 'libcurl' found

安装libcurl4-gnutls-dev
`sudo apt-get install libcurl4-gnutls-dev`
再执行`cmake ..`成功
安装
```
make
sudo make install
```
2. 命令行安装
`sudo apt-get install fcitx-module-cloudpinyin`(我是安装好之后才找到这个方法的:joy:)
安装成功后可以在add-on找到。
{% asset_img cloudpinyin.png Cloudpinyin %}
双击cloud-pinyin，修改search engine，使用Baidu，尤其是国内地名，百度比谷歌好用。
{% asset_img cloudpinyin-config.png Cloudpinyin Config %}


