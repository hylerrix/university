# Tomcat 权威指南

> 暂时的疑问：
>
> * Tomcat、Servlet、JSP、EL 版本变迁。
>
>
> * Tomcat 为什么要用 XML 作为配置文件格式，相比 Nginx 的 JSON 各有什么优缺点？
> * 精确的配置各大环境变量

Tomcat 是源自 Apache 软件基金会的 Java Servlet 容器与 Web 服务器实现。

Tomcat 不仅包含了 Java Servlet 技术，还包含了 JavaServer Pages 技术，除此之外还有用各种编程语言编写的传统静态网页和 CGI 程序。

## 在 Tomcat 中部署 Servlet 与 JSP  Web 应用程序

***CGI，通用网管接口***

在 Java Servlet 出现之前，大多数 Web 应用程序都是以 C/C++ 或 Perl 编写的。这些应用程序通常由多个静态 HTML 网页以及一些产生动态网页内容的 CGI 所构成。

CGI 是跨所有 Web 服务器品牌与实现的工业标准，可以写成独立于 Web 服务器的实现程序，但其设计本身会让它先天性执行缓慢，并无法拓充。

所有对 CGI 脚本的 HTTP 请求，操作系统都必须产生及执行新的进程，而且设计准则也会强制要求这么做。当 Web 服务器在大量网络负载下运行时，会启动和停止太多的进程，势必会造成服务器必须投入大部分资源以处理启动和停止作业，而非完成 HTTP 的请求。

***Web 服务器模块***

Web 服务器可以在启动时执行动态可加载的模块，这些模块会响应预先配置的 HTTP 请求样式，并将动态网页送至 HTTP 的客户端。

不足在于，虽然 Web 服务器的模块可以用与平台无关的方式来编写，但是没有针对 Web 服务器模块的 Web 服务器独立实现标准——它们只能针对您编写模块的特定服务器，而且可能无法用于其他的 Web 服务器实现。

***Servlet Web***

Java 将平台独立性引入服务器，以此作为制定快速且与平台无关的 Web 应用程序标准的解决方案的一部分——Java Servlet。Servlet 背后的意义是不启动新进程而使用 Java 简单但功能强大的多线程以响应请求。

Servlet Web 应用程序也被设计成课重新安置的形式，可以编写 Web 应用程序，以便将其程序重新映射至主机上不同的 URI，而无需重新编写应用程序内部的任何组件。

Tomcat 的配置文件总是称 Web 应用程序为 “context”。Tomcat 的主配置文件 server.xml 有一个叫做 Context 的 XML 元素，代表了 Web 应用程序的配置。

针对 Tomcat 的部署情况，Manager Web 应用程序允许不经重启 Tomcat 而实现本地和远程部署，并精细地集成了 Apache Ant 编译工具。

在开发过程中，最好将 context 配置为可重载的形式，从而在修改类文件时，Tomcat 将立即注意到这一变化并重新加载类，这一操作通常比停止整个 Web 应用程序、重新部署和重启要快得多——实际分发的产品中建议关闭 context 重载功能。

***主机，Host***

为了在 Tomcat 中部署 Web 应用程序，必须在主机 Host 下部署，一台主机代表了一个完全限定的域名或 IP 地址。

通过把 Host 的 autoDeploy 设置为 true，Tomcat 允许您在本地完成热部署操作。

Host 上设置 deployOnStartup="false"，可以让 Tomcat 在启动时不部署 Web 应用程序。

有两种形式可以将 Web 应用程序部署到 Tomcat 中：

* 解包目录：推荐，此时容易诊断 Web 应用程序的故障，大多数时不必重启 Web 应用程序。
* WAR 文件：不常见，但可以只需监视一个文件，有效增强对本地 shell 用户的安全防护。

***Host Manager Web 应用程序***

不想编辑 servet.xml 或想通过 Web 浏览器远程增加配置主机，可以通过 Admin Web 应用程序或 Host Manager Web 应用程序来完成。

***Web 应用程序的布局***

设计 J2EE 的目的，是为了让应用程序开发者无需重大改写或修改源码，就能将应用程序从一台符合规范的应用程序服务器移植到另一台上——可以以专业的可移植方式打包，例如，打包成 Web 应用程序的归档文件，或企业应用程序的归档文件 WAR。

常规布局：

```
/
/index.jsp
/products.jsp
/widgets/index.html
/widgets/pricing.jsp
/images/logo.png
/WEB-INF/web.xml
/WEB-INF/classes/com/acme/PriceServlet.class
```

将网站的自定义设定放入网站目录内的 XML 文件，即 DD 的好处之一是所有网站的自定义设定都与该网站的部署一起储存。这会简化维护工作，也使得将打包后的应用程序移植至其他服务器。

Tomcat 会自动保护 WEB-INF 目录的内容，以防止客户端的网页浏览器访问。此目录可以包含诸如数据库连接、用户名称以及密码的初始化参数。

***部署解包的 Web 应用程序目录***

