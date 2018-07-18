layout: post
title: Use Maven Profile to Change Configuring during Packaging
tags:
  - maven
categories:
  - Tools
date: 2018-07-18 18:06:00
---
{% asset_img Profiles profiles.jpg %}
We are going to deploy our application to wildfly to cloud server, while we need to set test environment and production environment. It's complecated to set up double server with different ports, because it means doing the same deployment things twice. One simple way is to deploy the same app in the same wildfly, but with different app names. To achieve this, you can simply rename `app.war` to `newname.war`. So the context becomes to `newname`. You can visit web application through `http://localhost:8080/newname`. You need to do this each time you do deploy. There's another elegant way to achieve this. Add `jboss-web.xml` to `WEB-INF` and package it to the war. `jboss-web.xml` looks like this:
```
<?xml version="1.0" encoding="UTF-8"?>
<jboss-web xmlns="http://www.jboss.com/xml/ns/javaee"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="
      http://www.jboss.com/xml/ns/javaee
      http://www.jboss.org/j2ee/schema/jboss-web_5_1.xsd">
    <context-root>appname</context-root>
</jboss-web>

```
And if the `war` contains resource connecting like database, file system. You probably need to connect to different databases, for databases, we usually put data source in `META-INF/persistence.xml`, like this:
```
<jta-data-source>java:jboss/datasources/DS</jta-data-source>
```
I can't change it each time do deploy. It'll be good if the `context-root` and `jta-data-source` can be changed during packaging. [Maven profile](https://maven.apache.org/guides/introduction/introduction-to-profiles.html ) works like a charm.
> I have to attach document because it explains more details and more complemental. When we looking for solutions we usually google it instead of reading docs, because if we find the right place, it will solve our issue directly, but we might be blind for why and how or some other usages. Reading docs might take much more time and probably you lost in it. It depends on situations. It's up to you.

Back to maven profile, it's like "pom.xml settings", during packing, you can choose which profile to use. And packaging the application for you. In this case, we use profile to set property with different values. Remember this, profile can do more than set property values, it can use different jdks to compile, copy different resource files, package for different hosts, etc. I make example for data source. First, create a property in `<properties>` element.
```
<properties>
    <base-jta-data-source>java:jboss/datasources/DS</base-jta-data-source>
</properties>

```
Then create profiles
```
<profiles>
    <profile>
        <id>default</id>
        <activation>
            <activeByDefault>true</activeByDefault>
        </activation>
        <properties>
            <jta-data-source>${base-jta-data-source}</jta-data-source>
            <final-artifactId>${project.artifactId}</final-artifactId>
        </properties>
        <build>
            <plugins>
                <plugin>
                    <artifactId>maven-surefire-plugin</artifactId>
                    <configuration>
                        <skip>false</skip>
                        <argLine>-Dfile.encoding=UTF-8</argLine>
                    </configuration>
                </plugin>
                <plugin>
                    <groupId>org.jboss.as.plugins</groupId>
                    <artifactId>jboss-as-maven-plugin</artifactId>
                    <inherited>true</inherited>
                    <configuration>
                        <skip>false</skip>
                    </configuration>
                </plugin>
            </plugins>
            <finalName>${project.artifactId}-${project.version}</finalName>
        </build>
    </profile>

    <profile>
        <!-- To use this profile run command: [mvn clean package -P dev] -->
        <id>dev</id>
        <properties>
            <jta-data-source>${base-jta-data-source}-DEV</jta-data-source>
            <final-artifactId>${project.artifactId}-dev</final-artifactId>
        </properties>
        <build>
            <plugins>
                <plugin>
                    <artifactId>maven-surefire-plugin</artifactId>
                    <configuration>
                        <skip>false</skip>
                        <argLine>-Dfile.encoding=UTF-8</argLine>
                    </configuration>
                </plugin>
                <plugin>
                    <groupId>org.jboss.as.plugins</groupId>
                    <artifactId>jboss-as-maven-plugin</artifactId>
                    <inherited>true</inherited>
                    <configuration>
                        <skip>false</skip>
                    </configuration>
                </plugin>
            </plugins>
            <finalName>${final-artifactId}-${project.version}</finalName>
        </build>
    </profile>

</profiles>

```
Notice `<jta-data-source>`, `<final-artifactId>`, they are defined in `<profile>` element, belongs to each profile, so they can be different values. And in `<finalName>`, we use `final-artifactId` and `project.version` to generate package name. It will be like "mvn-profile-1.0-SNAPSHOT", "mvn-profile-dev-1.0-SNAPSHOT". All properties can be used by `${propertyname}`. Properties can be used out side of `pom.xml`. When packaing, maven can filter files and replace `${propertyname}` with real value. For example, if we want to change `context-root` of `jboss-web.xml`. We can change it like:
```
<context-root>${final-artifactId}</context-root>
```
Besides, we need to set filter in `pom.xml` in `<build><plugins><plugin>` element:
```
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-war-plugin</artifactId>
    <configuration>
        <failOnMissingWebXml>false</failOnMissingWebXml>
        <archive>
            <manifest>
                <addClasspath>false</addClasspath>
                <addDefaultImplementationEntries>true</addDefaultImplementationEntries>
            </manifest>
        </archive>

        <webResources>
            <resource>
                <directory>${project.build.directory}/generated-web</directory>
            </resource>

            <resource>
                <directory>src/main/web/WEB-INF</directory>
                <filtering>true</filtering>
                <targetPath>WEB-INF</targetPath>
                <includes>
                    <include>jboss-web.xml</include>
                </includes>
            </resource>
            <resource>
                <directory>src/main/web/WEB-INF</directory>
                <filtering>false</filtering>
                <targetPath>WEB-INF</targetPath>
            </resource>
        </webResources>
    </configuration>
</plugin>
```
[maven-war-plugin](https://maven.apache.org/plugins/maven-war-plugin/ ) will collect dependencies, resources and packing the files. Noticing element `<filtering>`, default is false. It will filter the files and replacing properties. But you should be responsible for files to be filtered. You just include the files that need to be filtered. Other files should be copy no filtering. Because image, certification would be broken after filtering.
All codes can be found [here](https://github.com/bejondshao/personal/tree/master/java/mvn-profile ). After downloading the module, you can verify this by running`mvn clean package` and `mvn clean package -P dev` to see the differences. Or you can download the packages {% asset_link mvn-profile.zip here %}.