layout: post
title: Hexo使用经验
tags:
  - Hexo
category:
  - Others
categories:
  - Others
  - Hexo
date: 2017-10-27 19:38:00
---
本文仅做使用Hexo时遇到的问题汇总，如果对你有帮助，那就有吧:sweat_smile:。

##### Hexo官方安装方式
`$ npm install hexo-cli -g`
注意是是普通用户，如果安装时遇到权限问题，请想尽办法解决。不要用root安装，会出各种奇怪的问题。
* 安装后，写好文章，在执行 hexo deploy 后,出现 error deployer not found:git 的错误。
`$ npm install hexo-deployer-git --save`
* 如果hexo deploy后，输入网站域名，显示404了，但是通过url访问页面还能访问到。
```
$ hexo clean
$ hexo g
$ hexo d
```
再访问试试
* 在不同系统下，比如OS X和Linux，package-lock.json是不一样的。我建议把package-lock.json加入.gitignore中。
在package-lock.json所在目录执行一下命令：
```
$ cp package-lock.json ~/package-lock.json
$ git checkout package-lock.json
$ git reset package-lock.json
$ git rm --cached package-lock.json
```
编辑 .gitignore，在末尾填入package-lock.json
```
$ cp ~/package-lock.json .
$ rm -f ~/package-lock.json
$ git add .
$ git commit -m "source the text you want to add"
$ git push origin -f source
```
* 不同系统下，比如OS X和Linux，package.json不同，一定要保证两个环境都是用相同版本的Hexo和插件。建议都升到最新版，以后定时统一升级。

##### 增加Emoji支持

