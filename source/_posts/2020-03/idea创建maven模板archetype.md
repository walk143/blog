---
title: idea创建maven模板archetype
date: 2020-03-18 22:31:34
tags:
    - idea
    - 配置
    - maven
categories:
    - 工具
    - 软件
    - idea
---

idea用archetype创建模板时，默认从`remote`下载，调用`repo.maven`地址，会出现超时错误。本文利用idea创建一个自定义的模板部署到nexus私服，方便以后新建项目时直接调用。
<!-- more -->

# 创建一个maven项目

此项目的结构，即为生成后的`archetype`模板结构。当之后使用此`archetype`进行新建项目时，会自动创建相同目录结构及maven依赖。

```
groupId	com.sloera
artifactId	boot-starter
version	1.0-SNAPSHOT
archetypeGroupId	org.apache.maven.archetypes
archetypeArtifactId	maven-archetype-quickstart
archetypeVersion	RELEASE
```

创建完成如图所示

{% asset_img 1584542754564.png 构建成功 %}

目录结构(`tree /f`生成)为

```
│  boot-starter.iml
│  pom.xml
│
├─.idea
│      compiler.xml
│      encodings.xml
│      misc.xml
│      workspace.xml
│
└─src
    ├─main
    │  └─java
    │      └─com
    │          └─sloera
    │                  App.java
    │
    └─test
        └─java
            └─com
                └─sloera
                        AppTest.java
```



可在最外层`pom.xml`中添加自己的依赖。或添加自定义部署到nexus私服。

```xml
<!--发布到私有nexus-->
<distributionManagement>
    <repository>
        <id>nexus-releases</id>
        <name>Nexus Releases Repository</name>
        <url>http://192.168.1.166:8712/repository/maven-releases/</url>
    </repository>
    <snapshotRepository>
        <id>nexus-snapshots</id>
        <name>Nexus Snapshots Repository</name>
        <url>http://192.168.1.166:8712/repository/maven-snapshots/</url>
    </snapshotRepository>
</distributionManagement>
```



需要添加`archetype`插件。

```xml
<!--  archetype插件-->
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-archetype-plugin</artifactId>
    <version>3.1.2</version>
</plugin>
```

# 构建部署archetype

在项目根位置执行`mvn archetype:create-from-project`

执行成功后的目录结构为：

```
│  boot-starter.iml
│  pom.xml
│
├─.idea
│      compiler.xml
│      encodings.xml
│      misc.xml
│      workspace.xml
│
├─src
│  ├─main
│  │  └─java
│  │      └─com
│  │          └─sloera
│  │                  App.java
│  │
│  └─test
│      └─java
│          └─com
│              └─sloera
│                      AppTest.java
│
└─target
    └─generated-sources
        └─archetype
            │  pom.xml
            │
            ├─src
            │  ├─main
            │  │  └─resources
            │  │      ├─archetype-resources
            │  │      │  │  pom.xml
            │  │      │  │  __artifactId__.iml
            │  │      │  │
            │  │      │  ├─.idea
            │  │      │  │      compiler.xml
            │  │      │  │      encodings.xml
            │  │      │  │      misc.xml
            │  │      │  │      workspace.xml
            │  │      │  │
            │  │      │  └─src
            │  │      │      ├─main
            │  │      │      │  └─java
            │  │      │      │          App.java
            │  │      │      │
            │  │      │      └─test
            │  │      │          └─java
            │  │      │                  AppTest.java
            │  │      │
            │  │      └─META-INF
            │  │          └─maven
            │  │                  archetype-metadata.xml
            │  │
            │  └─test
            │      └─resources
            │          └─projects
            │              └─basic
            │                      archetype.properties
            │                      goal.txt
            │
            └─target
                │  boot-starter-archetype-1.0-SNAPSHOT.jar
                │
                ├─classes
                │  ├─archetype-resources
                │  │  │  pom.xml
                │  │  │  __artifactId__.iml
                │  │  │
                │  │  ├─.idea
                │  │  │      compiler.xml
                │  │  │      encodings.xml
                │  │  │      misc.xml
                │  │  │      workspace.xml
                │  │  │
                │  │  └─src
                │  │      ├─main
                │  │      │  └─java
                │  │      │          App.java
                │  │      │
                │  │      └─test
                │  │          └─java
                │  │                  AppTest.java
                │  │
                │  └─META-INF
                │      └─maven
                │              archetype-metadata.xml
                │
                └─test-classes
                    └─projects
                        └─basic
                                archetype.properties
                                goal.txt
```

进入`cd target/generated-sources/archetype`目录。

在`archetype`下的`pom.xml`添加自己的私服部署地址。

发布生成的项目到私有仓库。或本地仓库。

`mvn clean deploy`

此时已完成`archetype`模板创建。

# 新建

打开idea，选择maven新建项目如图。

{% asset_img 1584543949304.png 添加archetype %}

选中`Create from archetype`，点击`Add Archetype`，填写的信息为刚才发布的地址。即可在列表中添加自己的模板。

进行idea设置。如下图，使maven构建时不从`remote`读取配置。

{% asset_img 1584544209233.png 设置maven运行参数 %}

> archetypeCatalog用来指定maven-archetype-plugin读取archetype-catalog.xml文件的位置：
>
> internal——maven-archetype-plugin内置的
>
> local——本地的，位置为~/.m2/archetype-catalog.xml
>
> remote——指向Maven中央仓库的Catalog

此时新建maven项目时，选中`boot-starter-archetype`即为从maven私服中调取。



## 注：

可在构建一次后，将`-DarchetypeCatalog=local`，使得之后的创建为从本地仓库读取。

配置自定义archetype参考[Archetype Repository](https://maven.apache.org/archetype/maven-archetype-plugin/archetype-repository.html)

# 参考

<https://blog.csdn.net/Big_Blogger/article/details/78777676>

<https://maven.apache.org/archetype/maven-archetype-plugin/advanced-usage.html>