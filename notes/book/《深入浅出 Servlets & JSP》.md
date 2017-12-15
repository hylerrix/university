# 深入浅出 Servlets & JSP

> 目前还待深入：Tomcat 容器原理

## 为什么使用 Servlet & JSP？

***HTML 速成指南***

***什么是 HTTP 协议？***

* TCP 负责确保从一个网络节点向另一个网络节点发送的文件能作为一个完整的文件到达目的地

* IP 是一个底层协议，负责把数据块(数据包)沿路移动/路由到目的地。

* HTTP 则是另一个网络协议，有一些 Web 特定的特性，依赖于 TCP/IP。

  * 请求流的关键要素：HTTP 方法、要访问的页面、表单参数


  * 响应流的关键要素：状态码、内容类型、内容

***GET 和 POST 请求剖析***

* GET 的总字符数是有限的，取决于服务器，发送的数据会追加到 URL 的后面。
* POST 不能对一个表单提交建立书签。
* MIME 类型是 content-type 相应首部的值，告诉浏览器要接受的数据是什么类型。

***URL，统一资源定位符***

* 端口表示与服务器硬件上运行的一个特定软件的逻辑连接。
* 服务器上有 65536 个端口，从 0~1023 的 TCP 端口已经保留。
  * 21：FTP
  * 23：Telnet
  * 25：SMTP
  * 37：TIME
  * 80：HTTP
  * 110：POP3
  * 443：HTTPS

***Web 服务器***

* Web 服务器应用只提供静态页面，不生成动态内容，不在服务器上保存数据。
* Web 服务器通过“CGI”(公共网关接口) 程序辅助，大多数 CGI 程序都编写成 Perl 脚本。
* 如果使用 CGI，用户点击一个链接，它的 URL 指向一个 CGI 程序，而不是一个静态页面。
* Servlet 和 CGI 在 Web 服务器中都扮演着辅助应用的角色。

***Servlet 揭秘(编写、部署、运行)***

***JSP，HTML 中引入 Java***

## Web 应用体系结构

***Servlet 也需要帮助——使用 Web 容器***

请求到来时，必须有人实例化 Servlet 或至少创建一个新的线程处理这个请求，必须有人调用 Servlet 的 doPost() 或 doGet() 方法。必须有人把请求和相应交给 Servlet，还得有人管理 Servlet 的生与死以及 Servlet 的资源。

***什么是容器***

Servlet 没有 main() 方法，它们受控于另一个 Java 应用，这个 Java 应用称为容器，Tomcat 就是这样一个容器。

如果 Web 服务器应用得到一个指向某 Servlet 的请求，此时服务器把请求交给部署该 Servlet 的容器。要由容器向 Servlet 提供 HTTP 请求和相应，而且要由容器调用 Servlet 的方法，如 doPost() 或 doGet()。

***容器能提供什么***

* 通信支持：通过容器，能轻松使 Servlet 与 Web 服务器对话，无需自己建立 ServletSocket、监听端口、创建流、线程管理、安全性、网络通信等。
* 生命周期管理：容器控制着 Servlet 的生与死，它会负责加载类、实例化和初始化 Servlet，调用 Servlet 方法，并使 Servlet 实例能够被垃圾回收——我们不用太多的考虑资源管理了。
* 多线程支持：容器会自动地为它接收的每个 Servlet 请求创建一个新的 Java 线程。针对客户的请求，如果 Servlet 已经运行完相应的 HTTP 服务方法，这个线程就会结束。
* 声明方式实现安全：利用容器，可以使用 XML 部署描述文件来配置和修改安全性。
* JSP 支持：容器负责把 JSP 代码翻译成真正的 Java。

***容器如何处理请求***

* 用户点击一个链接，其 URL 指向一个 Servlet 而不是静态页面。
* 容器“看出来”这个请求的是一个 Servlet，所以容器创建两个对象：HTTPServletResponse 和 HttpServletRequest。
* 容器根据请求中的 URL 找到正确的 Servlet，为这个请求创建或分配一个线程，并将创建的请求和相应对象传递给这个 Servlet 线程。
* 容器调用 Servlet 的 service 方法，根据请求的不同类型，service() 方法会调用 doGet() 或 doPost() 方法。
* doGet() 或 doPost() 方法生成动态页面，并把这个页面“填入”响应对象。
* 线程结束，容器把响应对象转换为一个 HTTP 响应，把它发回给客户，然后删除请求和响应对象。

