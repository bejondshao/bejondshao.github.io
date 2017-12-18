---
layout: post
title: Jboss6.x升级到Wildfly10所遇到的问题
date: 2017-12-18 17:46:59
tags:
- Wildfly

category:
- Java

---
1). Hibernate second level cache问题

> 17:02:43,041 ERROR [org.jboss.msc.service.fail] (ServerService Thread Pool -- 61) MSC000001: Failed to start service jboss.persistenceunit.kunlun-weixin-showcase#primary.__FIRST_PHASE__: org.jboss.msc.service.StartException in service jboss.persistenceunit.kunlun-weixin-showcase#primary.__FIRST_PHASE__: org.hibernate.service.spi.ServiceException: Unable to create requested service [org.hibernate.cache.spi.RegionFactory]
	at org.jboss.as.jpa.service.PhaseOnePersistenceUnitServiceImpl$1$1.run(PhaseOnePersistenceUnitServiceImpl.java:120)
	at org.jboss.as.jpa.service.PhaseOnePersistenceUnitServiceImpl$1$1.run(PhaseOnePersistenceUnitServiceImpl.java:102)
	at org.wildfly.security.manager.WildFlySecurityManager.doChecked(WildFlySecurityManager.java:667)
	at org.jboss.as.jpa.service.PhaseOnePersistenceUnitServiceImpl$1.run(PhaseOnePersistenceUnitServiceImpl.java:129)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
	at java.lang.Thread.run(Thread.java:745)
	at org.jboss.threads.JBossThread.run(JBossThread.java:320)
Caused by: org.hibernate.service.spi.ServiceException: Unable to create requested service [org.hibernate.cache.spi.RegionFactory]
	at org.hibernate.service.internal.AbstractServiceRegistryImpl.createService(AbstractServiceRegistryImpl.java:264)
	at org.hibernate.service.internal.AbstractServiceRegistryImpl.initializeService(AbstractServiceRegistryImpl.java:228)
	at org.hibernate.service.internal.AbstractServiceRegistryImpl.getService(AbstractServiceRegistryImpl.java:207)
	at org.hibernate.boot.internal.MetadataBuilderImpl$MetadataBuildingOptionsImpl.<init>(MetadataBuilderImpl.java:663)
	at org.hibernate.boot.internal.MetadataBuilderImpl.<init>(MetadataBuilderImpl.java:127)
	at org.hibernate.boot.MetadataSources.getMetadataBuilder(MetadataSources.java:135)
	at org.hibernate.jpa.boot.internal.EntityManagerFactoryBuilderImpl.<init>(EntityManagerFactoryBuilderImpl.java:185)
	at org.hibernate.jpa.boot.internal.EntityManagerFactoryBuilderImpl.<init>(EntityManagerFactoryBuilderImpl.java:149)
	at org.hibernate.jpa.boot.spi.Bootstrap.getEntityManagerFactoryBuilder(Bootstrap.java:28)
	at org.hibernate.jpa.boot.spi.Bootstrap.getEntityManagerFactoryBuilder(Bootstrap.java:40)
	at org.jboss.as.jpa.hibernate5.TwoPhaseBootstrapImpl.<init>(TwoPhaseBootstrapImpl.java:39)
	at org.jboss.as.jpa.hibernate5.HibernatePersistenceProviderAdaptor.getBootstrap(HibernatePersistenceProviderAdaptor.java:159)
	at org.jboss.as.jpa.service.PhaseOnePersistenceUnitServiceImpl.createContainerEntityManagerFactoryBuilder(PhaseOnePersistenceUnitServiceImpl.java:242)
	at org.jboss.as.jpa.service.PhaseOnePersistenceUnitServiceImpl.access$800(PhaseOnePersistenceUnitServiceImpl.java:59)
	at org.jboss.as.jpa.service.PhaseOnePersistenceUnitServiceImpl$1$1.run(PhaseOnePersistenceUnitServiceImpl.java:117)
	... 7 more