使用[hexo-renderer-markdown-it-plus](https://github.com/CHENXCHEN/hexo-renderer-markdown-it-plus)
```
npm un hexo-renderer-marked --save
npm i hexo-renderer-markdown-it-plus --save
```
编辑_config.yml，添加
```
markdown_it_plus:
    highlight: true
    html: true
    xhtmlOut: true
    breaks: true
    langPrefix:
    linkify: true
    typographer:
    quotes: “”‘’
    pre_class: highlight
```
然后到[这里](http://emoji.muan.co/)或[这里](https://www.webpagefx.com/tools/emoji-cheat-sheet/) 查找emoji code，粘贴到文章即可。
我一开始使用**hexo-renderer-markdown-it**插件会有两个问题，一个问题是段落之间如果不加空行，会合并成一段，也就是整篇文章都成了一段，导致以前的文章都需要作调整。二个问题是无法使用粗体标志，相信其他标志也无法完美使用，需要在`**`前加空格，很是麻烦，后来使用hexo-renderer-markdown-it-plus就没有这些问题。

##### Hexo使用站内链接
```
{% post_link hello-world Hello World %}
```
hello-world是你的文章名称。如果文章不存在，这段代码将会被直接忽略。
Hello World是要展示的文字，如果不填，默认取引用文章标题。
示例：{% post_link hello-world Hello World %}

##### Hexo文章插入图片
详见{% post_link 如何在Hexo文章中插入图片 %}

##### 主动推送Hexo博客链接到百度搜索
详见[hexo-baidu-url-submit](https://www.npmjs.com/package/hexo-baidu-url-submit)

##### [hexo-admin](https://github.com/jaredly/hexo-admin)插件，可以本地可视化编辑文章。
  
  * hexo-admin可配置文章自定义参数，在博客_config.yml里添加
  ```
  metadata:
    categories: 杂记
    comments: true
  ```
即可，需要重启hexo生效。
{% asset_img hexo-admin_metadata.png Hexo-admin metadata %}
`categories`配置创建文章的默认类别，该属性是数组，如果想配置文章到默认的二级类别则为`categories: [杂记,生活]`。`comments`为是否开启文章评论功能，添加后即可在hexo-admin后台设定属性（comments即便不设定，如果博客开启评论功能，也是所有文章默认都开启的，这样配置则方便在hexo-admin设置某篇文章不开启comment，就不需要手动修改文章markdown文件了）。这些默认值需要通过hexo-admin方式创建文章才会设定默认值。如果是通过`hexo new abc`来创建文章，则打开hexo-admin文章这些配置默认值不生效，需要手动设定。

* Metadata功能，可以在_config.yml里添加诸如默认category的功能（因为hexo的默认category功能好像不好用）
```
metadata:
  categories: Others
```
* 发现原来一个文章可以添加多个categories，比如：

```
categories:
- Others
- SubCategory
```

其中SubCategory就是Others菜单的二级菜单。

##### hexo升级
```
$ npm update hexo
$ npm install -g --save hexo
```
会安装hexo的依赖，如果有warn提示其他包过期，需要使用`npm update xxx`方法更新包，不一定需要安装。另外需要注意package.json中使用的插件是否与当前hexo版本兼容。我一般不升级，因为升级都往往有些意料不到的问题，尤其用开源工具以及其他开源插件。不过还是要感谢做这些工具的开发者，他们是技术发展的推动者和贡献者。

##### hexo主题升级
参考{% post_link 如何将hexo与theme代码分开提交 如何将hexo与theme代码分开提交 %}。

##### 一键提交更新网站脚本
```
#!/bin/bash
# bejondshao.github.io
HOME=/root
LOGNAME=root
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
LANG=en_US.UTF-8
SHELL=/bin/bash
PWD=/root
cd /absolute_path/to/bejondshao.github.io
git status
git add .
git commit -m "source post"
git pull --rebase origin source
git push origin source
hexo g && hexo d
```
存为abc.sh将文件赋予执行权限

`$ sudo chmod +x abc.sh`

##### 每日定时执行脚本

`$ sudo crontab -e`

然后编辑，设定
```
0 3 * * * /absolute_path/to/script
```
每天早晨三点执行上面更新脚本。参考[博客](https://blog.csdn.net/xingyue0422/article/details/83012000)和[博客](https://blog.csdn.net/iteye_4528/article/details/82033239)。为了防止某次提交出现网络问题，可以设置每隔10分钟执行。本地测试举例：

```
*/10 * * * * /bin/bash /Users/alpha/abc/abc.sh 2>LOG_FILE > /Users/alpha/abc/abc.txt
```

`/bin/bash`是使用bash执行脚本。crontab默认使用`/bin/sh`执行脚本。如果使用默认的shell，可以这样配置：
```
*/10 * * * * /Users/alpha/abc/abc.sh 2>LOG_FILE > /Users/alpha/abc/abc.txt
```

`2>LOG_FILE > /Users/alpha/abc/abc.txt`是记录脚本执行输出内容，方便查看脚本是否执行，执行的情况。

**注意:**  
  * 脚本路径需要是绝对路径，脚本内的路径可以设定PATH，但是如无必要，建议使用绝对路径。
  * 脚本里的`HOME, LOGNAME, PATH, LANG, SHELL, PWD`需要设定。不设定的话，后面的`hexo`相关的命令不会执行，即只能将源码推到资源库，但是没法更新网站。
  * 执行计划任务时电脑不可以是sleep或关机。
  * 脚本可能会有时不时无法将源码推到资源库的情况，有可能是执行`git pull`时出错，检查网络或者`.git/config`。也可能是执行`git push`出错，检查当前git用户是否有权限向资源库推送，必要时在github资源库上添加Collaborator。

##### Hexo server可以启动多个服务

指定另外一个端口即可，使用命令：

`hexo s -p 4001`

##### hexo-admin插件可以设置登录账号密码

在Settings里，输入值后可以贴到_config.yml里，每次访问就需要登录。在家用不着，如果你想把hexo server部署到服务器，可以发布出去，在任意浏览器访问后台发布内容，这个功能就很有必要。

* 还可以使用路由器申请DDNS，申请一个二级域名，如果有个人域名可以指向该域名。然后再开启虚拟服务器，就可以访问域名加端口，访问本机的应用，当然也可以访问到hexo-admin。省去租服务器的钱了。但是要注意，偶尔用用可以，如果做涉及到广告商业或者其他用途，被宽带商监测到会发函警告的，我见网上有人被发函了，理由就是私自架设Web服务。

##### Comment

评论功能我以前没开启，后来想开启，大概看了有几种方式: 
 1. 第三方评论服务（Disqus，有言，多说等，优点是可注册授权，缺点是服务不稳定。）
 2. Staticman（以pull request的方式在github的资源库创建issues，好处是不需要注册登录，可手动审核评论，坏处是没法注册登录。本想使用这种方式，但是配置时官方服务失效，作罢。）
 3. git issues（有gitment, ghpages-ghcomments等，在github的资源库创建issues。其他人评论则以comment的形式出现，没有验证博主是否有权利删除他人评论。需要注册登录github。）
 4. [Valine](https://valine.js.org/)（使用[LeanCloud](https://leancloud.cn/)作为数据库存储。好处是不需要注册，绝大多数Hexo主题都已支持，可以直接启动配置项即可。另外建议结合[Valine-Admin](https://github.com/DesertsP/Valine-Admin)一起使用，可视化管理评论。）
 * 禁用某个文章评论功能，在文章头部添加`comments: false`即可。
 