***代码里有什么？Servlet 何以成为一个 Servlet***

* 在实际中，99.9% 的 servlet 都会覆盖 doGet() 或 doPost() 方法。
* 99.99999% 的 Servlet 都是 HttpServlet。
* Servlet 从容器中拿到请求和响应的对象。

***容器怎样找到 Servlet***

有多种方法：

* 把映射硬编码写到 HTML 页面中，客户使用 Servlet 的绝对路径和文件名。
* 使用容器开发商提供的工具来完成映射。
* 使用某种属性表来存储映射。

一个 Servlet 可以有 3 个名字：客户知道的 URL 名、部署人员知道的秘密的内部名、实际的文件名。

建立 Servlet 名的映射，这有助于改善应用的灵活性和安全性。

***使用部署描述文件将 URL 映射到 Servlet***

将 Servlet 部署到 Web 容器时，会创建一个相当简单的 XML 文档，称为部署描述文件(DD)。

DD 会告诉容器如何运行 Servlet 和 JSP，不只是为了映射 Servlet 名。

可以使用两个 XML 元素把 URL 映射到 Servlet 中：

* `<servlet>` 内部名映射到完全限定类名。
* `<servlet-mapping>` 内部名映射到公共 URL 名。

DD 提供了一种“声明”机制来定制 Web 应用，而无需修改源代码。

* 定制 URL 映射、安全角色、错误页面、标记库、初始配置信息。

DD 的优点：

* 尽量少改动已经测试过的源代码。
* 即使你手上并没有源代码，也可以对应用的功能进行调整。
* 不用重新编译和测试任何代码，也可以让你的应用适应不同的资源。
* 可以更容易地维护动态安全信息，如访问控制列表和安全角色。
* 非程序员也可以修改和部署你的 Web 应用。

***Servlet & JSP 世界中的 MVC***

Servlet 中 doGet() 方法把请求转发到一个特定的 JSP 页面，而不是把 HTML 打印到输出流。

MVC 关键是，业务逻辑要与表示分离。

* 控制器——Servlet：从请求获得用户输入，并明确这些输入对模型有什么影响。告诉模型自行更新，并且让视图 JSP 能得到新的模型状态。
* 模型——普通 Java 类：包含具体的业务逻辑和状态。系统中只有这部分与数据库通信。
* 视图——JSP：负责显示方面，它从控制器得到模型的状态，另外视图还要获得用户输入，并交给控制器。

***J2EE 如何继承这一切***

J2EE 是一种超级规范，包括 Servlets 2.4 规范和 JSP 2.0 规范，这些是对应 Web 容器的。另外 J2EE 1.4 规范还包括 EJB 2.1 规范，这对应 EJB 容器。换句话说，Web 容器用于 Web 组件，EJB 容器用于业务组件。

一个完全兼容的 J2EE 应用服务器必须有一个 Web 容器和一个 EJB 容器，Tomcat 只是一个 Web 容器。

早先，比如 2000 年，确实可以找到完整的 J2EE 应用服务器和独立的 Web 容器，也能找到独立的 EJB 容器。不过，如今几乎所有 EJB 容器都作为完整 J2EE 服务器的一部分，但独立的 Web 容器依然存在。独立的 Web 容器通常配置为与一个 HTTP Web 服务器写作。

## MVC 迷你教程

***构建一个真正的（小）Web 应用***

我们的 4 大步骤：

* 分析用户的视图，以及高层体系结构。
* 创建用于开发这个项目的开发环境。
* 创建用于部署这个项目的部署环境。
* 对 Web 应用的各个组件完成迭代式的开发和测试。

***创建你的开发环境***

如下结构比较适用于小型和中型的项目，部署时要把这个目录结构适当地复制到特定容器所希望的位置上。

```
.
├── etc
│   └── web.xml # 配置文件放在这里
├── lib # 在这里放第三方 JAR 文件
├── src # 所有 Java 代码都在 src 目录下
│   └── com
│       └── example # 控制器组件与模型组件分离
│           ├── model
│           │   └── BeerExpert.java
│           └── web
│               └── BeerSelect.java
└── web # 静态和动态视图组件放在这里
    ├── form.html
    └── result.jsp
```

