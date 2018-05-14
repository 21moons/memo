# 1 Netty--异步和事件驱动

``` java
// 创建一个新的 ServerSocker, 用来监听指定端口上的连接请求
ServerSocket serverSocket = new ServerSocket(portNumber);
// 对 accept() 方法的调用将被阻塞, 知道一个连接建立
Socket clientSocket = serverSocket.accept();
// 下面两个流对象都派生于该套接字的流对象
BufferedReader in = new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));
PrintWriter out = new PrintWriter(clientSocket.getOutputStream(), true);
String request, response;

// 处理循环开始
while ((request = in.readLine()) != null) {
    // 如果客户端发送了 "Done" 则退出处理循环
    if ("Done".equals(request)) {
        break;
    };

    // 请求被传递给服务器的处理方法
    response = processRequest(request);
    // 服务器的响应被发送给客户端
    out.println(response);
}
```

上面的代码片段在同一时间内只能处理一个连接, 要处理多个并发的客户端, 一种方案是为每个新的客户端 Socket 创建一个新的 Thread, 但是这种方案占用系统资源较多.

![Blocking_IO](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_1_1_Blocking_IO.jpg)

### 1.1.1 Java NIO

### 1.1.2 选择器

![selector](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_1_2_Nonblocking_IO.jpg)

<br>

java.nio.channels.Selector 是 Java 的非阻塞 I/O 实现的关键. 它使用了事件通知 API 以确定在一组非阻塞套接字中有哪些已经就绪能够进行 I/O 相关的操作. 因为可以在任何的时间检查任意的读操作或者写操作的完成状态, 所以一个单一的线程便可以处理多个并发的连接.

总体来看, 与阻塞 I/O 模型相比, 这种模型提供了更好的资源管理:
* 使用较少的线程便可以处理许多连接, 因此也减少了内存管理和上下文切换所带来开销;
* 当没有 I/O 操作需要处理的时候, 线程也可以被用于其他任务.

| 分类 | Netty 的特性 |
| ------| ------ |
| 设计 | 统一的 API, 支持多种传输类型, 包括阻塞和非阻塞 <br> 简单而强大的线程模型 <br> 真正的无连接数据报套接字支持 <br> 链接逻辑组件以支持复用 |
| 性能 | **拥有比 Java 的核心 API 更高的吞吐量以及更低的延迟** <br> **得益于池化和复用, 拥有更低的资源消耗** <br> **最少的内存复制** |
| 健壮性 | 不会因为慢速、快速或者超载的连接而导致 OutOfMemoryError 异常 <br> 消除在高速网络中 NIO 应用程序常见的不公平读/写比率 |
| 安全性 | 完整的 SSL/TLS 以及 StartTLS 支持 <br> 可用于受限环境下，如 Applet 和 OSGI |

### 1.2.2 异步和事件驱动

异步和可伸缩性之间的联系又是什么呢?
* 非阻塞网络调用使得我们可以不必等待一个操作的完成. 完全异步的 I/O 正是基于这个特性构建的, 并且更进一步: 异步方法调用会立即返回, 并且在它完成时, 会直接或者在稍后的某个时间点通知用户.
* 选择器使得我们能够通过较少的线程便可监视许多连接上的事件.

<font color=#fd0209 size=6 >问题: 这里提到的事件是谁发出的, socket 由谁来监视?</font>

## 1.3 异步和事件驱动

Netty 的核心组件:
* Channel
* 回调
* Future
* 事件和 ChannelHandler

### 1.3.1 Channel

它是一个抽象概念, 代表一个到实体(如一个硬件设备, 一个文件, 一个网络套接字或者一个能够执行一个或者多个不同的 I/O 操作的程序组件)的开放连接, 因此, 它可以被打开或者被关闭, 连接或者断开连接.

### 1.3.2 回调

Netty 在内部使用了回调来处理事件; 当一个回调被触发时, 相关的事件可以被一个 interfaceChannelHandler 的实现处理.

### 1.3.3 Future

Future 提供了另一种在操作完成时通知应用程序的方式. 这个对象可以看作是一个异步操作的结果的**占位符**; 它将在未来的某个时刻完成, 并提供对其结果的访问.
Netty 的 Future 实现支持对 Future 注册 listener.
事实上, 回调和 Future 是相互补充的机制.

### 1.3.4 事件和 ChannelHandler
每个 ChannelHandler 的实例都类似于一种为了响应特定事件而被执行的回调

![Event_Flow](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_1_3_Event_Flow.jpg)

### 1.3.5 把它们放在一起

