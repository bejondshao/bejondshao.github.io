{}
date: 2018-06-15 14:31:34
---
ResteasyClientBuilder是一个支持Client pool，SSL context，异步执行的HttpClient Builder。在使用时遇到了奇怪的事情。我在`test`包下使用
```
new ResteasyClientBuilder().executorService(Executors.newCachedThreadPool());
```
就没问题，但是在`main`包下使用上述代码，就出现：
> Caused by: org.jboss.resteasy.spi.UnhandledException: java.lang.NoSuchMethodError: org.jboss.resteasy.client.jaxrs.ResteasyClientBuilder.executorService(Ljava/util/concurrent/ExecutorService;)Ljavax/ws/rs/client/ClientBuilder;
	at org.jboss.resteasy.core.ExceptionHandler.handleApplicationException(ExceptionHandler.java:78)
	at org.jboss.resteasy.core.ExceptionHandler.handleException(ExceptionHandler.java:222)
	at org.jboss.resteasy.core.SynchronousDispatcher.writeException(SynchronousDispatcher.java:179)
	at org.jboss.resteasy.core.SynchronousDispatcher.invoke(SynchronousDispatcher.java:422)

`NoSuchMethodError`。我怀疑是项目包依赖的问题，去`pom.xml`发现依赖是
```
        <dependency>
            <groupId>org.jboss.resteasy</groupId>
            <artifactId>resteasy-client</artifactId>
            <scope>provided</scope>
        </dependency>
```
之所以用`<scope>provided</scope>`是因为`resteasy-client`是Wildfly自带的组件，所以打包项目时不包含`resteasy-client`，带了反而会出错。

之所以报`NoSuchMethodError`是因为Wildfly自带的`resteasy-client`在`/home/bejond/codewildfly-10.1.0.Final/modules/system/layers/base/org/jboss/resteasy/resteasy-jaxrs/main/resteasy-client-3.0.19.Final.jar`，版本比较旧，是`3.0.19.Final`。
{% asset_img wildfly10-resteasy-jaxrs.png Wildfly10.1.0Final resteasy-jaxrs %}
这个版本里并没有`executorService(ExecutorService executorService)`。实际上是`ResteasyClientBuilder`的父类`ClientBuilder`并没有定义`executorService(ExecutorService executorService)`。

而`test`下能正常测试是因为使用的新版本的`resteasy-client`，我使用的是`resteasy-client-3.5.0.Final`。而自动化测试是不依赖Web服务器的，所以能正常运行。

`pom.xml`依赖没写`<version>`是依赖了父pom。子pom如果要使用别的版本的依赖，加上`<version>`即可：
```
        <dependency>
            <groupId>org.jboss.resteasy</groupId>
            <artifactId>resteasy-client</artifactId>
            <!-- wildfly 10.1.0.Final使用的resteasy-jarx里面的resteasy-client是3.0.19.Final -->
            <version>3.0.19.Final</version>
            <scope>provided</scope>
        </dependency>
```
[Stackoverflow RESTEasy Client + NoSuchMethodError](https://stackoverflow.com/a/24167741/3908814)建议下载最新`resteasy-jaxrx`包，替换`wildfly-10.1.0.Final/modules/system/layers/base/org/jboss/resteasy/resteasy-jaxrs`所有的组件。我并没有这么做，一来不确定是否有兼容性问题（Java向下兼容做得很好，一般不会有这个问题。但就怕遇到，排查起来很麻烦），二来加大实施成本。所以我使用降级的方式来做，其实也不算降级，就是使用Wildfly10.1.0.Final自带的包。

这样调整后进入`ResteasyClientBuilder`就找不到`executorService(ExecutorService executorService)`了。但是打开`ResteasyClientBuilder`的源码，发现提示编译错误。

{% asset_img after-change-version-but-failed-for-dependency.png Compile error %}
点击`ClientBuilder`跳到源码发现版本是2.1
{% asset_img jboss-jarx-api_2.1.png jboss-jarx-api_2.1 %}
2.1版本里的ClientBuilder定义新的抽象方法`executorService(ExecutorService executorService)`，所以提示`未实现抽象方法或者将ResteasyClientBuilder改为抽象类`错误。
是因为Intellij项目library依赖配置问题，到项目`.idea/library`下，把`Maven__org_jboss_spec_javax_ws_rs_jboss_jaxrs_api_2_1_spec_1_0_0_Final.xml`删掉即可。
{% asset_img intellij-libraries.png Intellij libraries %}
然后Intellij会自动根据maven依赖增加`Maven__org_jboss_spec_javax_ws_rs_jboss_jaxrs_api_2_0_spec_1_0_1_Final.xml`文件。

调整好依赖后，`test`里的测试代码就会对`executorService(ExecutorService executorService)`标红了。需要将项目代码和`test`的代码修改为
```
new ResteasyClientBuilder().asyncExecutor(Executors.newCachedThreadPool());
```

需要注意，`asyncExecutor(ExecutorService asyncExecutor)`在新版本的`resteasy-client`包里已经过期，用`executorService(ExecutorService executorService)`代替。从这个例子也能看出程序设计的问题，低版本方法名定义得随意，到了后期调整为新的名字。实际上`executorService`直接调用的`asyncExecutor`，又将`asyncExecutor`标记为`Deprecated`。

补充： 在这个例子里如果不使用`executorService(ExecutorService executorService)`，`ResteasyClient.build()`就会创建10线程固定大小的线程池。那么设定`connectionPoolSize(int size)`就不会起