使用如上标准的包结构有如下好处：

* 项目管理、命名空间管理、可移植性和可重用性

***创建你的部署环境***

要部署一个 Web 应用，涉及到容器特定的规则，还涉及 Servlet 和 JSP 规范的需求。

* 特定于 Tomcat 的部分：Tomcat 主目录。
* Servlet 规范的部分：web.xml 必须放置在 WEB-INF 中。
* 特定于应用的部分：里面的包结构与开发环境中使用的结构完全相同。

***创建应用的路线图***

这一部分又分为 5 个小步骤：

* 构建和测试用户最初请求的 HTML 表单。
* 构建控制器 Servlet 的第一个版本。这个版本通过 HTML 表单来调用，并打印它接收到的参数。
* 为专家/模型类构建一个测试类，然后构建并测试专家/模型类本身。
* 把 Servlet 升级到第 2 版。这个版本增加了一个功能，可以调用模型类。
* 构建 JSP，把 Servlet 升级到第三个版本，然后再测试整个应用。

***部署和测试开始页面***

要进行测试，需要把它部署到容器(Tomcat)目录结构中：

* 在开发环境中串讲 HTML。
* 把这个文件复制到部署环境。
* 在开发环境中创建 DD。
* 把这个文件复制到部署环境。
* 启动 Tomcat。
* 测试页面。

***逻辑名映射到 Servlet 类文件***

请求 URL -> Web 容器 -> Web 应用 -> web.xml -> 类

***控制器 Servlet 的第一版***

***编译、部署和测试控制器 Servlet***

编译

```
javac -classpath /usr/local/apache-tomcat-9.0.1/lib/servlet-api.jar:lib/classes:. -d lib/classes src/com/example/web/BeerSelect.java
```

部署 -> 复制到部署结构 WEB-INF 下

重启 Tomcat

***构建和测试模型类***

在 MVC 中，模型是指应用的“后台”，通常是一个遗留系统。

模型规范

* 包应当是 com.example.model
* 其目录结构应当是 /WEB-INF/classes/com/model
* 提供一个方法 getBrands()，取一个喜欢的啤酒颜色作为参数，并返回一个 ArrayList

为模型构建测试

***第二版 Servlet 的代码***

重新编译 Servlet 和部署模型类

***第三版 Servlet 的代码***

容器提供了一种称为“请求分派的机制”，允许容器管理的一个组件调用另一个组件。可以使用这种机制，Servlet 从模型中得到信息，把它保存在请求对象中，然后把请求分派给 JSP。

## 作为 Servlet

Servlet 的任务是得到一个客户的请求，再发回一个响应。

***Servlet 的生命周期***

* 加载 Servlet 类
* 初始化 Servlet
* init()
* service() -> 处理客户请求，doGet()、doPost()，每个请求都在一个单独的线程中运行。
* destroy()

***Servlet 继承的声明周期方法***

Servlet 接口，Javax.servlet.Servlet，此接口指出所有 Servlet 都有这 5 个方法。

* service(ServiceRequest, ServletResponse)、init(ServletConfig)、destroy()、getServletInfo()

GenericServlet 类，Javax.servlet.GenericServlet，是一个抽象类，实现了需要的大部分基本 Servlet 方法，包括 Servlet 接口中的方法。

* service(ServletRequest, ServletResponse)、init(ServletConfig)、init()、destroy()、getServletConfig()、getServletInfo()、getInitParameter(String)、getInitParameterNames()、getServletContext()、log(String)、log(String, Throwable)

HttpServlet 类，javax.servlet.http.HttpServlet，是一个抽象类，实现了一个 service() 方法来反映 Servlet 的 HTTP 特性——Service() 方法不仅仅可以取一般的 Servlet 请求和响应，还可以取 HTTP 特定的请求和相应作参数。

* service(HttpServletRequest, HttpServletResponse)、service(ServletRequest, ServletResponse)、doGet(HttpServletRequest, HttpServletResponse)、doPost(HttpServletRequest, HttpServletResponse)、doHead(HttpServletRequest, HttpServletResponse)、doOptions(HttpServletRequest, HttpServletResponse)、doPut(HttpServletRequest, HttpServletResponse)、doTrace(HttpServletRequest, HttpServletResponse)、doDelete(HttpServletRequest, HttpServletResponse)、getLastModified(HttpServletRequest)

