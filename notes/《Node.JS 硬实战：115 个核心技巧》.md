# Node.JS 硬实战：115 个核心技巧

# 第一部分：Node 基础

## 入门

* 事实上，服务端的 JavaScript 和客户端的 JavaScript 存在的时间一样长。
* 对于其它脚本语言来说，要实现非阻塞 I/O 通常需要依赖特殊的类库。
* 在 JavaScript 与生俱来的 callback 下，我们能在统一进程中异步地操作文件读写、网络 sockets 以及其他 I/O 操作。
* Node 不仅仅是 V8，还涵盖了从 TCP 服务端到同步或者异步的文件管理。
* 在传统的编程语言中，I/O 的操作将阻塞进程直到它完成为止。
* Node 的适用场景是 Web API 和网络爬虫，且网络爬虫主要的消耗就在于网络和文件读写的 I/O。
* Node 可以创建任意你想要的 TCP/IP 服务，比如网络游戏服务器。
* Node 的强项：
  * 广告分布系统：有效地分配小块信息；处理潜在的网络速度慢的连接；容易拓展为多个处理器或服务器。
  * 游戏服务器：使用 JavaScript 来构建业务逻辑模型；不使用 C 语言来开发迎合特定网络的服务器程序。
  * 内容管理系统、博客：可以很轻松地创建 RESTful JSON APIs；轻量的服务器。
* Node 的主要特性是它的标准类库、模块系统以及 npm 包管理系统。
* Node 的标准类库主要由二进制类库以及核心模块两部分组成。二进制类包括 libuv，它为网络以及文件系统提供了快速的事件轮询以及非阻塞的 I/O。
* EventEmitter 是大多数 Node 核心模块的基础，只要是支持事件响应的核心模块如 Stream、网络、文件系统全部继承于它。我们可以基于 EventEmitter 来创建自己基于事件的 API。
* Stream：高拓展性 I/O 的基础，非常擅长于处理数据，无论是读、写或是转换。其继承于 EventEmitters，能被用来在不可预测的输入下创建数据。通过 Node 的 stream API，你可以创建一个对象接收关于连接的时间，在接收到新数据时触发 data 事件，在结束时触发 end 事件，在有错误时触发 error 事件。
* FS：处理文件。Node 的文件模块不但可以通过非阻塞的 I/O 读写文件，而且它也有同步的方法。
* 网络：创建网络客户端与服务端。Node 不局限于 HTTP 开发。
* process 对象可以让我们把数据传入或传出标准 I/O 流。

## 全局变量：Node 环境

* 全局对象能被用在所有的模块中。一些全局对象在每一个模块中都是独立的实体，比如 module 对象。
* 一个重要的内置全局对象是 process，它可以用来与操作系统通信。另一个重要的全局对象是 Buffer 类，解决 JavaScript 天生缺乏对二进制数据支持的问题。
* UNIX 系统中 npm 的全局目录通常是 /usr/local/lib/node_modules。
* Node 模块可以被安装、加载、创建、管理。
* Node 中的 require 返回了一个对象而不是像 C 语言中的预处理器那样把代码加载到当前的命名空间中。
* 管理模块不仅仅是 npm 的工作，Node 有一套内置的模块系统，基于 CommonJS modules/1.1 标准。
* exports 对象的作用域是在一个模块中。
* 要从缓存中删除一个模块，可以使用 delete 关键字，还需要模块的绝对路径，可以通过 require.resolve 来获取。例如：delete require.cache(require.resolve('./myclass'))
* Node 可以将目录作为模块，可以把相关模块按逻辑组合起来。加载目录时，会先尝试读取该目录下 package.json 中的 main 字段所指向的 js 文件，如果没有则尝试读取该目录的 index.js 文件。
* 在 UNIX 或者 Windows 系统中，文字可以通过命令行导入 Node 进程。无论你需要从一个应用中读取数据，还是写入数据，通过 process 对象来读写 I/O 流是一个有用的技巧。
* Console 对象的方法有：log、info、warn、error、dir、time、timeEnd、trace、assert
  * info 和 warn 方法是 log 和 error 的同义词，区别是输出流不同。console.error() 将写入 process.stderr 流而不是 process.stdout 流，这意味着我们可以在终端或者 shell 脚本中将 Node 程序的错误消息重定向。
  * trace 方法在当前执行点产生堆栈日志，产生的堆栈轨迹包含了执行异步回调的代码的行号。
  * time 方法和 timeEnd 方法联合起来使用可以完成对耗时的操作的基准测试，不需要依赖其他工具。这些方法基于 Date.now() 计算函数执行时间，精确到毫秒。要获取更加准确的基准，可以使用第三方模块 benchmark。
* process 对象能被用来获取操作系统的信息，并且通过退出码、信号量与其他进程进行通信。
  * process.memoryUsage() 返回一个有 3 个属性的对象来描述当前进程的内存使用情况。rss，常驻内存大小；heapTotal，动态分配的可用内存；heapUsed，已经使用的堆大小。
  * process.argv 用来处理命令行参数。比较复杂的程序可以使用参数解析模块，如 optimist 或 commander。
  * process.exit() 在程序终止时给出一个退出码，对于 Windows 或 UNIX 系统都很重要。Node 程序默认返回 0 的退出状态，意味着程序正常终止。任何的非 0 状态码被认为是一个错误。在 UNIX 中，这个状态码可以通过 $? 在 shell 中获取，在 Windows 中可以通过 %errorlevel% 获取。
  * 因为许多 Node 程序是异步执行的，有时候我们需要显式地调用 process.exit 或者关闭 I/O 链接来使 Node 程序能优雅地退出。
  * 大多数现代操作系统通过信号把简单的消息发给一个程序。处理信号的程序通常运行在一个程序的后台，因为这可能是它们通信的唯一办法。process 对象是一个 EventEmitter 对象，这意味着你可以对它添加监听器。在 UNIX 系统中对一个 POSIX 信号添加一个监听器就可以了。对信号的监听可以用来满足 UNXI 程序期待的行为，比如许多服务器以及进程守护程序在 SIGHUP 信号时会重新加载配置文件。
  * Node 在文档中建议，接口要么是同步的，要么是异步的，这表示如果你有一个方法接受一个回调，并可能异步的调用它，那么你也应该在同步的情况下通过 process.nextTick 来执行它，这样可以确保执行的顺序性。
* Node 实现了 JavaScript 的计时器功能——setTimeout、setInterval、clearTimeout，以及 clearInterval。这些方法全局可用。

## Buffers：使用比特、字节以及编码

* ​

## 