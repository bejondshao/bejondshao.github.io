---
layout: post
title: Hueman主题升级
date: 2017-11-13 20:38:22
tags:
- Hexo
- Hueman
category:
- Others
---
{% asset_img theme.png Hueman Theme %}
###### 下载hueman源码，将hueman源码包复制到themes文件夹下

###### 移植_config.yml

由于我使用的hueman主题版本比较旧，之前hueman配置文件是和hexo的_config.yml是一个文件。升级后在themes/hueman/下有新的_config.yml.example，把这个文件重命名为_config.yml，根据自己需要更改。

###### 增加不蒜子统计

- 增加站点统计

编辑themes/hueman/layout/common/footer.ejs，将
```
<p>Powered by <a href="//hexo.io/" target="_blank">Hexo</a>. Theme by <a href="//github.com/ppoffice" target="_blank">PPOffice</a></p>
```
更改为
```
<p>Powered by <a href="//hexo.io/" target="_blank">Hexo</a>. Theme by <a href="//github.com/ppoffice" target="_blank">PPOffice</a>
        <span id="busuanzi_container_site_pv">
    		本站总访问量<span id="busuanzi_value_site_pv"></span>次
		</span>
        <span id="busuanzi_container_site_uv">
		    你是本站第<span id="busuanzi_value_site_uv"></span>个访客
		</span>
</p>
```
- 增加文章统计

编辑themes/hueman/layout/common/article.ejs，将
```
<div class="article-entry" itemprop="articleBody">
	<%- post.content %>
</div>
```
更改为
```
<div class="article-entry" itemprop="articleBody">
	<%- post.content %>
</div>
<div class="article-entry" id="busuanzi_container_page_pv">
	本文总阅读<span id="busuanzi_value_page_pv"></span>次
</div>
```