MyServlet 类，由开发者实现，做的只是覆盖所需的 HTTP 方法。

***生命周期中的三大重要时刻***

* init()
  * 何时调用：Servlet 实例创建后，并在 Servlet 能为客户请求提供服务之前，容器要对 Servlet 调用 init()。
  * 作用：使你在 Servlet 处理客户请求之前有机会对其初始化。
  * 是否覆盖：有可能，初始化代码时使用。
* Service()
  * 何时调用：第一个客户请求到来时，容器会开始一个新线程，或者从线程池分配一个线程，并调用 Servlet 的 service() 方法。
  * 作用：该方法查看请求，确定 HTTP 方法，并在 Servlet 上调用相应方法。
  * 是否覆盖：不应该覆盖。
* doGet()、doPost()
  * 何时调用：service() 方法根据请求的 HTTP 方法来调用 doGet() 或 doPost()，因为你可能只会用到这两个方法。
  * 作用：告诉 Web 应用支持什么类型的请求。
  * 是否覆盖：至少覆盖其中之一。

***每个请求都在一个单独的线程中运行***

容器运行多个线程来处理对一个 Servlet 的多个请求。

Servlet 类不会有多个实例，每个请求会生成一对新的请求和响应对象。

与 Servlet 相关的一切，除了 JSP 外，要么在 javax.servlet 中，要么在 javax.servlet.http 中。前者通常包括通用 Servlet 类和接口，后者中都是与 HTTP 有关的。

***Servlet 的加载和初始化***

第一步：容器启动时，会寻找已经部署的 Web 应用，然后开始搜索 Servlet 类文件。

第二步：加载类——可能在容器启动时发生，也可能在第一个客户使用时进行。无论哪种情况，在 Servlet 没有完全初始化之前绝不能运行 Servlet 的 service() 方法。

Servlet 构造函数只是使之成为一个对象，而不是一个 Servlet；对象成为一个 Servlet 时，会得到 Servlet 该有的所有特权——能够使用 ServletContext 引用从容器得到信息、秘密握手、记录事件日志、得到其他资源引用、保存其他 Servlet 的属性等。

ServletConfig 对象，部署并运行后参数不能再改变：

* 每个 Servlet 都有一个 Servlet Config 对象。
* 用于向 Servlet 传递部署时信息，避免硬编码到 Servlet。
* 用于访问 ServletContext。
* 参数在部署描述文件中配置。

ServletContext 对象，每个 Web 应用只有一个：

* 每个 Web 应用只有一个 ServletContext，或叫做 AppContext。
* 用于访问 Web 应用参数，也在部署描述文件中配置。
* 相当于一种应用公告栏，可以在这里放置消息，应用的其他部分可以访问这些消息。
* 用于得到服务器信息，包括容器名和容器版本，以及所支持 API 的版本。

***Servlet 真正的意义是处理请求***

ServletRequest 接口 -> HttpServletRequest 接口。

ServletResponse 接口 -> HttpServletResponse 接口。

容器来实现 HttpServletRequest 接口和 HttpServletResponse 接口。

GenericServlet 不限于 HTTP 协议。

***GET 和 POST 的区别***

GET 对参数数据有限制，参数数据只能是放在请求行的内容

POST 有一个消息体，有时称为“负载”。

***响应***

响应会调用两个方法：setContentType() 和 getWriter()

响应对象依次继承的接口：

* ServletResponse 接口，Javax.servlet.ServletResponse：getBufferSize()、setContentType()、getOutputStream()、getWrite()、setContentLength()
* HttpServletResponse 接口，javax.servlet.http.HttpServletResponse：addCookie()、addHeader()、encodeURL()、sendError()、setStatus()、sendRedirect()

对于输出，可以选择字符还是字节输出。ServletResponse 接口提供了两个流可供选择，ServletOutputStream 用于输出字节，PrintWriter 用于输出字符数据。

响应首部

* setHeader() 覆盖现有的值
* addHeader() 增加另外一个值

sendRedirect() 方法可以使 Servlet 重定向，其中的相对 URL 和绝对 URL 有区别。

***请求分派是在服务器端做工作***

重定向让客户来完成工作，而请求分派要求服务器上的某某来完成任务。

RequestDispatcher。

请求分派中，URL 没有任何变化。

## 作为 Web 应用

