layout: post
title: 如何在Hexo文章中插入图片
tags:
  - Hexo
category:
  - Others
categories:
  - Others
  - Hexo
date: 2016-03-23 17:09:00
---
如何插入的图片?
https://hexo.io/docs/asset-folders.html

1. 编辑_config.yml

```
post_asset_folder: true
```

这样hexo会在每次使用hexo new title命令时在文章源文件目录下创建同名文件夹来存储资源.

2. 在markdown文章里如何引用?

```
{% asset_path slug %}
{% asset_img slug [title] %}
{% asset_link slug [title] %}
```

举个栗子:
本篇文章名叫what-is-LDAP, 创建它时,会创建同名文件夹,把LDAP.png放在那个文件夹下.
引用时写 

```
{% asset_img LDAP.png DLAP organization %}
```

`asset_img` 表示要引用图片, `LDAP.png`是文件名, 后面的是图片显示的标题，可含空格。 `asset_path`会直接将连接显示在页面中。 `asset_link`可以用作内部连接， 可以用于连接文件， 下载使用。

然后执行hexo generate (或者hexo g)
就会将资源拷贝到和生成的文章相同的目录下,这样就可以了.在本地查看会显示不正常,但是部署到github上就显示正常了.