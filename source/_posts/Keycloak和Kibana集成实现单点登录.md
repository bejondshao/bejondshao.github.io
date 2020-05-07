layout: draft
title: Keycloak和Kibana集成实现单点登录
author: Bejond Shao
categories:
  - DevOps
  - Keycloak
tags:
  - Keycloak
  - Kibana
  - Elasticsearch
  - Keycloak proxy
date: 2018-04-09 16:49:00
---
##### [Elasticsearch](https://www.elastic.co/products/elasticsearch)
Elasticsearch是一个分布式， 支持RESTful接口查询和分析的引擎， 用于解决不断增长的数据和用例。 作为Elastic Stack的核心存储数据。 Es提供多种查询方式， 聚合细化查询粒度。 可以自动检查结点健壮性。 Es可以做到应用查询， 安全分析， 记录日志， 监测， 警报， 生成报告等。 另外Es还可以和Hadoop结合使用。

##### 安装Elasticsearch
* 到[Elasticsearch下载页](https://www.elastic.co/downloads/elasticsearch)下载zip包，当前版本6.2.3。 下载zip包好处是解压到本地， 配置文件可以放在用户目录， 启动方便， 重做系统也不会丢失配置。 当然如果是服务器部署， 应该下载对应的安装包， 方便安装服务。
* 进入`elasticsearch/bin`目录， 命令行启动elasticsearch。
* 待启动完成， 访问http://localhost:9200 。 看到如下所示基本信息表明启动成功。
{% asset_img Elasticsearch es.png %}

##### [Kibana](https://www.elastic.co/products/kibana)
Kibana将Es的数据以友好的可视化方式展示， 比如脂肪图， 曲线图， 饼图， 地理图。 Kibana聚合了Es的功能， 并具备基于时间的分析功能。

##### 安装
* 到[Kibana下载页](https://www.elastic.co/downloads/kibana)下载zip包， 当前版本6.2.3。
* 进入`kibana/bin`目录， 启动kibana。
* 待启动完成，访问http://localhost:5601即可。

#### Keycloak保护Kibana
##### 工作流程
{% asset_img Workflow keycloak-kibana.jpg %}
1. 外部请求被[keycloak proxy](https://www.keycloak.org/docs/latest/server_installation/index.html#_proxy)拦截。 keycloak proxy根据它的配置决定请求是否需要验证。
2. 如果需要验证， keycloak proxy将请求重定向到keycloak的登录页面。
3. 成功登录后， 将请求发送到kibana。

##### Keycloak配置client
* 在Keycloak后台创建client， 名为kibana。 Root URL填keycloak proxy监听的地址， 如http://localhost:8081
{% asset_img Client client.png %}
* 开启Authorization Enabled， 保存， 用于验证客户端。
{% asset_img Authorization Enabled client2.png %}

##### 安装keycloak proxy
1. [下载keycloak proxy 3.4.3](https://downloads.jboss.org/keycloak/3.4.3.Final/keycloak-proxy-3.4.3.Final.zip)到本地， 解压。
2. 在`keycloak-proxy-3.4.3.Final/bin`目录下创建proxy.json文件
```
{
    "target-url": "http://localhost:5601",
    "bind-address": "0.0.0.0",
    "http-port": "8081",
    "applications": [
        {
            "base-path": "/",
            "error-page": "/error.html",
            "adapter-config": {
                "realm": "abc",
                "resource": "kibana",
                "auth-server-url": "http://localhost:8080/auth",
                "ssl-required" : "external",
                "principal-attribute": "name",
                "credentials": {
                    "secret": "7aa8d667-2ae1-4ee9-b9b8-eb94b317b649"
                }
            }
            ,
            "constraints": [
                {
                    "pattern": "/*",
                    "roles-allowed": [
                        "user"
                    ]
                }
            ]
        }
    ]
}
```
* target-url： kibana后台的地址
* bind-address： 绑定keycloak proxy的地址， 默认localhost
* http-port: keycloak proxy监听的端口
* base-path: 要保护的应用的路径，必须以"/"开头
* resource: keycloak创建的client id
* auth-server-url: keycloak验证地址
* credentials: 客户端验证的方式， 如果是secret， secret的密钥从`keycloak cline -> Credentials`获得， 见下图
{% asset_img Credentials client-secret.png %}
* constrainsts： 过滤条件
* pattern: 过滤不同路径下的内容
* roles-allowed: 具有roles-allowed里的role的用户才有权限访问pattern下的资源

配置好proxy.json后， 在`bin`下执行
`$ java -jar launcher.jar`

##### 验证登录
* 访问http://localhost:8081, keycloak proxy检测到当前用户未登录， 会跳转到keycloak登录界面。
{% asset_img Login login.png %}
* 输入用户名密码， 验证成功后即调回keycloak proxy。 keycloak proxy负责转发到kibana。
{% asset_img To Kibana tokibana.png %}
可以看到浏览器地址为localhost:8081， 而不是localhost:5601。

##### 遗留问题
1. Kibana没有验证功能， 因此也没有登出功能。 只能在Keycloak中`Users -> Someuser -> Sessions`点击Logout all sessions尝试登出。 但是点击后在keycloak proxy console则报`UT005071: Undertow request failed HttpServerExchange`异常， 该问题在[Keycloak-3311](https://issues.jboss.org/browse/KEYCLOAK-3311)已经提及， 但是至今未修复。
2. Keycloak proxy暂时只能起到定向到keycloak登录的功能， 对于鉴权还做得不好， 任何一个在Realm里的用户， 即便没有`roles_allowed`里的role， 也能访问登录后的kibana界面， 详见[Keycloak-7120](https://issues.jboss.org/browse/KEYCLOAK-7120)