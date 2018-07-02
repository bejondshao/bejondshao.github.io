layout: post
title: >-
  NoClassDefFoundError: Failed to link
  org/jboss/resteasy/plugins/providers/yaml/i18n/LogMessages
tags:
  - Java
  - Wildfly
categories:
  - Java
  - 服务器
date: 2018-06-22 14:48:00
---
I'm using wildfly10.1.0.Final and try to passing byte[] to REST API. It throws error. 

> 10:38:57,445 ERROR [io.undertow.request] (default task-11) UT005023:
> Exception handling request to /app/rest/searchUser:
> java.lang.NoClassDefFoundError: Failed to link
> org/jboss/resteasy/plugins/providers/yaml/i18n/LogMessages (Module
> "org.jboss.resteasy.resteasy-yaml-provider:main" from local module
> loader @2f490758 (finder: local module finder @101df177 (roots:
> /home/bejond/code/server/wildfly-10.1.0.Final/modules,/home/bejond/code/server/wildfly-10.1.0.Final/modules/system/layers/base/.overlays/layer-base-wildfly-10-weld-2.4,/home/bejond/code/server/wildfly-10.1.0.Final/modules/system/layers/base))):
> org/jboss/logging/BasicLogger 	at
> sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
> 	at
> sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:62)
> 	at
> sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
> 	at java.lang.reflect.Constructor.newInstance(Constructor.java:423)
> 	at
> org.jboss.modules.ModuleClassLoader.defineClass(ModuleClassLoader.java:446)
> 	at
> org.jboss.modules.ModuleClassLoader.loadClassLocal(ModuleClassLoader.java:274)
> 	at
> org.jboss.modules.ModuleClassLoader$1.loadClassLocal(ModuleClassLoader.java:78)
> 	at org.jboss.modules.Module.loadModuleClass(Module.java:606) 	at
> org.jboss.modules.ModuleClassLoader.findClass(ModuleClassLoader.java:190)
> 	at
> org.jboss.modules.ConcurrentClassLoader.performLoadClassUnchecked(ConcurrentClassLoader.java:363)
> 	at
> org.jboss.modules.ConcurrentClassLoader.performLoadClass(ConcurrentClassLoader.java:351)
> 	at
> org.jboss.modules.ConcurrentClassLoader.loadClass(ConcurrentClassLoader.java:93)
> 	at
> org.jboss.resteasy.plugins.providers.YamlProvider.readFrom(YamlProvider.java:60)
> 	at
> org.jboss.resteasy.core.interception.AbstractReaderInterceptorContext.readFrom(AbstractReaderInterceptorContext.java:61)
> 	at
> org.jboss.resteasy.core.interception.ServerReaderInterceptorContext.readFrom(ServerReaderInterceptorContext.java:60)
> 	at
> org.jboss.resteasy.core.interception.AbstractReaderInterceptorContext.proceed(AbstractReaderInterceptorContext.java:53)
> 	at
> org.jboss.resteasy.security.doseta.DigitalVerificationInterceptor.aroundReadFrom(DigitalVerificationInterceptor.java:34)
> 	at
> org.jboss.resteasy.core.interception.AbstractReaderInterceptorContext.proceed(AbstractReaderInterceptorContext.java:55)
> 	at
> org.jboss.resteasy.plugins.interceptors.encoding.GZIPDecodingInterceptor.aroundReadFrom(GZIPDecodingInterceptor.java:59)
> 	at
> org.jboss.resteasy.core.interception.AbstractReaderInterceptorContext.proceed(AbstractReaderInterceptorContext.java:55)
> 	at
> org.jboss.resteasy.core.MessageBodyParameterInjector.inject(MessageBodyParameterInjector.java:151)
> 	at
> org.jboss.resteasy.core.MethodInjectorImpl.injectArguments(MethodInjectorImpl.java:91)
> 	at
> org.jboss.resteasy.core.MethodInjectorImpl.invoke(MethodInjectorImpl.java:114)
> 	at
> org.jboss.resteasy.core.ResourceMethodInvoker.invokeOnTarget(ResourceMethodInvoker.java:295)
> 	at
> org.jboss.resteasy.core.ResourceMethodInvoker.invoke(ResourceMethodInvoker.java:249)
> 	at
> org.jboss.resteasy.core.ResourceMethodInvoker.invoke(ResourceMethodInvoker.java:236)
> 	at
> org.jboss.resteasy.core.SynchronousDispatcher.invoke(SynchronousDispatcher.java:402)
> 	at
> org.jboss.resteasy.core.SynchronousDispatcher.invoke(SynchronousDispatcher.java:209)
> 	at
> org.jboss.resteasy.plugins.server.servlet.ServletContainerDispatcher.service(ServletContainerDispatcher.java:221)
> 	at
> org.jboss.resteasy.plugins.server.servlet.HttpServletDispatcher.service(HttpServletDispatcher.java:56)
> 	at
> org.jboss.resteasy.plugins.server.servlet.HttpServletDispatcher.service(HttpServletDispatcher.java:51)
> 	at javax.servlet.http.HttpServlet.service(HttpServlet.java:790) 	at
> io.undertow.servlet.handlers.ServletHandler.handleRequest(ServletHandler.java:85)
> 	at
> io.undertow.servlet.handlers.FilterHandler$FilterChainImpl.doFilter(FilterHandler.java:129)
> 	at
> org.ocpsoft.rewrite.servlet.RewriteFilter.doFilter(RewriteFilter.java:226)
> 	at
> io.undertow.servlet.core.ManagedFilter.doFilter(ManagedFilter.java:61)
> 	at
> io.undertow.servlet.handlers.FilterHandler$FilterChainImpl.doFilter(FilterHandler.java:131)
> 	at
> org.omnifaces.filter.CharacterEncodingFilter.doFilter(CharacterEncodingFilter.java:124)
> 	at org.omnifaces.filter.HttpFilter.doFilter(HttpFilter.java:108) 	at
> io.undertow.servlet.core.ManagedFilter.doFilter(ManagedFilter.java:61)
> 	at
> io.undertow.servlet.handlers.FilterHandler$FilterChainImpl.doFilter(FilterHandler.java:131)
> 	at
> org.picketlink.http.internal.SecurityFilter.processRequest(SecurityFilter.java:356)
> 	at
> org.picketlink.http.internal.SecurityFilter.performOutboundProcessing(SecurityFilter.java:241)
> 	at
> org.picketlink.http.internal.SecurityFilter.doFilter(SecurityFilter.java:196)
> 	at
> io.undertow.servlet.core.ManagedFilter.doFilter(ManagedFilter.java:61)
> 	at
> io.undertow.servlet.handlers.FilterHandler$FilterChainImpl.doFilter(FilterHandler.java:131)
> 	at
> io.undertow.servlet.handlers.FilterHandler.handleRequest(FilterHandler.java:84)
> 	at
> io.undertow.servlet.handlers.security.ServletSecurityRoleHandler.handleRequest(ServletSecurityRoleHandler.java:62)
> 	at
> io.undertow.servlet.handlers.ServletDispatchingHandler.handleRequest(ServletDispatchingHandler.java:36)
> 	at
> org.wildfly.extension.undertow.security.SecurityContextAssociationHandler.handleRequest(SecurityContextAssociationHandler.java:78)
> 	at
> io.undertow.server.handlers.PredicateHandler.handleRequest(PredicateHandler.java:43)
> 	at
> io.undertow.servlet.handlers.security.SSLInformationAssociationHandler.handleRequest(SSLInformationAssociationHandler.java:131)
> 	at
> io.undertow.servlet.handlers.security.ServletAuthenticationCallHandler.handleRequest(ServletAuthenticationCallHandler.java:57)
> 	at
> io.undertow.server.handlers.PredicateHandler.handleRequest(PredicateHandler.java:43)
> 	at
> io.undertow.security.handlers.AbstractConfidentialityHandler.handleRequest(AbstractConfidentialityHandler.java:46)
> 	at
> io.undertow.servlet.handlers.security.ServletConfidentialityConstraintHandler.handleRequest(ServletConfidentialityConstraintHandler.java:64)
> 	at
> io.undertow.security.handlers.AuthenticationMechanismsHandler.handleRequest(AuthenticationMechanismsHandler.java:60)
> 	at
> io.undertow.servlet.handlers.security.CachedAuthenticatedSessionHandler.handleRequest(CachedAuthenticatedSessionHandler.java:77)
> 	at
> io.undertow.security.handlers.NotificationReceiverHandler.handleRequest(NotificationReceiverHandler.java:50)
> 	at
> io.undertow.security.handlers.AbstractSecurityContextAssociationHandler.handleRequest(AbstractSecurityContextAssociationHandler.java:43)
> 	at
> io.undertow.server.handlers.PredicateHandler.handleRequest(PredicateHandler.java:43)
> 	at
> org.wildfly.extension.undertow.security.jacc.JACCContextIdHandler.handleRequest(JACCContextIdHandler.java:61)
> 	at
> io.undertow.server.handlers.PredicateHandler.handleRequest(PredicateHandler.java:43)
> 	at
> io.undertow.server.handlers.PredicateHandler.handleRequest(PredicateHandler.java:43)
> 	at
> io.undertow.servlet.handlers.ServletInitialHandler.handleFirstRequest(ServletInitialHandler.java:292)
> 	at
> io.undertow.servlet.handlers.ServletInitialHandler.access$100(ServletInitialHandler.java:81)
> 	at
> io.undertow.servlet.handlers.ServletInitialHandler$2.call(ServletInitialHandler.java:138)
> 	at
> io.undertow.servlet.handlers.ServletInitialHandler$2.call(ServletInitialHandler.java:135)
> 	at
> io.undertow.servlet.core.ServletRequestContextThreadSetupAction$1.call(ServletRequestContextThreadSetupAction.java:48)
> 	at
> io.undertow.servlet.core.ContextClassLoaderSetupAction$1.call(ContextClassLoaderSetupAction.java:43)
> 	at
> io.undertow.servlet.api.LegacyThreadSetupActionWrapper$1.call(LegacyThreadSetupActionWrapper.java:44)
> 	at
> io.undertow.servlet.api.LegacyThreadSetupActionWrapper$1.call(LegacyThreadSetupActionWrapper.java:44)
> 	at
> io.undertow.servlet.api.LegacyThreadSetupActionWrapper$1.call(LegacyThreadSetupActionWrapper.java:44)
> 	at
> io.undertow.servlet.api.LegacyThreadSetupActionWrapper$1.call(LegacyThreadSetupActionWrapper.java:44)
> 	at
> io.undertow.servlet.api.LegacyThreadSetupActionWrapper$1.call(LegacyThreadSetupActionWrapper.java:44)
> 	at
> io.undertow.servlet.api.LegacyThreadSetupActionWrapper$1.call(LegacyThreadSetupActionWrapper.java:44)
> 	at
> io.undertow.servlet.handlers.ServletInitialHandler.dispatchRequest(ServletInitialHandler.java:272)
> 	at
> io.undertow.servlet.handlers.ServletInitialHandler.access$000(ServletInitialHandler.java:81)
> 	at
> io.undertow.servlet.handlers.ServletInitialHandler$1.handleRequest(ServletInitialHandler.java:104)
> 	at
> io.undertow.server.Connectors.executeRootHandler(Connectors.java:202)
> 	at
> io.undertow.server.HttpServerExchange$1.run(HttpServerExchange.java:805)
> 	at
> java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
> 	at
> java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
> 	at java.lang.Thread.run(Thread.java:748)

This error is not related with parsing or unmarshalling. 
I find that `LogMessage` is located in `wildfly-10.1.0.Final/modules/system/layers/base/org/jboss/resteasy/resteasy-jaxrs/main/resteasy-client-3.0.19.Final.jar/org/jboss/resteasy/client/jaxrs/i18n`. It should be found during wildfly startup.

I look up google and find little about it. And [here](https://issues.jboss.org/browse/JBEAP-7614) seems related with the issue. I stop trying and unzip a wildfly11.0.0.Final and the `NoClassDefFoundError` is gone, shows the real error info, which is what I need.
What I'm saying is that if you met this kind of issue. Stop searching, you can:
1. Upgrade to latest wildfly. 
2. If you can't upgrade. You can try to reach wildfly developer, it probably takes long time to get fixed, cause if you have worked on open source software, you will konw how much work to do before fixing the new issue：joy:.
3. Go deep into wildfly classpath and classloader, write a classloader to load the package you need. This is still not a good solution cause you probably need to add module on wildfly, which is beyond of your application.