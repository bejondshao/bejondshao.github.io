title: what is LDAP
date: 2015-11-19 09:24:33
tags:
---
LDAP是轻量目录访问协议(Lightweight Directory Access Protocol)的缩写，LDAP是从X.500目录访问协议的基础上发展过来的．
特点：
    LDAP的结构用树来表示，而不是用表格。正因为这样，就不能用SQL语句了
    LDAP可以很快地得到查询结果，不过在写方面，就慢得多
    LDAP提供了静态数据的快速查询方式
    Client/server模型，Server 用于存储数据，Client提供操作目录信息树的工具
    这些工具可以将数据库的内容以文本格式（LDAP 数据交换格式，LDIF）呈现在您的面前
    LDAP是一种开放Internet标准，LDAP协议是跨平台的Interent协议

{% asset_img LDAP.png LDAP organization %}


http://stackoverflow.com/questions/18756688/what-are-cn-ou-dc-in-an-ldap-search
CN = Common Name
OU = Organizational Unit
DC = Domain Component
You read it from right to left, the right-most component is the root of the tree, and the left most component is the node (or leaf) you want to reach.
https://en.wikipedia.org/wiki/LDAP_Data_Interchange_Format
dn
distinguished name
This refers to the name that uniquely identifies an entry in the directory.
dc
domain component
This refers to each component of the domain. For example www.google.com would be written as DC=www,DC=google,DC=com
ou
organizational unit
This refers to the organizational unit (or sometimes the user group) that the user is part of. If the user is part of more than one group, you may specify as such, e.g., OU= Lawyer,OU= Judge.
cn
common name 
This refers to the individual object (person's name; meeting room; recipe name; job title; etc.) for whom/which you are querying.
 dn: cn=The Postmaster,dc=example,dc=com
 objectClass: organizationalRole
 cn: The Postmaster


=====================================

如何插入的图片?
https://hexo.io/docs/asset-folders.html

1. 编辑_config.yml

post_asset_folder: true

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
asset_img 表示要引用图片, LDAP.png是标题, 后面的是图片显示的标题.

然后执行hexo generate (或者hexo g)
就会将资源拷贝到和生成的文章相同的目录下,这样就可以了.在本地查看会显示不正常,但是部署到github上就显示正常了.