第一次部署 Web 应用程序为 webapp 目录时，配置 Tomcat 以实现组织和启动 Web 应用程序的方法有两种：

* server.xml Context 部署：
  * server.xml 文件中增加一个 Context 元素并重启 Tomcat。
* Context XML 片段文件部署，两种方式：
  * 在 Tomcat 的 $CATALINA_HOME/conf/[EngineName]/[Hostname] 目录树中增加一个新的 context XML 片段文件，然后重启 Tomcat。
  * 创建为 Web 应用程序的 WEB-INF/context.xml 文件，然后重启 Tomcat。

***server.xml Context 部署***

要使用这一方法部署未打包的 webapp 目录，必须在应用程序的 server.xml 中增加一个 Context 元素，并将 webapp 的 Context 嵌套在 Host 容器元素中。

```
<Host name="localhost" appBase="webapps"
      unpackWARs="true" autoDeploy="false"
      xmlValidation="false" xmlNamespaceAware="false"
      
    <Context docBase="my-webapp" path="/my-webapp/"

</Host>
```

autoDeploy 设置为 false 会避免两次部署相同 Web 应用程序的现象。

* 第一次因 server.xml 中的 Context 配置而被部署，因为 deployOnStartup="true"。
* 第二次因该 autoDeploy 参数而进行自动部署。

Tomcat 启动后，会在 CATALINA_HOME/webapps/my-webapp 路径下查找 Web 应用程序对应的目录。如果 Tomcat 在该路径下找到了 Web 应用程序，那么 Tomcat 就会尝试部署它，并把它“安装”到 Web 服务器 URI 为 /my-webapp 的路径下，如果 Tomcat 在部署和启动时没有遇到错误，那就能用浏览器 http://localhost:8080/my-webapp 进行访问了。

***Context XML 片段文件部署***

Context XML 片段文件并不是完整的 server.xml 文件，而只是适合于 Web 应用程序的 Context 元素及任何子元素，就像 server.xml 中配置的那样。

Context 元素驻留在 context XML 片段文件中的时候无法指定路径属性。

Context XML 片段文件可以驻留在 $CATALINA_HOME/conf/[EngineName]/[Hostname] 目录中，此时 Tomcat 将该文件名用作 Web 应用程序的 Web 服务器 URI 路径。

Context XML 片段文件也可以放在 Web 应用程序本身，此时注意：

* 放在 META-INF/context.xml 中，不可更改这一文件名。缺点是不具备改变 URI 路径为 Tomcat 映射 Web 应用程序路径的方法。
* 一定不要设置主机上的 deployXML="false"，默认为 true。

Context XML 片段文件这一特性：

* 初衷之一——可以用区别于 server.xml 的配置文件配置 Web 应用程序，从而不需如此频繁地修改 server.xml。
* 宗旨——将 Web 应用程序配置模块与 Tomcat 配置文件的其他部分分开。但分开的并不彻底，Web 应用程序通常还需要定制 Connector 配置、定制 Host 配置、定制 GlobalNamingResources 配置等，此时不得不修改 server.xml。

***部署 WAR 文件***

部署 WAR 文件是将 Web 应用程序部署到 Tomcat 中的另一主要方法，Java Servlet 规范含有 WAR 文件的详细描述。

默认情况，当 Tomcat 部署 WAR 文件时，Tomcat 要做的第一件事情是解包 WAR 文件的内容到相同文件名的路径中去，去除 .war 拓展名。

* 此特性是可以通过设置 server.xml 中的 Host 元素的 unpackWARs="false" 以关闭解包行为，此时 Tomcat 将刚好从打包的 WAR 文件自身提供 Web 应用程序的文件。

第一次将 Web 应用程序部署为 WAR 文件时，有两种配置 Tomcat 辨认并启动应用的方法，大体同本章“部署解包的 Web 应用程序目录”一节：

* server.xml Context 部署。其中推荐设置 unpackWARs 为 true，参考“本章Host”一节。
* Context XML 片段文件部署。

***热部署***

不经过启动 Tomcat JVM 而实现 Web 应用程序的部署和解除部署，就是“热部署”。

* 本地文件系统热部署——所有工作都在同一台机器上执行。
* 远程热部署——从一台机器上将 Web 应用程序热部署到运行 Tomcat 的另一台机器上，推荐使用 Manager Web 应用程序。

热部署时在 Host 上的配置：autoDeploy="true"，deployOnStartup="false"。

* 在 server.xml 文件中创建一个 Context 容器 XML 元素，并嵌套到可以进行热部署的 Host 中。
* 将 Web 应用程序的 WAR 文件复制到可以进行热部署的 Host 的 appBase 中，Tomcat 将部署该文件并启动它。
* 创建一个 Context XML 片段文件，且该文件指向该 Web 应用程序的解包目录或 WAR 文件，并将 Context XML 片段文件放在$CATALINA_HOME/conf/[EngineName]/[Hostname] 目录中。

如果您没有提供任何 Context XML 片段文件，那么为了部署 Web 应用程序，Tomcat 将为此在内存中动态创建自己的 Context 配置。这种自动 Context 配置包括了在 Tomcat 全局 Context 配置文件的内容，可以在 CATALINA_HOME/conf/context.xml 中找到。