**Future, 回调和 ChannelHandler**
Netty 的异步编程模型是建立在 Future 和回调的概念之上的, 而将事件派发到 ChannelHandler 的方法则发生在更深的层次上(操作系统层面?). 
拦截操作以及高速地转换入站数据和出站数据, 都只需要你提供回调或者利用操作所返回的 Future. 这使得链接操作变得既简单又高效, 并且促进了可重用的通用代码的编写.

<font color=#fd0209 size=6 >问题: 什么场景下只提供回调, 什么场景下利用操作所返回的 Future?</font>

**选择器, 事件和 EventLoop**
Netty 通过触发事件将 Selector 从应用程序中抽象出来, 消除了所有本来将需要手动编写的派发代码. 在内部, 将会为每个 Channel 分配一个 EventLoop, 用以处理所有事件, 包括:
- 注册感兴趣的事件;
- 将事件派发给 ChannelHandler;
- 安排进一步的动作.

EventLoop 本身只由一个线程驱动, 其处理了一个 Channel 的所有 I/O 事件, 并且在该 EventLoop 的整个生命周期内都不会改变. 这个简单而强大的设计消除了你可能有的在 ChannelHandler 实现中需要进行同步的任何顾虑, 因此, 你可以专注于提供正确的逻辑.

<font color=#fd0209 size=6 >问题: Channel 可以共用 EventLoop 吗? <br> 答: 可以</font>

# 2 你的第一款 Netty 应用程序

* Channel-andler 可以被多个 Channel 安全地共享
* 每个 Channel 都拥有一个与之相关联的 ChannelPipeline, 其持有一个 ChannelHandler 的实例链. 因此, 如果 exceptionCaught() 方法没有被该链中的某处实现, 那么所接收的异常将会被传递到 ChannelPipeline 的尾端并被记录. 为此, 你的应用程序应该提供至少有一个实现了 exceptionCaught() 方法的 ChannelHandle.
* 在架构上, ChannelHandler 有助于保持业务逻辑与网络处理代码的分离. 这简化了开发过程, 因为代码必须不断地演化以响应不断变化的需求.

# 3 Netty 的组件和设计

## 3.1 Channel、EventLoop 和 ChannelFuture

* Channel — Socket
* EventLoop — 控制流、多线程处理、并发
* ChannelFuture — 异步通知

### 3.1.1 Channel 接口

* EmbeddedChannel
* LocalServerChannel
* NioDatagramChannel
* NioSctpChannel
* NioSocketChannel

### 3.1.2 EventLoop 接口

![EventLoop](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_3_1.jpg)

![EventLoopGroups](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_3_2_Server_with_two_EventLoopGroups.jpg)

<p align="center"><font size=2>Channel, EventLoop 和 EventLoopGroup 的关系</font></p>


* 一个 EventLoopGroup 包含一个或者多个 EventLoop
* **一个 EventLoop 在它的生命周期内只和一个 Thread 绑定**
* 所有由 EventLoop 处理的 I/O 事件都将在它专有的 Thread 上被处理
* 一个 Channel 在它的生命周期内只注册于一个 EventLoop
* 一个 EventLoop 可能会被分配给一个或多个 Channel

**注意, 在这种设计中，一个给定 Channel 的 I/O 操作都是由相同的 Thread 执行的, 实际上消除了对于同步的需要**

### 3.1.3 ChannelFuture 接口

## 3.2 ChannelHandler 和 ChannelPipeline

### 3.2.1 ChannelHandler 接口

### 3.2.2 ChannelPipeline 接口

ChannelPipeline 提供了 ChannelHandler 链的容器, 并定义了用于在该链上传播入站和出站事件流的 API. 当 Channel 被创建时, 它会被自动地分配到它专属的 ChannelPipeline.

ChannelHandler 安装到 ChannelPipeline 中的过程如下所示:
* 一个 ChannelInitializer 的实现被注册到了 ServerBootstrap 中
* 当 ChannelInitializer.initChannel() 方法被调用时, ChannelInitializer 将在 ChannelPipeline 中安装一组自定义的 ChannelHandler
* ChannelInitializer 将它自己从 ChannelPipeline 中移除

ChannelHandler 是专为支持广泛的用途而设计的, 可以将它看作是处理往来 ChannelPipeline 事件 (包括数据)的任何代码的通用容器.

使得事件流经 ChannelPipeline 是 ChannelHandler 的工作, 它们是在应用程序的初始化或者引导阶段被安装的. 这些对象接收事件, 执行它们所实现的处理逻辑, 并将数据传递给链中的下一个 ChannelHandler. 它们的执行顺序是由它们被添加的顺序所决定的.

![](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_3_4_ChannelPipeline_with_inbound_and_outbound_ChannelHandlers.jpg)

