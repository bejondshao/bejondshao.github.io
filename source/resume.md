邵帅 • 男 • 1990-10 • 辽宁 大连 • <a@bejond.org> • +86 18640902787

------
六年Java后台研发经验，曾就职于Liferay，服务于开源框架[Liferay（CMS）]((https://github.com/liferay/liferay-portal))的维护和支持，服务于亚太，欧美客户。

现从事酒店智能化管理，主要负责在线入住，在线选房，在线离店等一整套智能化酒店业务流程，提供移动PMS解决方案并实现。带领两个人做产品研发和维护。
对安全，身份认证、授权颇有研究。对[Keycloak](https://www.keycloak.org/)有过系统的学习。利用业余时间做单点登录，LDAP集成。使用Spring Security做OpenId Connect身份认证与授权。

对消息中间件远程调用有完整的产品设计和实现经验。熟悉主流Java框架。对JVM调优，内存泄露有研究和调优经验。

拥抱技术，对技术有钻研的兴趣。能很快的熟悉新的思想和技术方案并使用。能系统的学习技术，并善于做记录和总结，喜欢用自己所学帮助他人。

------

教育经历
----

2009-2013 : 大连理工大学，软件工程，本科。

工作经历
----

**[北京石基昆仑软件有限公司](http://www.shijinet.cn)（2016/05-至今）**
> 工作内容为智能化酒店服务管理。主要负责酒店自助入住相关业务，负责Kiosk自助机产品研发，云端GJE在线入住产品研发，移动端PMS的服务开发。包括创建订单，在线选房分房，预授权，付款，离店等功能。

* 入职两个月提前转正，并承担Kiosk自助机产品和GJE产品开发。后由于人员变动，成为项目负责人。
* 负责新技术调研，如SpringBoot, Camel, Keycloak。技术攻关，比如消息中间件RPC模式应用，新PMS接口对接，产品设计和代码规范。
* 优化代码结构，性能调优，代码审核。
* 负责Java开发人员招聘和面试。

**[Liferay](https://www.liferay.com)（2014/06-2016/04）**
> 工作内容为开源项目Liferay维护。为美国，日本，印度，澳大利亚，欧洲客户提供技术支持。

* 与客户支持人员沟通（英语），为客户分析Liferay的bug，其中包括搭建环境，分析客户数据，分析问题日志，重现问题，调试并解决问题，制作补丁，向低版本移植修复，解决不同修复之间代码冲突。问题涉及前台，后台API，系统业务模块，权限管理，Liferay升级兼容等问题。
* Thread Dump，Heap Dump分析，JVM调优。

**北京紫光华宇软件有限公司（2012/08-2012/12，实习）**
> 在北京高级人民法院驻地一个月,参与太极-紫光数据库统一整合工作,参与地方法院数据向最高人民法院汇报工作。

项目经历
----

产品名称：**[Kiosk自助机服务系统](http://www.shijinet.cn/Check%20in.html)**

产品简介：Kiosk自助机服务系统是部署在酒店，通过与酒店PMS进行交互完成客人自助查询分配房间，刷预授权，人脸识别，上传证件到公安局，办理入住以及离店的多功能智能化系统。Kiosk提供两套接口，一套接口（SOAP协议）供酒店自助机使用。解决客人排队，自主选房的问题，同时减轻酒店前台压力。另一套接口（REST API）供GJE使用，完成在线选房，入住，离店等功能。

角色及职能：产品负责人，对客户需求分析，设计，接口定义，并做技术难点实现。实现进度把控，产品质量把关。

相关技术：Java EE, EJB, CDI, AOP, Mybatis, JPA, JWT，RabbitMQ, Postgresql, Oracle, Maven, SVN。

----

产品名称：**Guest Journey Engine（GJE）**

产品简介：GJE是供第三方互联网厂商如飞猪，携程等使用的在线办理酒店业务的接口。服务于国内五星级酒店如香格里拉，红树林，万豪等。对接PMS有Opera（国内四星级五星级酒店行业通用酒店管理系统），Cambridge（石基信息自主研发云PMS）。主要功能包括创建订单，在线选房分房，刷预授权，在线入住，抛账，自助离店等。其中GJE调用酒店内网Kiosk接口遇到网络限制，通过应用RabbitMQ的RPC模式，实现了跨域调用。

角色及职能：产品负责人，对客户需求分析，设计，接口定义，并做技术难点实现。实现进度把控，产品质量把关。编写自动化并发测试代码。

相关技术：Java EE, EJB, CDI, AOP, Mybatis, JPA, JWT，Postgresql, RabbitMQ。

----

产品名称：**Kunlun 360**

产品介绍：Kunlun 360是石基昆仑自主研发的，全方位服务酒店业务的综合性系统。包含订单处理，客房管理，客人管理，账目管理，证件上传公安局，扫码支付，电子签名，发票打印等多个模块。实现移动PMS功能，方便服务人员专项服务客人，不必局限于前台或自助设备，让客人获得更优质的体验。

角色及职能：本人负责订单，客房，客人，账目管理等模块。负责需求分析，功能调研，接口设计，功能实现，协调客户端测试。

相关技术：SpringBoot, Mybatis, JPA, Postgresql。

----

产品名称：**[Liferay Portal](https://github.com/liferay/liferay-portal)**

产品简介：Liferay Portal是一个开源企业级的内容管理系统，为多家企业提供商业支持。其基于portlet的开发模式使其便于用户动态管理页面布局和内容。可插拔的多功能插件灵活完成预期功能。技术体系是基于SSH的多功能的portlet容器。官方标准版本的功能包括内容管理，日历，用户导入（LDAP，SAML，AD等），动态可配置的角色和权限的管理，内容搜索（基于Elasticsearch）等。

角色及职能：产品维护，负责系统与LDAP对接模块。维护liferay(开源)和liferay-ee(private)系统，提交修复。

相关技术: Liferay, portlet, Struts, Spring, Hibernate, JSP, LDAP, Bootstrap, js, css, sql, freemarker等

----

个人项目：**[Grape](https://github.com/bejondshao/grape)**

项目简介：使用python获取A股股票基本信息，每日交易信息，计算K线等其他技术指标，通过一定的选股策略进行筛选。本人在股票投资过程中，迫于股票数量太多，无法观测所有K线，所以想到通过读取所有股票历史日交易信息，根据信息计算60日均价，再根据邻近交易日均价计算K线斜率，推算股票价格趋势。

相关技术：Python, MongoDB, Tushare, pandas。

个人技能
----
* 2011年，获英语CET-6证书，听说读写流利。
* 2012年，获金融英语证书。
* 2012年，获IBM证书（SOA与服务计算）。
* Linux服务器(CentOS)维护经验。六年Linux使用经验。熟悉Linux常用命令。* 熟练使用git以及Intellij/eclipse调试，快速定位问题。使用Junit自动化测试。

兴趣爱好
----
* [技术博客](http://tech.bejond.org)
* [Stackoverflow](https://stackoverflow.com/users/3908814/bejond?tab=profile)
* [Github](https://github.com/bejondshao)