通过 Context XML 片段文件而不是在 server.xml 中配置，实现 Web 应用程序的热部署，这样处理的优点有：

* 直接在 Context XML 片段文件中更改内容，Tomcat 会自动部署。
* 删除某个 Comtext XML 片段文件，能快速被 Tomcat 注意到，并取消相应部署。

***使用 WAR 文件***

创建 WAR 文件过程与创建 JAR 文件过程完全相同。

jar 的命令行界面，甚至程序名，都是基于 Unix 的 tar 命令，该命令现多用于打包文件，以便在 Internet 上传递，而不是用于磁带命令。

```
jar cvf jar-file.jar dir [...]
```

c 表示创建一个归档文件。v 可选，表示执行该命令需要得到一个详细列表。f 必选，表示紧随 c、v、f 之后的参数是输出文件名。

```j
jar cvf jar-file.war dir [...]
```

war 后缀是 Servlet 后缀推荐的做法。

Ant 编译工具

***Manager Web 应用程序***

可以通过 Web 管理自己的 Web 应用程序，想要拥有该权限，但必须先：

* 配置 CATALINA_HOME/conf/tomcat-users.xml 文件。

Manager 也能让您停止、重新加载、删除或解除 Web 应用程序的部署。

* 如果此应用程序是从配置文件启动的，则下次再启动 Tomcat，它还会再出现。
* 如果 Web 应用程序是储存在所谓 Tomcat 的 appBase 目录中，则 Manager 的解除部署功能会自硬盘中删除 Web 应用程序的文件，包括 Context XML 片段文件。

Servlet 映射详情可以在 Complete Server Status 页上个查找它。例：

```
http://localhost:9999/manager/status/all
```

***Apache Ant 自动化部署***

虽然也可使用诸如 make、Perl、shell 脚本或预处理文件，不过在 Java 与 Tomcat 社区中，Ant 已渐渐成为自动部署的标准工具。

Ant 可以执行非 Java 程序。Ant 由 Java 编写，有可靠的 JVM，执行 Java 功能会相当快速，含有执行一般操作的内嵌任务的庞大链接库，通常不需要执行外部程序——也就是说，Ant 含有能读/写这些及其他格式文件、复制文件和编译 Java 程序等的 OS 便携式 Java 类。Ant 会读入以 XML 编写的构建文件，以找寻要执行的命令，一般文件名为 build.xml。

***构建 JAR/WAR***

Ant 有处理 JAR/WAR 文件，及复制文件的嵌入式任务。

实例，可产生 WAR 文件的 Ant 构建文件 build.xml：

```
<project name="Hello World Web Site"
         default="war"
         basedir=".">
    <!-- 构建 WAR 文件 -->
    <target name="war"
            description="Builds the WAR file.">
        <war destfile="${deploy.war}"
             webxml="${basedir}/webapp-dir/WEB-INF/web.xml"
             basedir="${basedir}/webapp-dir"
             excludes="WEB-INF/**/*">
            <lib dir="${basedir}/webapp-dir/WEB-INF/lib"/>
            <webinf dir="${basedir}/webapp-dir/WEB-INF/"
                    excludes="web.xml"/>
            <metainf dir="${basedir}/webapp-dir/META-INF"/>
        </war>
    </target>
</project>
```

***通过 Ant 进行部署***

通过 Ant 进行部署的方法有很多种：

* 复制 Web 应用程序到本地 Tomcat 安装的部署目录中，只需要一个 copy 指令。
* 使用 Manager Web 应用程序，通过网络“远程”部署，使用了 HTTP 协议。
* 使用 Tomcat 的独立部署器，该目录包含了通过 HTTP 给 Manager Web 应用程序提供命令的 Ant 编译工具和所有必要的 JAR 文件。
* 使用 Ant 的 scp 任务，远程复制文件

***Symbolic Links***

默认情况下，出于安全防护因素考虑，Tomcat 不允许使用 Web 应用程序内部的符号链接——您在 Tomcat 正给文件提供服务的 Web 应用内部放置符号链接，而且您要求这一符号链接与 Web 客户端通信，那么 Tomcat 将会回应 404 页面。

如果要 Tomcat通过符号链接供应文件，需要基于每个 Web 应用程序进行显式 Context 元素配置。

## 配置

Tomcat 也具备“域”的功能，允许实现网站特定行为的授权用户清单。

$CATALINA_HOME/conf 目录中的主要配置文件：

* server.xml：Tomcat 主配置文件。
* web.xml：servlet 与其他适用于整个 Web 应用程序设置的配置文件，必须符合 Servlet 规范的标准格式。
* tomcat-users.xml：Tomcat 的 UserDatabaseRealm 用于认证的默认角色、用户以及密码清单。
* catalina.policy：Tomcat 的 Java 安全防护策略文件。
* context.xml：默认的 context 配置，应用于安装了 Tomcat 的所有主机的所有部署内容。