<p align="center"><font size=2>包含入站和出站 ChannelHandler 的 ChannelPipeline</font></p>

上图也显示了入站和出站 ChannelHandler 可以被安装到同一个 ChannelPipeline 中. 如果一个消息或者任何其他的入站事件被读取, 那么它会从 ChannelPipeline 的头部开始流动, 并被传递给第一个 ChannelInboundHandler. 这个 ChannelHandler 不一定会实际地修改数据, 具体取决于它的具体功能, 在这之后，数据将会被传递给链中的下一个 ChannelInboundHandler. 最终, 数据将会到达 ChannelPipeline 的尾端, 此时所有处理就结束了.

数据的出站运动(即正在被写的数据)在概念上也是一样的. 在这种情况下, 数据将从 ChannelOutboundHandler 链的尾端开始流动, 直到它到达链的头部为止. 在这之后, 出站数据将会到达网络传输层, 这里显示为 Socket. 通常情况下, 这将触发一个写操作.

通过使用作为参数传递到每个方法的 ChannelHandlerContext(在 ChannelHandler 之间传递事件), 事件可以被传递给当前 ChannelHandler 链中的下一个 ChannelHandler. 因为你有时会忽略那些不感兴趣的事件, 所以 Netty 提供了抽象基类 ChannelInboundHandlerAdapter 和 ChannelOutboundHandlerAdapter. 通过调用 ChannelHandlerContext 上的对应方法, 每个都提供了简单地将事件传递给下一个ChannelHandler 的方法的实现.

当 ChannelHandler 被添加到 ChannelPipeline 时, 它将会被分配一个 ChannelHandlerContext, 其代表了 ChannelHandler 和 ChannelPipeline 之间的绑定. ChannelHandlerContext 的主要功能是管理通过同一个 ChannelPipeline 关联的 ChannelHandler 之间的交互.

ChannelHandlerContext 有许多方法, 其中一些也出现在 Channel 和ChannelPipeline 本身. 然而,如果您通过 Channel 或 ChannelPipeline 的实例来调用这些方法, 他们就会在整个 pipeline 中传播. 相比之下, 一样的方法在 ChannelHandlerContext 的实例上调用, 就只会从当前的 ChannelHandler 开始并传播到相关管道中的下一个有处理事件能力的 ChannelHandler.

类似 ChannelInboundHandlerAdapter 的适配器提供了大量默认的 ChannelHandler 实现, 其旨在简化应用程序处理逻辑的开发过程, 你只需要重写那些你想要特殊处理的方法和事件.

接下来我们将研究 3 个 ChannelHandler 的子类型: 编码器, 解码器和 SimpleChannelInboundHandler<T> —— ChannelInboundHandlerAdapter 的一个子类.

 >                                                 I/O Request
>                                       via Channel or ChannelHandlerContext
 >                                                      |
 >  +---------------------------------------------------+---------------+
 >  |                           ChannelPipeline         |               |
 >  |                                                  \|/              |
 >  |    +---------------------+            +-----------+----------+    |
 >  |    | Inbound Handler  N  |            | Outbound Handler  1  |    |
 >  |    +----------+----------+            +-----------+----------+    |
 >  |              /|\                                  |               |
 >  |               |                                  \|/              |
 >  |    +----------+----------+            +-----------+----------+    |
 >  |    | Inbound Handler N-1 |            | Outbound Handler  2  |    |
 >  |    +----------+----------+            +-----------+----------+    |
 >  |              /|\                                  .               |
 >  |               .                                   .               |
 >  | ChannelHandlerContext.fireIN_EVT() ChannelHandlerContext.OUT_EVT()|
 >  |        [ method call]                       [method call]         |
 >  |               .                                   .               |
 >  |               .                                  \|/              |
 >  |    +----------+----------+            +-----------+----------+    |
 >  |    | Inbound Handler  2  |            | Outbound Handler M-1 |    |
 >  |    +----------+----------+            +-----------+----------+    |
 >  |              /|\                                  |               |
 >  |               |                                  \|/              |
 >  |    +----------+----------+            +-----------+----------+    |
 >  |    | Inbound Handler  1  |            | Outbound Handler  M  |    |
 >  |    +----------+----------+            +-----------+----------+    |
 >  |              /|\                                  |               |
 >  +---------------+-----------------------------------+---------------+
 >                  |                                  \|/
 >  +---------------+-----------------------------------+---------------+
 >  |               |                                   |               |
 *> |       [ Socket.read() ]                    [ Socket.write() ]     |
 >  |                                                                   |
 >  |  Netty Internal I/O Threads (Transport Implementation)            |
 >  +-------------------------------------------------------------------+


### 3.2.4 编码器和解码器