Caused by: org.hibernate.boot.registry.selector.spi.StrategySelectionException: Unable to resolve name [org.jboss.as.jpa.hibernate4.infinispan.InfinispanRegionFactory] as strategy [org.hibernate.cache.spi.RegionFactory]
	at org.hibernate.boot.registry.selector.internal.StrategySelectorImpl.selectStrategyImplementor(StrategySelectorImpl.java:113)
	at org.hibernate.cache.internal.RegionFactoryInitiator.resolveRegionFactory(RegionFactoryInitiator.java:99)
	at org.hibernate.cache.internal.RegionFactoryInitiator.initiateService(RegionFactoryInitiator.java:67)
	at org.hibernate.cache.internal.RegionFactoryInitiator.initiateService(RegionFactoryInitiator.java:28)
	at org.hibernate.boot.registry.internal.StandardServiceRegistryImpl.initiateService(StandardServiceRegistryImpl.java:88)
	at org.hibernate.service.internal.AbstractServiceRegistryImpl.createService(AbstractServiceRegistryImpl.java:254)
	... 21 more

解决方法: https://developer.jboss.org/thread/266936
Solved by moving second level cache from hibernate4  to **hibernate5** in persistence.xml:
```
<property name="hibernate.cache.region.factory_class" value="org.jboss.as.jpa.hibernate5.infinispan.InfinispanRegionFactory" />
```

2). PSQLException: ERROR: column \"user_id\" contains null values"
由于hibernate4对表关联创建的列为users_id和roles_id，升级为hibernate5后，hibernate尝试将列名改为user_id，roles_id，而在进行`alter table user_roles add column User_id int8 not null`操作时，遇到非空检查失败了。解决方法是将原表备份，然后删除user_roles表，启动项目后会自动创建新的表和表结构。然后再自行更新表。

3). Hibernate infinispan statistics cache问题
> [2017-12-15 05:14:40,464] Artifact kunlun-weixin-showcase:war exploded: Error during artifact deployment. See server log for details.
[2017-12-15 05:14:40,468] Artifact kunlun-weixin-showcase:war exploded: java.lang.Exception: {"WFLYCTL0080: Failed services" => {"jboss.persistenceunit.kunlun-weixin-showcase#primary" => "org.jboss.msc.service.StartException in service jboss.persistenceunit.kunlun-weixin-showcase#primary: javax.persistence.PersistenceException: [PersistenceUnit: primary] Unable to build Hibernate SessionFactory
    Caused by: javax.persistence.PersistenceException: [PersistenceUnit: primary] Unable to build Hibernate SessionFactory
    Caused by: org.hibernate.service.spi.ServiceException: Unable to create requested service [org.hibernate.engine.spi.CacheImplementor]
    Caused by: org.hibernate.cache.CacheException: Unable to start region factory
    Caused by: org.infinispan.commons.CacheConfigurationException: ISPN000372: Statistics are enabled while attribute 'available' is set to false."},"WFLYCTL0412: Required services that are not installed:" => ["jboss.persistenceunit.kunlun-weixin-showcase#primary"],"WFLYCTL0180: Services with missing/unavailable dependencies" => undefined}
17:17:43,075 INFO  [org.jboss.as.server] (management-handler-thread - 5) WFLYSRV0236: Suspending server with no timeout.
[2017-12-15 05:23:17,962] Artifact kunlun-weixin-showcase:war exploded: Error during artifact deployment. See server log for details.
[2017-12-15 05:23:17,962] Artifact kunlun-weixin-showcase:war exploded: java.lang.Exception: {"WFLYCTL0080: Failed services" => {"jboss.persistenceunit.kunlun-weixin-showcase#primary" => "org.jboss.msc.service.StartException in service jboss.persistenceunit.kunlun-weixin-showcase#primary: javax.persistence.PersistenceException: [PersistenceUnit: primary] Unable to build Hibernate SessionFactory
    Caused by: javax.persistence.PersistenceException: [PersistenceUnit: primary] Unable to build Hibernate SessionFactory
    Caused by: org.hibernate.service.spi.ServiceException: Unable to create requested service [org.hibernate.engine.spi.CacheImplementor]
    Caused by: org.hibernate.cache.CacheException: Unable to start region factory
    Caused by: org.infinispan.commons.CacheConfigurationException: ISPN000372: Statistics are enabled while attribute 'available' is set to false."},"WFLYCTL0412: Required services that are not installed:" => ["jboss.persistenceunit.kunlun-weixin-showcase#primary"],"WFLYCTL0180: Services with missing/unavailable dependencies" => undefined}

Solution: https://stackoverflow.com/questions/43586016/infinispan-statistics-are-enabled-while-attribute-available-is-set-to-false

Workaround: 將persistence.xml hibernate.cache.infinispan.statistics=true 改为false
Note: 有可能在解决第2个问题后这个问题就不存在了。



