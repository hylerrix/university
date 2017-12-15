# Java

操作系统：MacOS

Web 服务器：Nginx

Web 应用容器：Tomcat



目标：MacOS 下的 Java Web 开发全解



## 第一天-开发环境搭建

### 安装 Java 环境

> 环境变量的设置、Java EE、Java SE 安装、官网、Web 服务器安装、Eclipse 安装与使用、Tomcat 安装、JDK、SDK、JRE

#### Java SE 的安装

下载 JDK，dmg 格式

查看 JDK 版本

* 图形化：Finder，Macintosh HD/资源库/Java/JavaVirtualMachines
* 命令行：/Library/Java/JavaVirtualMachines/

JDK 环境变量的配置

* echo $PATH
* whereis java -> /usr/bin/java
* /usr/bin，/usr/lib，/etc/bashrc，/etc/profile
* java -version

```
java version "1.8.0_101"
Java(TM) SE Runtime Environment (build 1.8.0_101-b13)
Java HotSpot(TM) 64-Bit Server VM (build 25.101-b13, mixed mode)
```

#### Tomcat 的安装

Tomcat 的文件结构及功能 

Tomcat 配置文件修改 ./conf/server.xml

Tomcat 命令行快速开启服务配置

#### Eclipse 的安装与使用

eclipse 的安装目录

eclipse 的插件目录 /Applications/Eclipse.app/Contents/Eclipse/plugins

eclipse 配置 Tomcat：preference -> Server -> RuntimeEnvironrenment -> Add

#### 实战测试

新建 Web 项目：new -> Dynamic Web Project

Dynamic Web Project 初始化架构

在 Tomcat 下运行 index.jsp

* 发布工程到 Tomcat： ./WebContent 下新建 index.jsp，右键项目根目录 -> run on server，此时 Tomcat 会以 WorkSpace 为根目录

```
.
├── .classpath
├── .project
├── .settings
│   ├── .jsdtscope
│   ├── org.eclipse.jdt.core.prefs
│   ├── org.eclipse.wst.common.component
│   ├── org.eclipse.wst.common.project.facet.core.xml
│   ├── org.eclipse.wst.jsdt.ui.superType.container
│   └── org.eclipse.wst.jsdt.ui.superType.name
├── WebContent
│   ├── META-INF
│   ├── WEB-INF
│   └── index.jsp
├── build
│   └── classes
└── src
```

## 第二天-基础概念深入

***Java EE 和 Spring***

无论 Java EE 还是 Spring 都使用了同样的核心 APIs（Servlet、JPA、JMS、BeanValidation 等）

Spring：Spring Security、Spring Integration、Spring XD、Spring Social、Spring AOP

Java EE：JBoss Forge、Wildfly Swarm、Picketlink

***Java SE、Java EE 历史、特性、现阶段***

![](http://www.oracle.com/ocom/groups/public/@otn/documents/digitalasset/1895611.png)

 