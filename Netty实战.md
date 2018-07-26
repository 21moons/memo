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

* 注册感兴趣的事件;
* 将事件派发给 ChannelHandler;
* 安排进一步的动作.

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

>                                                  I/O Request
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
>  |       [ Socket.read() ]                    [ Socket.write() ]     |
>  |                                                                   |
>  |  Netty Internal I/O Threads (Transport Implementation)            |
>  +-------------------------------------------------------------------+


### 3.2.4 编码器和解码器

###3.2.5 抽象类 SimpleChannelInboundHandler

最常见的情况是, 你的应用程序会利用一个 ChannelHandler 来接收解码消息, 并对该数据应用业务逻辑. 要创建一个这样的 ChannelHandler, 你只需要扩展基类 SimpleChannelInboundHandler<T>, 其中 T 是你要处理的消息的 Java 类型. 在这个 ChannelHandler 中, 你需要重写基类的一个或者多个方法，并且获取一个到 ChannelHandlerContext 的引用, 这个引用将作为输入参数传递给 ChannelHandler 的所有方法.
在这种类型的 ChannelHandler 中, 最重要的方法是 channelRead0(ChannelHandlerContext,T). 除了要求不要阻塞当前的 I/O 线程之外，其具体实现完全取决于你.

## 3.3 引导(Bootstrap)

有两种类型的引导: 一种用于客户端(简单地称为 Bootstrap), 而另一种
(ServerBootstrap)用于服务器.

问什么引导一个客户端只需要一个 EventLoopGroup, 但是 ServerBootstrap 则需要两个(也可以是同一个实例)?

因为服务器需要两组不同的 Channel. 第一组将只包含一个 ServerChannel, 代表已绑定到某个本地端口的, 正在监听的套接字. 而第二组用来处理所有已创建连接上的事件. 

![Server_with_two_EventLoopGroups](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_3_2_Server_with_two_EventLoopGroups.jpg)

# 4 传输

OIO: 阻塞传输
NIO: 异步传输

## 4.1 案例研究: 传输迁移

### 4.1.2 阻塞的 Netty 版本

``` java
public class NettyOioServer {
    public void server(int port) throws Exception {
    final ByteBuf buf = Unpooled.unreleasableBuffer(Unpooled.copiedBuffer("Hi!\r\n", Charset.forName("UTF-8")));
    EventLoopGroup group = new OioEventLoopGroup();

    try {
        ServerBootstrap b = new ServerBootstrap();
        b.group(group)
            // 使用 OioEventLoopGroup 以允许阻塞模式
            .channel(OioServerSocketChannel.class)
            .localAddress(new InetSocketAddress(port))
            // 指定 ChannelInitializer, 对于每个已接受的连接都调用它
            .childHandler(new ChannelInitializer<SocketChannel>() {
                @Override
                public void initChannel(SocketChannel ch)
                    throws Exception {
                    ch.pipeline().addLast(
                        new ChannelInboundHandlerAdapter() {
                            @Override
                            public void channelActive(ChannelHandlerContext ctx)
                                throws Exception {
                                // 将消息写到客户端, 并添加 ChannelFutureListener,
                                // 以便消息一被写完就关闭连接
                                ctx.writeAndFlush(buf.duplicate())
                                    .addListener(ChannelFutureListener.CLOSE);
                            }
                        }
                    );
                }
            });
        // 绑定服务器以接受连接
        ChannelFuture f = b.bind().sync();
        f.channel().closeFuture().sync();
    } finally {
        group.shutdownGracefully().sync();
    }
    }
}
```

### 4.1.3 非阻塞的 Netty 版本

``` java
public class NettyOioServer {
    public void server(int port) throws Exception {
    final ByteBuf buf = Unpooled.unreleasableBuffer(Unpooled.copiedBuffer("Hi!\r\n", Charset.forName("UTF-8")));
    EventLoopGroup group = new NioEventLoopGroup();

    try {
        ServerBootstrap b = new ServerBootstrap();
        b.group(group)
            // 使用 OioEventLoopGroup 以允许阻塞模式
            .channel(NioServerSocketChannel.class)
            .localAddress(new InetSocketAddress(port))
            // 指定 ChannelInitializer, 对于每个已接受的连接都调用它
            .childHandler(new ChannelInitializer<SocketChannel>() {
                @Override
                public void initChannel(SocketChannel ch)
                    throws Exception {
                    ch.pipeline().addLast(
                        new ChannelInboundHandlerAdapter() {
                            @Override
                            public void channelActive(ChannelHandlerContext ctx)
                                throws Exception {
                                // 将消息写到客户端, 并添加 ChannelFutureListener,
                                // 以便消息一被写完就关闭连接
                                ctx.writeAndFlush(buf.duplicate())
                                    .addListener(ChannelFutureListener.CLOSE);
                            }
                        }
                    );
                }
            });
        // 绑定服务器以接受连接
        ChannelFuture f = b.bind().sync();
        f.channel().closeFuture().sync();
    } finally {
        group.shutdownGracefully().sync();
    }
    }
}
```

## 4.2 传输 API

![Channel](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_4.1_Channel_interface_hierarchy.jpg)

ChannelPipeline 持有所有将应用于入站和出站数据以及事件的 ChannelHandler 实例, 这些 ChannelHandler 实现了应用程序用于处理状态变化以及数据处理的逻辑. ChannelHandler 的典型用途包括:
* 将数据从一种格式转换为另一种格式;
* 提供异常的通知;
* 提供 Channel 变为活动的或者非活动的通知;
* 提供当 Channel 注册到 EventLoop 或者从 EventLoop 注销时的通知;
* 提供有关用户自定义事件的通知.

ChannelPipeline 实现了一种常见的设计模式--拦截过滤器(Intercepting Filter)

Netty 的 Channel 实现是线程安全的

### 4.3.1 NIO —— 非阻塞 I/O

选择器背后的基本概念是充当一个注册表, 在那里你可以在 Channel 的状态发生变化时得到通知. 可能的状态变化有:

* 新的 Channel 已被接受并且就绪;
* Channel 连接已经完成;
* Channel 有已经就绪的可供读取的数据;
* Channel 可用于写数据.

选择器运行在一个线程上, 检查所有 Channel 的状态变化并做出响应, 在应用程序对状态的改变做出响应之后, 选择器将会被重置, 然后重复这个过程。

![Selecting_and_Processing_State_Changes](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_4.2_Selecting_and_Processing_State_Changes.jpg)

<font color=#fd0209 size=6 >问题: select() 函数是主动的扫描 channel 上的事件, 还是被动的等待操作系统通知?</font>

>零拷贝
>零拷贝(zero-copy)是一种目前只有在使用 NIO 和 Epoll 传输时才可使用的特性. 它使你可以快速>高效地将数据从文件系统移动到网络接口, 而不需要将其从内核空间复制到用户空间, 其在像 FTP 或>者 HTTP 这样的协议中可以显著地提升性能. 但是, 并不是所有的操作系统都支持这一特性. 特别
>地, 它对于实现了数据加密或者压缩的文件系统是不可用的——只能传输文件的原始内容.

### 4.3.2 Epoll — 用于 Linux 的本地非阻塞传输

Netty 为 Linux 提供了一组 NIO API, 其以一种和它本身的设计更加一致的方式使用 epoll, 并且以一种更加轻量的方式使用中断. JDK 的实现是水平触发, 而 Netty 的(默认的)是边沿触发.

### 4.3.3 OIO — 旧的阻塞 I/O

Netty 是如何能够使用和用于异步传输相同的 API 来支持 OIO 的呢?
答案就是 Netty 利用了 SO_TIMEOUT 这个 Socket 标志, 它指定了等待一个 I/O 操作完成的最大毫秒数. 如果操作在指定的时间间隔内没有完成, 则将会抛出一个 SocketTimeout Exception. Netty 将捕获这个异常并继续处理循环. 在 EventLoop 下一次运行时, 它将再次尝试. 这实际上也是类似 Netty 这样的异步框架能够支持 OIO 的唯一方式.

![OIO-Processing_logic](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_4.3_OIO-Processing_logic.jpg)

### 4.3.4 用于 JVM 内部通信的 Local 传输

Netty 提供了一个 Local 传输, 用于在同一个 JVM 中运行的客户端和服务器程序之间的异步通信. 同样, 这个传输也支持对于所有 Netty 传输实现都共同的 API.

### 4.3.5 Embedded 传输(测试你的 ChannelHandler 实现)

Netty 提供了一种额外的传输, 使得你可以将一组 ChannelHandler 作为帮助器类嵌入到其他的 ChannelHandler 内部. 通过这种方式, 你将可以扩展一个 ChannelHandler 的功能, 而又不需要修改其内部代码.

# 5 ByteBuf

Java NIO 提供了 ByteBuffer 作为字节容器, 但是这个类使用起来过于复杂, 而且也有些繁琐.
Netty 提供了 ByteBuffer 的替代品 ByteBuf.

## 5.1 ByteBuf 的 API

Netty 的数据处理 API 通过两个组件暴露 -- abstract class ByteBuf 和 interface ByteBufHolder

ByteBuf API 的优点:
* 通过内置的复合缓冲区类型实现了透明的零拷贝
* 容量可以按需增长
* 在读和写这两种模式之间切换不需要调用 ByteBuffer 的 flip() 方法
* 读和写使用了不同的索引
* 支持方法的链式调用
* 支持引用计数
* 支持池化

<font color=#fd0209 size=6 >问题: 什么是支持池化?</font>

## 5.2 ByteBuf 类 -- Netty 的数据容器

ByteBuf 维护了两个不同的索引: 一个用于读取, 一个用于写入. 当你从 ByteBuf 读取时, readerIndex 将会递增读取的字节数. 同样地, 当你写入 ByteBuf 时, writerIndex 也会递增.

### 5.2.2 ByteBuf 的使用模式

#### 堆缓冲区
最常用的 ByteBuf 模式是将数据存储在 JVM 的堆空间中. 这是通过将数据存储在数组来实现的, 这种模式被称为支持数组(backing array), 它能在没有使用池化的情况下提供快速的分配和释放. 
堆缓冲区可以快速分配, 当不使用时也可以快速释放. 它还提供了直接访问数组的方法, 通过 ByteBuf.array() 来获取 byte[] 中保存的数据.
这种方式, 如代码清单 5-1 所示, 非常适合于有遗留的数据需要处理的情况.

<p align="center"><font size=2>代码清单 5-1 支持数组</font></p>

``` java
ByteBuf heapBuf = ...;
// 检查 ByteBuf 是否有支持数组
if (heapBuf.hasArray()) {
    // 如果有, 则获取对该数组的引用
    byte[] array = heapBuf.array();
    // 计算第一个字节的偏移量
    int offset = heapBuf.arrayOffset() + heapBuf.readerIndex();
    // 获得可读字节数
    int length = heapBuf.readableBytes();
    // 使用数组、偏移量和长度作为参数调用方法
    handleArray(array, offset, length);
}
```

注意:

* 访问非堆缓冲区 ByteBuf 的数组会导致 UnsupportedOperationException, 可以使用 ByteBuf.hasArray() 来检查是否支持访问数组。
* 这个用法与 JDK 的 ByteBuffer 类似

#### 直接缓冲区

在 JDK1.4 中被引入 NIO 的ByteBuffer 类允许 JVM 通过本地方法调用分配内存, 其目的是:

* 通过免去中间交换的内存拷贝, 提升IO处理速度;
* DirectBuffer 在 -XX:MaxDirectMemorySize=xxM 大小限制下, 使用 Heap 之外的内存, GC对此”无能为力”, 也就意味着规避了高负载下频繁的 GC 过程对应用线程的中断影响.

直接缓冲区的内容可以驻留在垃圾回收扫描的堆区以外.

这就解释了为什么 "直接缓冲区" 对于那些通过 socket 实现数据传输的应用来说, 是一种非常理想的方式. 如果你的数据是存放在堆中分配的缓冲区, 那么在通过 socket 发送数据之前, JVM 要先将数据复制到直接缓冲区.

但是直接缓冲区的缺点是在内存空间的分配和释放上比堆缓冲区更复杂, 另外一个缺点是如果要将数据传递给遗留代码处理, 因为数据不是在堆上, 你可能不得不进行一次拷贝, 如下：

<p align="center"><font size=2>代码清单 5-2 访问直接缓冲区的数据</font></p>

``` java
ByteBuf directBuf = ...;
// 检查 ByteBuf 是否有支持数组, 如果没有则是一个直接缓冲区
if (!directBuf.hasArray()) {
    // 获取可读字节数
    int length = directBuf.readableBytes();
    // 分配一个新的数组来保存具有该长度的字节数据
    byte[] array = new byte[length];
    // 将字节复制到数组
    directBuf.getBytes(directBuf.readerIndex(), array);
    // 使用数组、偏移量和长度作为参数调用处理方法
    handleArray(array, 0, length);
}
```

#### 复合缓冲区(COMPOSITE BUFFER)

最后一种模式是复合缓冲区, 我们可以创建多个不同的 ByteBuf, 然后提供一个这些 ByteBuf 组合的视图. 复合缓冲区就像一个列表, 我们可以动态的添加和删除其中的 ByteBuf, JDK 的 ByteBuffer 没有这样的功能.

Netty 提供了 ByteBuf 的子类 CompositeByteBuf 类来处理复合缓冲区, CompositeByteBuf 只是一个视图.

*警告*
*如果 CompositeByteBuf 既包含堆缓冲区, 也包含直接缓冲区, CompositeByteBuf.hasArray() 返回 false*

例如一条消息由 header 和 body 两部分组成, 将 header 和 body 组装成一条消息发送出去, 可能 body 相同, 只是 header 不同, 使用CompositeByteBuf 就不用每次都重新分配一个新的缓冲区. 下图显示 CompositeByteBuf 组成 header 和 body:

![CompositeByteBuf](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_5.2_CompositeByteBuf_holding_a_header_and_body.jpg)

<p align="center"><font size=2>代码清单 5-3 使用 ByteBuffer 的复合缓冲区模式</font></p>

``` java
// Use an array to hold the message parts
ByteBuffer[] message = new ByteBuffer[] { header, body };
// Create a new ByteBuffer and use copy to merge the header and body
ByteBuffer message2 =
ByteBuffer.allocate(header.remaining() + body.remaining());
message2.put(header);
message2.put(body);
message2.flip();
```

<p align="center"><font size=2>代码清单 5-4 使用 CompositeByteBuf 的复合缓冲区模式</font></p>

``` java
CompositeByteBuf messageBuf = Unpooled.compositeBuffer();
ByteBuf headerBuf = ...; // can be backing or direct
ByteBuf bodyBuf = ...; // can be backing or direct
// 将 ByteBuf 实例追加到 CompositeByteBuf
messageBuf.addComponents(headerBuf, bodyBuf);
.....
// 删除位于索引位置为 0 (第一个组件) 的 ByteBuf
messageBuf.removeComponent(0); // remove the header
// 循环遍历所有的 ByteBuf 实例
for (ByteBuf buf : messageBuf) {
    System.out.println(buf.toString());
}
```

CompositeByteBuf 不允许访问其内部可能存在的支持数组, 也不允许直接访问数据, 这一点类似于直接缓冲区模式.

<p align="center"><font size=2>代码清单 5-5 访问 CompositeByteBuf 中的数据</font></p>

``` java
CompositeByteBuf compBuf = Unpooled.compositeBuffer();
// 获得可读字节数
int length = compBuf.readableBytes();
// 分配一个具有可读字节数长度的新数组
byte[] array = new byte[length];
// 将字节读到该数组中
compBuf.getBytes(compBuf.readerIndex(), array);
// 使用偏移量和长度作为参数使用该数组
handleArray(array, 0, array.length);
```

Netty 尝试使用 CompositeByteBuf 优化 socket I/O 操作, 消除原生 JDK 中可能存在的的性能低和内存消耗问题. 虽然这是在 Netty 的核心代码中进行的优化, 并且是不对外暴露的, 但是作为开发者还是应该意识到其影响.

## 5.3 字节级操作

除了基本的读写操作, ByteBuf 还提供了它所包含的数据的修改方法.

### 5.3.1 随机访问索引

ByteBuf 使用 zero-based 的 indexing(从0开始的索引), 第一个字节的索引是 0, 最后一个字节的索引是 ByteBuf 的 capacity - 1, 下面代码是遍历 ByteBuf 的所有字节:

<p align="center"><font size=2>代码清单 5-6 Access data</font></p>

``` java
    ByteBuf buffer = ...;
    for (int i = 0; i < buffer.capacity(); i++) {
        byte b = buffer.getByte(i);
        System.out.println((char) b);
    }
```

注意通过索引访问时不会推进 readerIndex (读索引)和 writerIndex(写索引), 我们可以通过 ByteBuf 的 readerIndex(index) 或 writerIndex(index) 来分别偏移读索引或写索引.

### 5.3.2 顺序访问索引

虽然 ByteBuf 同时具有读索引和写索引, 但是 JDK 的 ByteBuffer 却只有一个索引, 这就是为什么 ByteBuffer 必须调用 flip() 方法在读模式和写模式之间进行切换. 下图展示了 ByteBuf 是如何被它的两个索引划分成 3 个区域的.

![ByteBuf_internal_segmentation](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_5.3_ByteBuf_internal_segmentation.jpg)

### 5.3.3 可丢弃字节

在图 5-3 中标记为可丢弃字节的分段包含了已经被读过的字节. 通过调用  discardReadBytes()方法, 可以丢弃它们并回收空间. 这个分段的初始大小为 0, 存储在 readerIndex 中, 会随着 read 操作的执行而增加.

图 5-4 展示了在图 5-3 中所展示的缓冲区上调用 discardReadBytes() 函数后的结果. 可以看到, 可丢弃字节分段中的空间已经变为可写的了. 注意, 在调用 discardReadBytes() 之后, 对可写分段的内容并没有任何的保证(只移动了索引, 而没有对所有可写入的字节进行擦除写).

![ByteBuf_after_discarding_read_bytes](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_5.4_ByteBuf_after_discarding_read_bytes.jpg)


虽然你可能会倾向于频繁地调用 discardReadBytes() 方法以确保可写分段的最大化, 但是请注意, 这将极有可能会导致内存复制, 因为可读字节(图中标记为 CONTENT 的部分)必须被移动到缓冲区的开始位置. 我们建议只在有真正需要的时候才这样做, 例如, 当内存非常宝贵的时候.

### 5.3.4 可读字节

ByteBuf 的可读字节分段存储了实际数据. 新分配的, 包装的或者复制的缓冲区的默认的 readerIndex 值为 0. 任何名称以 read 或者 skip 开头的操作都将检索或者跳过位于当前 readerIndex 前面的数据, 并且将它增加已读字节数.

如果所谓的读操作是一个指定 ByteBuf 参数作为写入的对象, 并且没有一个目标索引参数, 目标缓冲区的 writerIndex 也会增加. 例如:

``` java
    readBytes(ByteBuf dest);
```

如果试图从可读字节数已经用尽的缓冲器读取字节, 则抛出 IndexOutOfBoundsException.

``` java
    //遍历缓冲区的可读字节
    ByteBuf buffer= ...;
    while (buffer.isReadable()) {
        System.out.println(buffer.readByte());
    }
```

### 5.3.5 可写字节

可写字节分段是指一个拥有未定义内容的, 写入就绪的内存区域. 新分配的缓冲区的 writerIndex 的默认值为 0. 任何名称以 write 开头的操作都将从当前的 writerIndex 处开始写数据, 并将它递增已经写入的字节数. 如果写操作的目标也是 ByteBuf, 并且没有指定源索引, 则源缓冲区的 readerIndex 也同样会被增加相同的大小. 这个调用如下所示:

``` java
    writeBytes(ByteBuf dest);
```

如果试图写入超出目标的容量, 则抛出 IndexOutOfBoundException.

代码清单 5-8 是一个用随机整数值填充缓冲区, 直到它空间不足为止的例子. writeableBytes() 方法在这里被用来确定该缓冲区中是否还有足够的空间.

``` java
    //填充随机整数到缓冲区中
    ByteBuf buffer = ...;
    while (buffer.writableBytes() >= 4) {
        buffer.writeInt(random.nextInt());
    }
```

### 5.3.6 索引管理

JDK 的 InputStream 定义了 mark(int readlimit) 和 reset() 方法. 这些是分别用来标记流中的当前位置和复位流到该位置. 同样, 可以通过调用 markReaderIndex(), markWriterIndex(), resetWriterIndex() 和 resetReaderIndex() 来标记和重置 ByteBuf 的 readerIndex 和 writerIndex.

也可以通过调用 readerIndex(int) 或者 writerIndex(int) 来将索引移动到指定位置. 试图将任何一个索引设置到一个无效的位置都将导致一个 IndexOutOfBoundsException.

可以通过调用 clear() 方法来将 readerIndex 和 writerIndex 都设置为 0. 注意, 这并不会清除内存中的内容.图 5-5(重复上面的图 5-3)展示了它是如何工作的。

![Before_clear_is_called](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_5.5_Before_clear_is_called.jpg)

![After_clear_is_called](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_5.6_After_clear_is_called.jpg)

调用 clear()比调用 discardReadBytes() 轻量得多, 因为它将只是重置索引而不会复制任何的内存.

### 5.3.7 查找操作

有几种方法可以在缓冲区中查找指定值的索引. 最简单的是使用 indexOf() 方法. 更复杂的搜索方法使用 ByteBufProcessor 作为参数. 这个接口定义了一个方法, boolean process(byte value), 它将检查输入值 value 是否是指定值.

代码清单 5-9 展示了一个查找回车符 (\r) 的例子.

``` java
    ByteBuf buffer = ...;
    int index = buffer.forEachByte(ByteBufProcessor.FIND_CR);
```

### 5.3.8 派生缓冲区

派生缓冲区为 ByteBuf 提供了以专门的方式来呈现其内容的视图. 这类视图是通过以下方法被创建的:

* duplicate()
* slice()
* slice(int, int)
* Unpooled.unmodifiableBuffer(…)
* order(ByteOrder)
* readSlice(int)

每个这些方法都将返回一个新的 ByteBuf 实例, 它具有自己的读索引, 写索引和标记索引. 其内部存储和 JDK 的 ByteBuffer 一样也是共享的. 这使得派生缓冲区的创建成本很低廉, 但是这也意味着, 如果你修改了它的内容, 也同时修改了其对应的源实例, 所以要小心.

**ByteBuf 复制** 如果需要一个现有缓冲区的真实副本, 请使用 copy() 或者 copy(int, int) 方法. 不同于派生缓冲区, 由这个调用所返回的 ByteBuf 拥有独立的数据副本.

<p align="center"><font size=2>代码清单 5-10 对 ByteBuf 进行切片</font></p>

``` java
    Charset utf8 = Charset.forName("UTF-8");
    // 创建一个用于保存给定字符串的 ByteBuf
    ByteBuf buf = Unpooled.copiedBuffer("Netty in Action rocks!", utf8);
    // 基于该 ByteBuf 从索引 0 到索引 15 的部分创建一个切片
    ByteBuf sliced = buf.slice(0, 14);
    // 将打印 "Netty in Action"
    System.out.println(sliced.toString(utf8));
    // 更新索引 0 处的字节
    buf.setByte(0, (byte) 'J');
    // 将会成功, 因为数据是共享的, 对其中一个所做的更改对另外一个也是可见的
    assert buf.getByte(0) == sliced.getByte(0);
```

<p align="center"><font size=2>代码清单 5-11 复制一个 ByteBuf</font></p>

``` java
    Charset utf8 = Charset.forName("UTF-8");
    // 创建一个用于保存给定字符串的 ByteBuf
    ByteBuf buf = Unpooled.copiedBuffer("Netty in Action rocks!", utf8);
    // 基于该 ByteBuf 从索引 0 到索引 15 的部分创建一个副本
    ByteBuf copy = buf.copy(0, 15);
    // 将打印 "Netty in Action"
    System.out.println(copy.toString(utf8));
    // 更新索引 0 处的字节
    buf.setByte(0, (byte) 'J');
    // 将会成功, 因为数据不是共享的
    assert buf.getByte(0) != copy.getByte(0);
```

### 5.3.9 读/写操作

有两种类别的读/写操作:

* get() 和 set() 操作, 从给定的索引开始, 并且保持索引不变;
* read() 和 write() 操作, 从给定的索引开始, 并且会根据已经访问过的字节数递增当前的写索引或读索引.

### 5.3.10 更多的操作

方法名称     | 描述
-------- | ---
isReadable() | Returns true if at least one byte can be read.
isWritable() | Returns true if at least one byte can be written.
readableBytes() | Returns the number of bytes that can be read.
writablesBytes() | Returns the number of bytes that can be written.
capacity() | Returns the number of bytes that the ByteBuf can hold. After this it will try to expand again until maxCapacity() is reached.
maxCapacity() | Returns the maximum number of bytes the ByteBuf can hold.
hasArray() | Returns true if the ByteBuf is backed by a byte array.
array() | Returns the byte array if the ByteBuf is backed by a byte array, otherwise throws an UnsupportedOperationException.

## 5.4 ByteBufHolder 接口

我们经常发现, 除了实际的数据负载之外, 我们还需要存储各种属性值. HTTP 响应便是一个很好的例子, 除了表示为字节的内容, 还包括状态码, cookie 等.

Netty 提供 ByteBufHolder 处理这种常见的情况. ByteBufHolder 还提供对于 Netty 的高级功能, 如缓冲池, 其中保存实际数据的 ByteBuf 可以从池中借用, 如果需要还可以自动释放.

ByteBufHolder 只有几种用于访问底层数据和引用计数的方法. 表 5-6 列出了它们(这里不包括它继承自 ReferenceCounted 的那些方法).

public interface ByteBufHolder extends ReferenceCounted

Table 5.7 ByteBufHolder operations

| 名称     | 描述 |
| -------- | --- |
| content() | 返回由这个 ByteBufHolder 所持有的 ByteBuf |
| copy() | 返回这个 ByteBufHolder 的一个深拷贝, 包括一个其所包含的 ByteBuf 的非共享拷贝 |
| duplicate() | 返回这个 ByteBufHolder 的一个浅拷贝, 包括一个其所包含的 ByteBuf 的共享拷贝 |
| replace(ByteBuf content) | 返回包含指定内容的新 ByteBufHolder |

<font color=#fd0209 size=6 >问题: 为什么需要 ByteBufHolder?</font>

## 5.5 ByteBuf 分配

### 5.5.1 按需分配: ByteBufAllocator 接口

为了降低分配和释放内存的开销, Netty 通过 interface ByteBufAllocator 实现了ByteBuf 的池化, 它可以用来分配我们所描述过的任意类型的 ByteBuf 实例.

名称     | 描述
-------- | ---
buffer() <br> buffer(int initialCapacity) <br> buffer(int initialCapacity, int maxCapacity) | 返回一个基于堆或者直接内存存储的 ByteBuf.
heapBuffer() <br> heapBuffer(int initialCapacity) <br> heapBuffer(int initialCapacity, int maxCapacity) | 返回一个基于堆内存存储的 ByteBuf
directBuffer() <br> directBuffer(int initialCapacity) <br> directBuffer(int initialCapacity, int maxCapacity) | 返回一个基于直接内存存储的 ByteBuf
compositeBuffer() <br> compositeBuffer(int maxNumComponents) <br> compositeDirectBuffer() <br> compositeDirectBuffer(int maxNumComponents) <br> compositeHeapBuffer() <br> compositeHeapBuffer(int maxNumComponents) | 返回一个可以通过添加最大到指定数目的基于堆的或者直接内存存储的缓冲区来扩展的 CompositeByteBuf
ioBuffer() | 返回一个用于套接字的 I/O 操作的 ByteBuf

得到一个 ByteBufAllocator 的引用很简单. 你可以得到从 Channel (在理论上, 每 Channel 可具有不同的 ByteBufAllocator), 或通过绑定到的 ChannelHandler 的 ChannelHandlerContext 得到它, 用它实现了你数据处理逻辑.

<p align="center"><font size=2>代码清单 5-14 获取一个到 ByteBufAllocator 的引用</font></p>

``` java
    // 从 Channel 获取一个到 ByteBufAllocator 的引用
    Channel channel = ...;
    ByteBufAllocator allocator = channel.alloc();
    ....
    // 从 ChannelHandlerContext 获取一个到 ByteBufAllocator 的引用
    ChannelHandlerContext ctx = ...;
    ByteBufAllocator allocator2 = ctx.alloc();
    ...
```

Netty 提供了两种 ByteBufAllocator 的实现, 一种是 PooledByteBufAllocator, 用 ByteBuf 实例池改进性能并将内存使用降到最低, 此实现使用一个 "[jemalloc](http://people.freebsd.org/~jasone/jemalloc/bsdcan2006/jemalloc.pdf)" 内存分配. 另一种是 UnpooledByteBufAllocator , 它的实现不池化 ByteBuf, 每次调用都会返回一个新的 ByteBuf 实例.

### 5.5.2 Unpooled 缓冲区

可能某些情况下, 你未能获取一个到 ByteBufAllocator 的引用. 对于这种情况, Netty 提供了一个简单的称为 Unpooled 的工具类, 它提供了静态的辅助方法来创建未池化的 ByteBuf 实例.

Table 5.9 Unpooled helper class

名称     | 描述
-------- | ---
buffer() <br> buffer(int initialCapacity) <br> buffer(int initialCapacity, int maxCapacity)  | 返回一个未池化的基于堆内存存储的 ByteBuf |
directBuffer() <br> directBuffer(int initialCapacity) <br> directBuffer(int initialCapacity, int maxCapacity) | 返回一个未池化的基于直接内存存储的 ByteBuf
wrappedBuffer()  | 返回一个包装了给定数据的 ByteBuf
copiedBuffer()  | 返回一个复制了给定数据的 ByteBuf


在非联网项目, 该 Unpooled 类也使得它更容易使用的 ByteBuf API, 获得一个高性能的可扩展缓冲 API, 而不需要 Netty 的其他部分的.

### 5.5.3 ByteBufUtil 类

ByteBufUtil 提供了用于操作 ByteBuf 的静态的辅助方法. 因为这个 API 是通用的, 并且和池化无关, 所以这些方法已然在分配类的外部实现.

这些静态方法中最有价值的可能就是 hexdump() 方法, 它以十六进制的表示形式打印 ByteBuf 的内容.

另一个有用的方法是 boolean equals(ByteBuf, ByteBuf), 它被用来判断两个 ByteBuf 实例的相等性.

## 5.6 引用计数

引用计数是一种通过在某个对象所持有的资源不再被其他对象引用时释放该对象所持有的资源来优化内存使用和性能的技术. Netty 在第 4 版中为 ByteBuf 和 ByteBufHolder 引入了引用计数技术, 它们都实现了 ReferenceCounted 接口.

引用计数对于池化实现 (如 PooledByteBufAllocator) 来说是至关重要的, 它降低了内存分配的开销. 代码清单 5-15 和代码清单 5-16 展示了相关的示例:

<p align="center"><font size=2>代码清单 5-15 引用计数</font></p>

``` java
    Channel channel = ...;
    // 从 Channel 获取 ByteBufAllocator
    ByteBufAllocator allocator = channel.alloc();
    ....
    // 从 ByteBufAllocator 分配一个 ByteBuf
    ByteBuf buffer = allocator.directBuffer();
    // 检查引用计数是否为预期的 1
    assert buffer.refCnt() == 1;
    ...
```

<p align="center"><font size=2>代码清单 5-16 释放引用计数的对象</font></p>

``` java
    ByteBuf buffer = ...;
    // 减少到该对象的活动引用. 当减少到 0 时, 该对象被释放, 并且该方法返回 true
    boolean released = buffer.release();
    ...
```

试图访问一个已经被释放的引用计数的对象, 将会导致一个 IllegalReferenceCount-Exception.

注意, 一个特定的(实现了 ReferenceCounted 接口)类, 可以用它自己的独特方式来定义它的引用计数规则. 例如, 我们可以设计一个类, 其 release() 方法的实现总是将引用计数设为零, 而不用关心它的当前值, 从而一次性地使所有的活动引用都失效.

> 谁负责释放?
> 一般来说, 是由最后访问(引用计数)对象的那一方来负责将它释放. 在第 6 章中, 我
> 们将会解释这个概念和 ChannelHandler 以及 ChannelPipeline 的相关性.

# 6 ChannelHandler 和 ChannelPipeline

本章主要内容

* Channel
* ChannelHandler
* ChannePipeline
* ChannelHandlerContext

我们在上一章研究的 ByteBuf 是一个用来 "包装" 数据的容器. 在本章我们将探讨这些容器是如何在应用程序中进行传输的, 以及如何处理它们 "包装" 的数据.

Netty 在这方面提供了强大的支持. 它让 Channelhandler 链接在 ChannelPipeline 上, 从而使数据处理更加灵活和模块化.

在这一章中我们会遇到各种各样 Channelhandler 和 ChannelPipeline 的使用案例, 以及重要的相关的类 Channelhandlercontext. 我们将展示如何利用这些组件来实现干净可重用的处理逻辑.

### 6.1.1 Channel 的生命周期

Interface Channel 定义了一组和 ChannelInboundHandler API 密切相关的简单但
功能强大的状态模型, 表 6-1 列出了 Channel 的这 4 个状态:

| 状态     | 描述 |
| -------- | --- |
| ChannelUnregistered  | Channel 已经被创建, 但还未注册到 EventLoop |
| ChannelRegistered  | Channel 已经被注册到了 EventLoop |
| ChannelActive  | Channel 处于活动状态 (已经连接到它的远程节点). 它现在可以接收和发送数据了 |
| ChannelInactive  | Channel 没有连接到远程节点 |

![Channel_State_Model](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_6.1_Channel_State_Model.jpg)

### 6.1.2 ChannelHandler 的生命周期

<p align="center"><font size=2>表 6-2 ChannelHandler 的生命周期方法</font></p>

| 类型     | 描述 |
| -------- | --- |
| handlerAdded  | 当把 ChannelHandler 添加到 ChannelPipeline 中时被调用 |
| handlerRemoved  | 当从 ChannelPipeline 中移除 ChannelHandler 时被调用 |
| exceptionCaught  | 当处理过程中在 ChannelPipeline 中有错误产生时被调用 |

Netty 定义了下面两个重要的 ChannelHandler 子接口:

* ChannelInboundHandler -- 处理入站数据以及各种状态变化;
* ChannelOutboundHandler -- 处理出站数据并且允许拦截所有的操作.

### 6.1.3 ChannelInboundHandler 接口

<p align="center"><font size=2>表 6-3 ChannelInboundHandler 的方法</font></p>

| 类型     | 描述 |
| -------- | --- |
| channelRegistered  | 当 Channel 已经注册到它的 EventLoop 并且能够处理 I/O 时被调用 |
| channelUnregistered  | 当 Channel 从它的 EventLoop 注销并且无法处理任何 I/O 时被调用 |
| channelActive  | 当 Channel 处于活动状态时被调用; Channel 已经连接/绑定并且已经就绪 |
| channelInactive  | 当 Channel 离开活动状态并且不再连接它的远程节点时被调用 |
| channelReadComplete  | 当 Channel 上的一个读操作完成时被调用 |
| channelRead  | 当从 Channel 读取数据时被调用 |
| ChannelWritabilityChanged  | 当 Channel 的可写状态发生改变时被调用. 用户可以确保写操作不会完成得太快(以避免发生 OutOfMemoryError)或者可以在 Channel 变为再次可写时恢复写入. 可以通过调用 Channel 的 isWritable() 方法来检测Channel 的可写性. 与可写性相关的阈值可以通过 Channel.config().setWriteHighWaterMark() 和 Channel.config().setWriteLowWaterMark() 方法来设置. |
| userEventTriggered  | 当 ChannelnboundHandler.fireUserEventTriggered() 方法被调用时被调用, 因为一个 POJO 被传经了 ChannelPipeline |

当某个 ChannelInboundHandler 的实现重写 channelRead() 方法时, 它将负责显式地释放与池化的 ByteBuf 实例相关的内存.

<p align="center"><font size=2>代码清单 6-1 释放消息资源</font></p>

``` java
    @Sharable
    // 扩展了 ChannelInboundHandlerAdapter
    public class DiscardHandler extends ChannelInboundHandlerAdapter {
        @Override
        public void channelRead(ChannelHandlerContext ctx, Object msg) {
            // 丢弃已接收的消息
            ReferenceCountUtil.release(msg);
        }
    }
```

<font color=#fd0209 size=6 >问题: 这里继承的为什么是 ChannelInboundHandlerAdapter, 而不是 ChannelInboundHandler?</font>

Netty 将使用 WARN 级别的日志消息记录未释放的资源, 使得可以非常简单地在代码中发现违规的实例.

另外 SimpleChannelInboundHandler 可以自动释放资源, 所以你不应该存储指向任何消息的引用供将来使用, 因为这些引用都将会失效.

### 6.1.4 ChannelOutboundHandler 接口

出站操作和数据将由 ChannelOutboundHandler 处理. 它的方法将被 Channel, ChannelPipeline 以及 ChannelHandlerContext 调用.

ChannelOutboundHandler 的一个强大的功能是可以按需推迟操作或者事件, 这使得可以通过一些复杂的方法来处理请求. 例如, 如果向远程节点的数据写入被暂停了, 那么你可以延迟执行刷新操作, 稍后在继续.

下面显示了 ChannelOutboundHandler 的方法(继承自 ChannelHandler 未列出来)

<p align="center"><font size=2>表 6-4 ChannelOutboundHandler 的方法</font></p>

类型 | 描述
-----|---------
bind(ChannelHandlerContext ctx, SocketAddress localAddress, ChannelPromise promise)  | 当请求将 Channel 绑定到本地地址时被调用
connect(ChannelHandlerContext ctx, SocketAddress remoteAddress, SocketAddress localAddress, ChannelPromise promise) | 当请求将 Channel 连接到远程节点时被调用
disconnect(ChannelHandlerContext ctx, ChannelPromise promise) | 当请求将 Channel 从远程节点断开时被调用
close(ChannelHandlerContext ctx, ChannelPromise promise)  | 当请求关闭 Channel 时被调用
deregister(ChannelHandlerContext ctx, ChannelPromise promise) |  当请求将 Channel 从它的 EventLoop 注销时被调用
read(ChannelHandlerContext ctx)  | 当请求从 Channel 读取更多的数据时被调用
flush(ChannelHandlerContext ctx)  | 当请求通过 Channel 将入队数据冲刷到远程节点时被调用
write(ChannelHandlerContext ctx, Object msg, ChannelPromise promise)  | 当请求通过 Channel 将数据写到远程节点时被调用

> ChannelPromise vs. ChannelFuture
ChannelPromise 与 ChannelFuture ChannelOutboundHandler 中的大部分方法都需要一个 ChannelPromise 参数, 以便在操作完成时得到通知.

### 6.1.5 ChannelHandler 适配器

你可以使用 ChannelInboundHandlerAdapter 和 ChannelOutboundHandlerAdapter类作为自己的 ChannelHandler 的起始点. 这两个适配器分别提供了 ChannelInboundHandler 和 ChannelOutboundHandler 的基本实现. 通过扩展抽象类 ChannelHandlerAdapter, 它们获得了它们共同的超接口 ChannelHandler 的方法.

![ChannelHandlerAdapter](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_6.2_ChannelHandlerAdapter.png)

<font color=#fd0209 size=6 >问题: 这里难道不会有多重继承导致的菱形继承问题么?</font>

ChannelHandlerAdapter 还提供了实用方法 isSharable(). 如果其对应的实现被标注为 Sharable, 那么这个方法将返回 true, 表示它可以被添加到多个 ChannelPipeline 中.

在 ChannelInboundHandlerAdapter 和 ChannelOutboundHandlerAdapter 中所提供的方法体调用了其相关联的 ChannelHandlerContext 上的等效方法, 从而将事件转发到了 ChannelPipeline 中的下一个 ChannelHandler 中.

你要想在自己的 ChannelHandler 中使用这些适配器类, 只需要简单地扩展它们, 并且重写那些你想要自定义的方法.

### 6.1.6 资源管理

每当通过调用 ChannelInboundHandler.channelRead() 或者 ChannelOutboundHandler.write() 方法来处理数据时, 你都需要确保没有任何的资源泄漏.

为了让用户更加简单的找到遗漏的释放, Netty 包含了一个 ResourceLeakDetector, 将会从已分配的缓冲区 1% 作为样品来检查是否存在在应用程序泄漏.

实现 ChannelInboundHandler.channelRead() 和 ChannelOutboundHandler.write() 方法时, 应该如何使用这个诊断工具来防止泄露呢? 让我们看看 channelRead() 函数直接消费入站消息的场景; 也就是说, 它不会通过调用 ChannelHandlerContext.fireChannelRead() 方法将入站消息转发给下一个 ChannelInboundHandler. 代码清单6-3 展示了如何释放消息.

<p align="center"><font size=2>代码清单 6-3 消费并释放入站消息</font></p>

``` java
    @ChannelHandler.Sharable
    // 扩展了 ChannelInboundandlerAdapter
    public class DiscardInboundHandler extends ChannelInboundHandlerAdapter {
        @Override
        public void channelRead(ChannelHandlerContext ctx,
                                         Object msg) {
            // 调用 ReferenceCountUtil.release() 方法释放资源
            ReferenceCountUtil.release(msg);
        }
    }
```

>SimpleChannelInboundHandler -- 消费入站消息的简单方式
>由于消费入站数据是一项常规任务, 所以 Netty 提供了一个特殊的称为 SimpleChannelInboundHandler 的 ChannelInboundHandler 实现. 这个实现会在消息被 channelRead0() 方法消费之后自动释放消息.

在出站方向这边, 如果你处理了 write() 操作并丢弃了一个消息, 那么你也应该负责释放它占用的内存. 代码清单 6-4 展示了一个丢弃所有的写入数据的实现.

<p align="center"><font size=2>代码清单 6-4 丢弃并释放出站消息</font></p>

``` java
@ChannelHandler.Sharable
// 扩展了 ChannelOutboundHandlerAdapter
public class DiscardOutboundHandler
        extends ChannelOutboundHandlerAdapter {

    @Override
    public void write(ChannelHandlerContext ctx,
                                     Object msg, ChannelPromise promise) {
        // 通过使用 ReferenceCountUtil.realse() 方法释放资源
        ReferenceCountUtil.release(msg);
        // 通知 ChannelPromise 数据已经被处理了
        promise.setSuccess();
    }

}
```

重要的是, 不仅要释放资源, 还要通知 ChannelPromise. 否则可能会出现 ChannelFutureListener 收不到某个消息已经被处理了的通知的情况. 总之, 如果一个消息被消费或者丢弃了, 并且没有传递给 ChannelPipeline 中的下一个 ChannelOutboundHandler, 那么用户就有责任调用 ReferenceCountUtil.release() 释放消息占用的内存. 如果消息到达了实际的传输层, 那么当它被写入时或者 Channel 关闭时, 都将被自动释放.

## 6.2 ChannelPipeline 接口

ChannelPipeline 是一系列 ChannelHandler 实例组成的实例链, 用于拦截流经一个 Channel 的入站和出站事件, ChannelPipeline 允许用户自定义对入站/出站事件的处理逻辑, 以及 pipeline 里的各个 Handler 之间的交互.

每一个新创建的 Channel 都将会被分配一个新的 ChannelPipeline. 这项关联是永久性的; Channel 既不能附加另外一个 ChannelPipeline, 也不能分离其当前的.

根据事件的起源, 事件将会被 ChannelInboundHandler 或者ChannelOutboundHandler 处理. 随后, 通过调用 ChannelHandlerContext 它将被转发给同一超类型的下一个 ChannelHandler.

> ChannelHandlerContext
> ChannelHandlerContext 使得 ChannelHandler 能够和其所属的 ChannelPipeline 以及其他 ChannelHandler 交互. ChannelHandler 可以通知其所属的 ChannelPipeline 中的下一个 ChannelHandler, 甚至可以动态修改它所属的 ChannelPipeline(这里的修改是指修改其所属的 ChannelPipeline 中 ChannelHandler 的编排, ChannelHandlerContext 是 ChannelPipeline 的控制模块).

<p align="center"><font size=2>图 6-3 ChannelPipeline 和它的 ChannelHandler</font></p>

![ChannelPipeline_and_ChannelHandlers](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_6.2_ChannelPipeline_and_ChannelHandlers.jpg)

在 ChannelPipeline 传播事件时, 它会测试 ChannelPipeline 中的下一个 ChannelHandler 的类型是否和事件的运动方向相匹配. 如果不匹配, ChannelPipeline 将跳过该 ChannelHandler 并前进到下一个, 直到它找到和该事件所期望的方向相匹配的为止.(当然, ChannelHandler 也可以同时实现 ChannelInboundHandler 接口和 ChannelOutboundHandler 接口).

### 6.2.1 修改 ChannelPipeline

可以通过添加, 删除或者替换 ChannelHandler 来实时修改 ChannelPipeline 的布局.

<p align="center"><font size=2>表 6-6 ChannelHandler 的用于修改 ChannelPipeline 的方法</font></p>

名称 | 描述
------ | ----
addFirst <br> addBefore <br> addAfter <br> addLast | 将一个 ChannelHandler 添加到 ChannelPipeline 中
Remove | 将一个 ChannelHandler 从 ChannelPipeline 中移除
Replace | 将 ChannelPipeline 中的一个 ChannelHandler 替换为另一个 ChannelHandler


>**ChannelHandler 的执行和阻塞**
通常 ChannelPipeline 中的每一个 ChannelHandler 都是通过它的 EventLoop (I/O 线程) 来处理传递给它的事件的. 所以重要的是不要阻塞这个线程, 因为这会对整体的 I/O 处理产生负面的影响.
但有时可能需要与那些使用阻塞 API 的遗留代码进行交互. 对于这种情况, ChannelPipeline 有一些接受一个 EventExecutorGroup 的 add() 方法. 如果一个事件被传递给一个自定义的 EventExecutorGroup, 它将被包含在这个 EventExecutorGroup 中的某个 EventExecutor 所处理, 从而被从该
Channel 本身的 EventLoop 中移除. 对于这种场景, Netty 提供了一个叫 DefaultEventExecutorGroup 的默认实现.

<p align="center"><font size=2>表 6-7 ChannelPipeline 的用于访问 ChannelHandler 的操作</font></p>

名称 | 描述
------ | ----
get() | 通过类型或者名称返回 ChannelHandler
context() | 返回和 ChannelHandler 绑定的 ChannelHandlerContext
names() | 返回 ChannelPipeline 中所有 ChannelHandler 的名称

<font color=#fd0209 size=6 >问题: 一个 ChannelPipeline 上的 ChannelHandler 可以绑定多少个 ChannelHandlerContext?</font>

### 6.2.2 触发事件

ChannelPipeline 的 API 公开了用于调用入站和出站操作的附加方法. 表 6-8 列出了入站操作, 用于通知 ChannelInboundHandler 在 ChannelPipeline 中所发生的事件.

<p align="center"><font size=2>表 6-8 ChannelPipeline 的入站操作</font></p>

名称 | 描述
-----|---
fireChannelRegistered | 调用 ChannelPipeline 中下一个 ChannelInboundHandler 的 channelRegistered(ChannelHandlerContext) 方法
fireChannelUnregistered | 调用 ChannelPipeline 中下一个 ChannelInboundHandler 的 channelUnregistered(ChannelHandlerContext) 方法
fireChannelActive | 调用 ChannelPipeline 中下一个 ChannelInboundHandler 的 channelActive(ChannelHandlerContext) 方法
fireChannelInactive | 调用 ChannelPipeline 中下一个 ChannelInboundHandler 的 channelInactive(ChannelHandlerContext) 方法
fireExceptionCaught | 调用 ChannelPipeline 中下一个 ChannelInboundHandler 的 exceptionCaught(ChannelHandlerContext, Throwable) 方法
fireUserEventTriggered | 调用 ChannelPipeline 中下一个 ChannelInboundHandler 的 userEventTriggered(ChannelHandlerContext, Object) 方法
fireChannelRead | 调用 ChannelPipeline 中下一个 ChannelInboundHandler 的 channelRead(ChannelHandlerContext, Object msg) 方法
fireChannelReadComplete | 调用 ChannelPipeline 中下一个 ChannelInboundHandler 的 channelReadComplete(ChannelHandlerContext) 方法
fireChannelWritabilityChanged | 调用 ChannelPipeline 中下一个 ChannelInboundHandler 的 channelWritabilityChanged(ChannelHandlerContext) 方法

<p align="center"><font size=2>表 6-9 ChannelPipeline 的出站操作</font></p>

名称 | 描述
-----|---
bind | 将 Channel 绑定到一个本地地址, 这将调用 ChannelPipeline 中的下一个 ChannelOutboundHandler 的 bind(ChannelHandlerContext, SocketAddress, ChannelPromise) 方法
connect | 将 Channel 连接到一个远程地址, 这将调用 ChannelPipeline 中的下一个 ChannelOutboundHandler 的 connect(ChannelHandlerContext, SocketAddress, ChannelPromise) 方法
disconnect | 将 Channel 断开连接. 这将调用 ChannelPipeline 中的下一个 ChannelOutboundHandler 的 disconnect(ChannelHandlerContext, Channel Promise) 方法
close | 将 Channel 关闭. 这将调用 ChannelPipeline 中的下一个 ChannelOutboundHandler 的 close(ChannelHandlerContext, ChannelPromise) 方法
deregister | 将 Channel 从它先前所分配的 EventExecutor (即 EventLoop)中注销. 这将调用 ChannelPipeline 中的下一个 ChannelOutboundHandler 的 deregister (ChannelHandlerContext, ChannelPromise) 方法
flush | 冲刷 Channel 所有挂起的写入. 这将调用 ChannelPipeline 中的下一个 ChannelOutboundHandler 的 flush(ChannelHandlerContext) 方法
write | 将消息写入 Channel. 这将调用 ChannelPipeline 中的下一个 ChannelOutboundHandler 的 write(ChannelHandlerContext, Object msg, ChannelPromise) 方法. 注意: 这并不会将消息写入底层的 Socket, 而只会将它放入队列中. 要将它写入 Socket, 需要调用 flush() 或者 writeAndFlush() 方法
writeAndFlush | 这是一个先调用 write() 方法再接着调用 flush() 方法的便利方法
read | 请求从 Channel 中读取更多的数据. 这将调用 ChannelPipeline 中的下一个 ChannelOutboundHandler 的 read(ChannelHandlerContext) 方法

总结一下:
* ChannelPipeline 保存了与 Channel 相关联的 ChannelHandler;
* ChannelPipeline 可以根据需要, 通过添加或者删除 ChannelHandler 来动态地修改;
* ChannelPipeline 有着丰富的 API 来响应入站和出站事件.

## 6.3 ChannelHandlerContext 接口

接口 ChannelHandlerContext 代表 ChannelHandler 和 ChannelPipeline 之间的关联, 每当有 ChannelHandler 添加到 ChannelPipeline 中时, 都会创建 ChannelHandlerContext实例. ChannelHandlerContext 的主要功能是管理通过同一个 ChannelPipeline 关联的 ChannelHandler 之间的交互.

ChannelHandlerContext 有许多方法, 其中一些也出现在 Channel 和 ChannelPipeline. 然而, 如果您通过 Channel 或ChannelPipeline 的实例来调用这些方法, 他们就会在整个 pipeline 中传播. 相比之下, 一样的方法在 ChannelHandlerContext 的实例上调用, 就只会从当前的 ChannelHandler 开始并传播到相关管道中的下一个有处理事件能力的 ChannelHandler.

<p align="center"><font size=2>表 6-10 ChannelHandlerContext 的 API</font></p>

方法名称 | 描述
------ | ----
alloc | 返回和这个实例相关联的 Channel 所配置的 ByteBufAllocator
bind | 绑定到给定的 SocketAddress ，并返回 ChannelFuture
channel  | 返回绑定到这个实例的 Channel
close  | 关闭 Channel, 并返回 ChannelFuture
connect | 连接给定的 SocketAddress, 并返回 ChannelFuture
deregister  | 从之前分配的 EventExecutor 注销, 并返回 ChannelFuture
disconnect | 从远程节点断开, 并返回 ChannelFuture
executor |  返回调度事件的 EventExecutor
fireChannelActive  | 触发对下一个 ChannelInboundHandler 上的 channelActive() 方法(已连接)的调用
fireChannelInactive | 触发对下一个 ChannelInboundHandler 上的 channelInactive() 方法(已关闭)的调用
fireChannelRead |  触发对下一个 ChannelInboundHandler 上的 channelRead() 方法(已接收的消息)的调用
fireChannelReadComplete | 触发对下一个 ChannelInboundHandler 上的channelReadComplete() 方法的调用
fireChannelRegistered | 触发对下一个 ChannelInboundHandler fireChannelRegistered() 方法的调用
fireChannelUnregistered | 触发对下一个 ChannelInboundHandler fireChannelUnregistered() 方法的调用
fireChannelWritabilityChanged | 触发对下一个 ChannelInboundHandler fireChannelWritabilityChanged() 方法的调用
fireExceptionCaught | 触发对下一个 ChannelInboundHandler fireExceptionCaught() 方法的调用
fireUserEventTriggered | 触发对下一个 ChannelInboundHandler fireUserEventTriggered() 方法的调用
handler  | 返回绑定到这个实例的 ChannelHandler
isRemoved  | 如果所关联的 ChannelHandler 已经被从 ChannelPipeline 中移除则返回 true
name  |  返回这个实例的唯一名称
pipeline  | 返回这个实例所关联的 ChannelPipeline
read  | 将数据从 Channel 读取到第一个入站缓冲区; 如果读取成功则触发一个 channelRead 事件, 并 (在最后一个消息被读取完成后)通知 ChannelInboundHandler 的 channelReadComplete(ChannelHandlerContext) 方法
write  | 通过这个实例写入消息并经过 ChannelPipeline
writeAndFlush  | 通过这个实例写入并冲刷消息并经过 ChannelPipeline

其他注意注意事项:
* ChannelHandlerContext 与 ChannelHandler 的关联(绑定)从不改变, 所以缓存对它的引用是安全的;
* 如同我们在本节开头所解释的一样, 相对于其他类的同名方法, ChannelHandler Context 的方法将产生更短的事件流, 应该尽可能地利用这个特性来获得最大的性能.

### 6.3.1 使用 ChannelHandlerContext

<p align="center"><font size=2>图 6-4 Channel, ChannelPipeline, ChannelHandler 以及 ChannelHandlerContext 之间的关系</font></p>

![Channel_ChannelPipeline_ChannelHandler_and_ChannelHandlerContext](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_6.4_Channel_ChannelPipeline_ChannelHandler_and_ChannelHandlerContext.png)

<br>
<br>

下图中我们可以看到, 虽然在 Channel 或 ChannelPipeline 上调用 write() 方法会使事件通过整个 ChannelPipeline, 但是在 ChannelHandler 的级别上, 事件从一个 ChannelHandler 传播到下一个 ChannelHandler 是通过 ChannelHandlerContext 上的调用完成的.

![通过 Channel 或者 ChannelPipeline 进行的事件传播](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_6.5_Event_propagation_via_the_Channel_or_the_ChannelPipeline.png)

<br>
<br>

要想让某个特定的 ChannelHandler 处理事件, 必须获取到指定  ChannelHandler 之前的 ChannelHandler 关联的 ChannelHandlerContext. 这个 ChannelHandlerContext 将调用指定的 ChannelHandler.

![通过 ChannelHandlerContext 触发的操作的事件流](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_6.6_Event_flow_for_operations_triggered_via_the_ChannelHandlerContext.png)

### 6.3.2 ChannelHandler 和 ChannelHandlerContext 的高级用法

你可以通过调用 ChannelHandlerContext 上的 pipeline() 方法来获得被封闭的 ChannelPipeline 的引用. 这使得运行时得以操作 ChannelPipeline 中的 ChannelHandler 链, 我们可以利用这一点来实现一些复杂的设计. 例如, 你可以通过将 ChannelHandler 添加到 ChannelPipeline 中来实现动态的协议切换.

另一种高级的用法是缓存到 ChannelHandlerContext 的引用以供稍后使用, 这可能会发生在任何的 ChannelHandler 方法之外, 甚至来自于不同的线程. 代码清单 6-9 展示了用这种模式来触发事件.

<p align="left"><font size=2>代码清单 6-9 缓存到 ChannelHandlerContext 的引用</font></p>

``` java
    public class WriteHandler extends ChannelHandlerAdapter {
        private ChannelHandlerContext ctx;

        @Override
        public void handlerAdded(ChannelHandlerContext ctx) {
            // 存储到 ChannelHandlerContext 的引用以供稍后使用
            this.ctx = ctx;
        }

        // 使用之前存储的到 ChannelHandlerContext 的引用来发送消息
        public void send(String msg) {
            ctx.writeAndFlush(msg);
        }
    }
```

因为一个 ChannelHandler 可以从属于多个 ChannelPipeline, 所以它也可以绑定到多个 ChannelHandlerContext 实例. 对于这种用法指在多个 ChannelPipeline 中共享同一个 ChannelHandler, 对应的 ChannelHandler 必须要使用 `@Sharable` 注解标注; 否则, 试图将它添加到多个 ChannelPipeline 时将会触发异常. 显而易见, 为了安全地被用于多个并发的 Channel(即连接), 这样的 ChannelHandler 必须是线程安全的.

<p align="left"><font size=2>代码清单 6-10 可共享的 ChannelHandler</font></p>

``` java
    @Sharable
    public class SharableHandler extends ChannelInboundHandlerAdapter {

        @Override
        public void channelRead(ChannelHandlerContext ctx, Object msg) {
            System.out.println("channel read message " + msg);
            // 转发给下一个 ChannelHandler
            ctx.fireChannelRead(msg);
        }
    }
```

前面的 ChannelHandler 实现符合所有的将其加入到多个 ChannelPipeline 的需求, 即它使用了注解 `@Sharable` 标注, 并且也不持有任何的状态.

<p align="left"><font size=2>代码清单 6-11 @Sharable 的错误用法</font></p>

``` java
    @Sharable
    public class SharableHandler extends ChannelInboundHandlerAdapter {
        private int count;

        @Override
        public void channelRead(ChannelHandlerContext ctx, Object msg) {
            count++;
            System.out.println("channelRead(...) called the " + count + " time");
            // 转发给下一个 ChannelHandler
            ctx.fireChannelRead(msg);
        }
    }
```

这段代码的问题在于它拥有状态, 即用于跟踪方法调用次数的实例变量count. 将这个类的一个实例添加到 ChannelPipeline 将极有可能在它被多个并发的Channel 访问时导致问题. (当然, 这个简单的问题可以通过使channelRead()方法变为同步方法来修正)
总之, 只应该在确定了你的 ChannelHandler 是线程安全的时才使用 `@Sharable` 注解.
在多个 ChannelPipeline 中安装同一个 ChannelHandler 通常是为了收集跨越多个 Channel 的统计信息.

### 6.4.1 处理入站异常

* ChannelHandler.exceptionCaught()的默认实现是简单地将当前异常转发给 ChannelPipeline 中的下一个 ChannelHandler;
* 如果异常到达了 ChannelPipeline 的尾端, 它将会被记录为未被处理;
* 要想定义自定义的处理逻辑, 你需要重写 exceptionCaught() 方法.然后你需要决定是否需要将该异常传播出去.

### 6.4.2 处理出站异常

* 每个出站操作都将返回一个 ChannelFuture. 注册到 ChannelFuture 的 ChannelFutureListener 将在操作完成时被通知该操作是成功了还是出错了.
* 几乎所有的 ChannelOutboundHandler 上的方法都会传入一个 ChannelPromise 的实例. 作为 ChannelFuture 的子类, ChannelPromise 也可以被分配用于异步通知的监听器. 但是, ChannelPromise 还具有提供立即通知的可写方法: ChannelPromise setSuccess(); ChannelPromise setFailure(Throwable cause);

<p align="left"><font size=2>代码清单 6-13 添加 ChannelFutureListener 到 ChannelFuture</font></p>

``` java
ChannelFuture future = channel.write(someMessage);
future.addListener(new ChannelFutureListener() {
    @Override
    public void operationComplete(ChannelFuture f) {
        if (!f.isSuccess()) {
            f.cause().printStackTrace();
            f.channel().close();
        }
    }
}
);
```

<p align="left"><font size=2>代码清单 6-14 添加 ChannelFutureListener 到 ChannelPromise</font></p>

``` java
public class OutboundExceptionHandler extends ChannelOutboundHandlerAdapter {
    @Override
    public void write(ChannelHandlerContext ctx, Object msg,
        ChannelPromise promise) {
        promise.addListener(new ChannelFutureListener() {
            @Override
            public void operationComplete(ChannelFuture f) {
                if (!f.isSuccess()) {
                    f.cause().printStackTrace();
                    f.channel().close();
                }
            }
        });
    }
}
```

#  7 EventLoop 和线程模型

## 7.1 线程模型概述

![Executor的执行逻辑](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_7.1_Executor的执行逻辑.png)

虽然池化和重用线程相对于简单地为每个任务都创建和销毁线程是一种进步, 但是它并不能消除由上下文切换所带来的开销, 开销将随着线程数量的增加很迅速增长, 并且在高负载下愈演愈烈.

## 7.2 EventLoop 接口

![EventLoop的类层次结构](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_7.2_EventLoop的类层次结构.png)

### 7.2.1 Netty 4 中的 I/O 和事件处理

Netty 4 中所采用的线程模型, 通过在同一个线程中处理某个给定的 EventLoop 中所产生的所有事件, 解决了这个问题. 这提供了一个更加简单的执行体系架构, 并且消除了在多个 ChannelHandler 中进行同步的需要(除了任何可能需要在多个 Channel 中共享的).

## 7.3 任务调度

### 7.3.1 JDK 的任务调度 API

<p align="left"><font size=2>表 7-1 java.util.concurrent.Executors 类的工厂方法</font></p>

方法 | 描述
------ | ----
newScheduledThreadPool(int corePoolSize) <br> newScheduledThreadPool(int corePoolSize, ThreadFactorythreadFactory) | 创建一个 ScheduledThreadExecutorService, 用于调度命令在指定延迟之后运行或者周期性地执行. 它使用 corePoolSize 参数来设置线程数.
newSingleThreadScheduledExecutor() <br> newSingleThreadScheduledExecutor(ThreadFactorythreadFactory) | 创建一个 ScheduledThreadExecutorService, 用于调度命令在指定延迟之后运行或者周期性地执行. 它使用一个线程来执行被调度的任务.

### 7.3.2 使用 EventLoop 调度任务

## 7.4 实现细节

### 7.4.1 线程管理

Netty线程模型的卓越性能取决于对于当前执行的Thread的身份的确定, 也就是说, 确定它是否是分配给当前 Channel 以及它的 EventLoop 的那一个线程. (回想一下 EventLoop 将负责处理一个 Channel 的整个生命周期内的所有事件).
如果(当前)调用线程正是支撑 EventLoop 的线程, 那么所提交的代码块将会被直接执行. 否则, EventLoop 将调度该任务以便稍后执行, 并将它放入到内部队列中. 当 EventLoop下次处理它的事件时, 它会执行队列中的那些任务/事件. 这也就解释了任何的 Thread 是如何与 Channel 直接交互而无需在 ChannelHandler 中进行额外同步的.

注意, 每个 EventLoop 都有它自已的任务队列, 独立于任何其他的 EventLoop. 图 7-3 展示了 EventLoop 用于调度任务的执行逻辑. 这是 Netty 线程模型的关键组成部分.

![EventLoop的执行逻辑](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_7.3_EventLoop的执行逻辑.png)

"永远不要将一个长时间运行的任务放入到执行队列中, 因为它将阻塞需要在同一线程上执行的任何其他任务." 如果必须要进行阻塞调用或者执行长时间运行的任务, 我们建议使用一个专门的 EventExecutor.

### 7.4.2 EventLoop/线程的分配

服务于 Channel 的 I/O 和事件的 EventLoop 包含在 EventLoopGroup 中.

### 1. 异步传输

异步传输实现只使用了少量的 EventLoop(以及和它们相关联的 Thread), 而且在当前的线程模型中, 它们可能会被多个 Channel 所共享. 这使得可以通过尽可能少量的 Thread 来支撑大量的 Channel, 而不是每个 Channel 分配一个 Thread.

图 7-4 显示了一个 EventLoopGroup, 它具有3 个固定大小的 EventLoop (每个 EventLoop 都由一个 Thread 支撑). 在创建 EventLoopGroup 时就直接分配了 EventLoop(以及支撑它们的 Thread), 以确保在需要时它们是可用的.

![用于非阻塞传输的EventLoop分配方式](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_7.4_用于非阻塞传输的EventLoop分配方式.png)

EventLoopGroup 负责为每个新创建的 Channel 分配一个 EventLoop. 在当前实现中, 使用顺序循环(round-robin)的方式进行分配以获取一个均衡的分布, 并且相同的 EventLoop 可能会被分配给多个 Channel.(这一点在将来的版本中可能会改变)

一旦一个 Channel 被分配给一个 EventLoop, 它将在它的整个生命周期中都使用这个 EventLoop(以及相关联的 Thread). 请牢记这一点, 因为它可以使你从担忧你的 ChannelHandler 实现中的线程安全和同步问题中解脱出来.

另外, 需要注意的是 EventLoop 的分配方式对 ThreadLocal 使用的影响. 因为一个 EventLoop 通常会被用于支撑多个 Channel, 所以对于所有关联的 Channel 来说, ThreadLocal 都将是一样的. 这使得它对于实现状态追踪等功能来说是个糟糕的选择. 然而, 在一些无状态的上下文中, 它仍然可以被用于在多个 Channel 之间共享一些重度的或者代价昂贵的对象, 甚至是事件.

### 2. 阻塞传输

用于像 OIO(旧的阻塞式 I/O), 设计会略有不同, 如图 7-5 所示:

这里每一个 Channel 都将被分配给一个 EventLoop.

![阻塞传输的EventLoop分配方式](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_7.5_阻塞传输的EventLoop分配方式.png)

# 8 引导(Bootstrap)

## 8.1 Bootstrap 类

![引导类的层次结构](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_8.1_引导类的层次结构.png)

>**为什么引导类是 Cloneable 的**
你有时可能会创建多个具有类似配置或者完全相同配置的 Channel. 为了支持这种模式而又不需要为每个 Channel 都创建并配置一个新的引导类实例, AbstractBootstrap 被标记为了Cloneable. 注意, 这种方式只会创建引导类实例的 EventLoopGroup 的一个浅拷贝, 所以, EventLoopGroup 会在所有克隆的 Channel 实例之间共享. 这是可行的, 因为通常这些克隆的 Channel 的生命周期都很短暂, 一个典型的场景是创建一个 Channel 以进行一次 HTTP 请求.

### 8.2.1 引导客户端

Bootstrap 类负责为客户端和使用无连接协议的应用程序创建 Channel.

![引导过程](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_8.2_引导过程.png)

### 8.3.2 引导服务器

![ServerBootstrap和ServerChannel](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_8.3_ServerBootstrap和ServerChannel.png)

## 8.4 从 Channel 引导客户端(代理服务器场景)

![在两个Channel之间共享EventLoop](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_8.4_在两个Channel之间共享EventLoop.png)

## 8.5 在引导过程中添加多个 ChannelHandler

一个必须要支持多种协议的应用程序将会有很多的 ChannelHandler, 但是,  如果在引导的过程中你只能设置一个 ChannelHandler, 那么你应该怎么做到这一点呢?

针对于这个场景, Netty 提供了一个特殊的 ChannelInboundHandlerAdapter 子类 ChannelInitializer, 它定义了下面的方法 initChannel, 该方法可以将多个 ChannelHandler 添加到一个 ChannelPipeline 中.

你只需要简单地向 Bootstrap 或 ServerBootstrap 的实例提供你的 ChannelInitializer 实现即可, 并且一旦 Channel 被注册到了它的 EventLoop 之后, 就会调用你的 initChannel() 方法. 在该方法返回之后, ChannelInitializer 的实例将会从 ChannelPipeline 中移除它自己.

## 8.8 关闭

你需要关闭 EventLoopGroup, 它将处理任何挂起的事件和任务, 并且随后释放所有活动的线程. 这就是调用 EventLoopGroup.shutdownGracefully() 方法. 这个方法调用将会返回一个 Future, 这个 Future 将在关闭完成时接收到通知. 需要注意的是, shutdownGracefully() 方法也是一个异步的操作, 所以你需要阻塞等待直到它完成, 或者向所返回的 Future 注册一个监听器以在关闭完成时获得通知.

或者, 你也可以在调用 EventLoopGroup.shutdownGracefully() 方法之前, 显式地在所有活动的 Channel 上调用 Channel.close() 方法. 但是在任何情况下, 都请记得关闭 EventLoopGroup 本身.

# 10 编解码器框架

网络只将数据看作是原始的字节序列, 而我们的应用程序则会把这些字节组
织成有意义的信息. 在数据和网络字节流之间做相互转换是最常见的编程任务
之一. 

将应用程序的数据转换为网络格式, 以及将网络格式转换为应用程序的数据的组件分别叫作`编码器`和`解码器`, 同时具有这两种功能的单一组件叫作`编解码器`.

## 10.2 解码器

* 将字节解码为消息 -- ByteToMessageDecoder 和 ReplayingDecoder;
* 将一种消息类型解码为另一种 -- MessageToMessageDecoder;

### 10.2.1 抽象类 ByteToMessageDecoder

由于你不可能知道远程节点是否会一次性地发送一个完整的消息, 所以这个类会对入站数据进行缓冲, 直到它准备好处理.

<p align="left"><font size=2>表 10-1 ByteToMessageDecoder API</font></p>

方法 | 描述
------ | ----
decode(ChannelHandlerContext ctx, ByteBuf in, List<Object> out) | 这是你必须实现的唯一抽象方法. decode() 方法被调用时将会传入一个包含了传入数据的 ByteBuf, 以及一个用来添加解码消息的 List. 对这个方法的调用将会重复进行, 直到确定没有新的元素被添加到该 List, 或者该 ByteBuf 中没有更多可读取的字节时为止. 然后, 如果该 List 不为空, 那么它的内容将会被传递给 ChannelPipeline 中的下一个 ChannelInboundHandler.
decodeLast(ChannelHandlerContext ctx, ByteBuf in, List<Object> out) | Netty提供的这个默认实现只是简单地调用了 decode() 方法. 当 Channel 的状态变为非活动时, 这个方法将会被调用一次. 可以重写该方法以提供特殊的处理.

![ToIntegerDecoder](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_10.1_ToIntegerDecoder.png)

> **编解码器中的引用计数**
正如我们在第 5 章和第 6 章中所提到的, 引用计数需要特别的注意. 对于编码器和解码器来说, 其过程也是相当的简单: 一旦消息被编码或者解码, 它就会被ReferenceCountUtil.release(message) 调用自动释放. 如果你需要保留引用以便稍后使用, 那么你可以调用 ReferenceCountUtil.retain(message) 方法. 这将会增加该引用计数, 从而防止该消息被释放.

### 10.2.2 抽象类 ReplayingDecoder

ReplayingDecoder 扩展了 ByteToMessageDecoder类, 使得我们在解码数据前不必调用 readableBytes() 方法进行长度检查. 它通过一个自定义的ByteBuf ReplayingDecoderByteBuf 包装传入的 ByteBuf 实现了这一点, ReplayingDecoderByteBuf 在内部执行长度检查.

<p align="left"><font size=2>代码清单 10-2 ToIntegerDecoder2 类扩展了 ReplayingDecoder</font></p>

``` java
    public class ToIntegerDecoder2 extends ReplayingDecoder<Void> {

        @Override
        // 传入的 ByteBuf 是 ReplayingDecoderByteBuf
        public void decode(ChannelHandlerContext ctx, ByteBuf in, List<Object> out)
                throws Exception {
            // 从入站 ByteBuf 中读取一个 int，并将其添加到解码消息的 List 中
            out.add(in.readInt());
        }
    }
```

### 10.2.3 抽象类 MessageToMessageDecoder

![IntegerToStringDecoder](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_10.2_IntegerToStringDecoder.png)

### 10.2.4 TooLongFrameException 类

由于 Netty 是一个异步框架, 所以需要在字节可以解码之前在内存中缓冲它们. 因此, 不能让解码器缓冲大量的数据以至于耗尽可用的内存. 为了解除这个常见的顾虑, Netty 提供了 TooLongFrameException 类, 其将由解码器在帧超出指定的大小限制时抛出.

### 10.3.1 抽象类 MessageToByteEncoder

这个类只有一个方法, 而解码器有两个. 原因是解码器通常需要在 Channel 关闭之后产生最后一个消息(因此也就有了 decodeLast()方法). 这显然不适用于
编码器的场景--在连接被关闭之后仍然产生一个消息是毫无意义.

![ShortToByteEncoder](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_10.3_ShortToByteEncoder.png)

### 10.3.2 抽象类 MessageToMessageEncoder

![IntegerToStringEncoder](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_10.4_IntegerToStringEncoder.png)

### 10.4.1 抽象类 ByteToMessageCodec

### 10.4.2 抽象类 MessageToMessageCodec

### 10.4.3 CombinedChannelDuplexHandler 类

结合一个解码器和编码器可能会对可重用性造成影响. 但是, 有一种方法既能够避免这种惩罚, 又不会牺牲将一个解码器和一个编码器作为一个单独的单元部署所带来的便利性. CombinedChannelDuplexHandler 提供了这个解决方案，其声明为:

``` java
public class CombinedChannelDuplexHandler
    <I extends ChannelInboundHandler, O extends ChannelOutboundHandler>
```

这个类充当了 ChannelInboundHandler 和 ChannelOutboundHandler(该类的类型参数 I 和 O)的容器. 通过提供分别继承了解码器类和编码器类的类型, 我们可以实现一个编解码器, 而又不必直接扩展抽象的编解码器类.

# 11 预置的 ChannelHandler 和编解码器

## 11.1 通过 SSL/TLS 保护 Netty 应用程序

![通过SslHandler进行解密和加密的数据流](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_11.1_通过SslHandler进行解密和加密的数据流.png)

> **Netty 的 OpenSSL/SSLEngine 实现**
Netty 还提供了使用 OpenSSL 工具包(www.openssl.org)的 SSLEngine 实现. 这个 OpenSslEngine 类提供了比 JDK 提供的 SSLEngine 实现更好的性能.

### 11.2.1 HTTP 解码器、编码器和编解码器

![HTTP请求的组成部分](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_11.2_HTTP请求的组成部分.png)

![HTTP响应的组成部分](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_11.3_HTTP响应的组成部分.png)

### 11.2.2 聚合 HTTP 消息

引入自动聚合机制只不过是向 ChannelPipeline 中添加另外一个 ChannelHandler 罢了.

### 11.2.3 HTTP 压缩

### 11.2.4 使用 HTTPS

只需要简单地将一个 ChannelHandler 添加到 ChannelPipeline 中, 便可以提供一项新功能, 甚至像加密这样重要的功能都能提供.

### 11.2.5 WebSocket

WebSocket 在一个 TCP 连接上提供双向的通信. WebSocket 现在可以用于传输任意类型的数据, 很像普通的套接字. 

图 11-4 给出了 WebSocket 协议的一般概念. 在这个场景下, 通信将作为普通的 HTTP 协议开始, 随后升级到双向的 WebSocket 协议. 要想向你的应用程序中添加对于 WebSocket 的支持, 你需要将适当的客户端或者服务器 WebSocket ChannelHandler 添加到 ChannelPipeline 中. 这个类将处理由 WebSocket 定义的称为帧的特殊消息类型. WebSocketFrame 可以被分为数据帧和控制帧.

![WebSocket协议](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_11.4_WebSocket协议.png)

### 11.3 空闲的连接和超时

检测空闲连接和超时是为了及时释放资源. 常见的方法发送消息用于测试一个不活跃的连接来, 通常称为 "心跳", 到远端来确定它是否还活着.

<p align="center"><font size=2>表 11-4 用于空闲连接以及超时的 ChannelHandler</font></p>

名称 | 描述
-----|----
IdleStateHandler | 如果连接闲置时间过长, Handler 会触发 IdleStateEvent 事件. 然后, 你可以在你的 ChannelInboundHandler 中重写 userEventTriggered() 方法来处理 IdleStateEvent 事件
ReadTimeoutHandler | 在指定的时间间隔内没有接收到入站数据则会抛出 ReadTimeoutException 并关闭 Channel. ReadTimeoutException 可以通过覆盖 ChannelHandler 的 exceptionCaught(…) 方法检测到.
WriteTimeoutHandler | 如果在指定的时间间隔内没有任何出站数据写入, 则抛出一个 WriteTimeoutException 并关闭对应的 Channel. WriteTimeoutException 可以通过覆盖 ChannelHandler 的 exceptionCaught(…) 方法检测到.

<p align="center"><font size=2>代码清单 11-7 发送心跳</font></p>

``` java
public class IdleStateHandlerInitializer extends ChannelInitializer<Channel> {

    @Override
    protected void initChannel(Channel ch) throws Exception {
        ChannelPipeline pipeline = ch.pipeline();
        // IdleStateHandler 将在被触发时发送一个 IdleStateEvent事件
        pipeline.addLast(new IdleStateHandler(0, 0, 60, TimeUnit.SECONDS));
        // 将一个 HeartbeatHandler 添加到 ChannelPipeline 中
        pipeline.addLast(new HeartbeatHandler());
    }

    public static final class HeartbeatHandler extends ChannelInboundHandlerAdapter {
        // 发送到远程节点的心跳消息
        private static final ByteBuf HEARTBEAT_SEQUENCE = Unpooled.unreleasableBuffer(
                Unpooled.copiedBuffer("HEARTBEAT", CharsetUtil.ISO_8859_1));  //2

        // 实现 userEventTriggered() 方法以发送心跳消息
        @Override
        public void userEventTriggered(ChannelHandlerContext ctx, Object evt) throws Exception {
            if (evt instanceof IdleStateEvent) {
                // 发送心跳消息, 并在发送失败时关闭该连接
                ctx.writeAndFlush(HEARTBEAT_SEQUENCE.duplicate())
                        .addListener(ChannelFutureListener.CLOSE_ON_FAILURE);
            } else {
                // 不是 IdleStateEvent 事件, 所以将它传递给下一个 ChannelInboundHandler
                super.userEventTriggered(ctx, evt);
            }
        }
    }
}
```

如果连接超过 60 秒没有接收或者发送任何的数据, 那么 IdleStateHandler 将会调用 fireUserEventTriggered() 方法发送 IdleStateEvent 事件. HeartbeatHandler 实现了 userEventTriggered() 方法, 如果这个方法检测到 IdleStateEvent 事件, 它将会发送心跳消息, 并且添加一个将在发送操作失败时关闭该连接的 ChannelFutureListener.

### 11.4.1 基于分隔符的协议

基于分隔符的(delimited)消息协议使用定义的字符来标记的消息或者消息段(通常被称为帧)的开头或者结尾.

<p align="center"><font size=2>表 11-5 用于处理基于分隔符的协议和基于长度的协议的解码器</font></p>

名称 | 描述
-----|----
DelimiterBasedFrameDecoder | 使用任何由用户提供的分隔符来提取帧的通用解码器
LineBasedFrameDecoder | 提取由行尾符(\n 或者 \r\n)分隔的帧的解码器. 这个解码器比 DelimiterBasedFrameDecoder 更快.

![由行尾符分隔的帧](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_11.5_由行尾符分隔的帧.png)

### 11.4.2 基于长度的协议

基于长度的协议通过将它的长度编码到帧的头部来定义帧, 而不是使用特殊的分隔符来标记它的结束.

<p align="center"><font size=2>表 11-6 用于基于长度的协议的解码器</font></p>

名称 | 描述
-----|----
FixedLengthFrameDecoder | 提取在调用构造函数时指定的定长帧
LengthFieldBasedFrameDecoder | 根据编码进帧头部中的长度值提取帧, 该字段的偏移量以及长度在构造函数中指定

![解码长度为8字节的帧](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_11.6_解码长度为8字节的帧.png)

你将经常会遇到被编码到消息头部的帧大小不是固定值的协议. 为了处理这种变长帧, 你可以使用 LengthFieldBasedFrameDecoder, 它将从头部字段确定帧长, 然后从数据流中提取指定的字节数.

![将变长帧大小编码进头部的消息](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_11.7_将变长帧大小编码进头部的消息.png)

## 11.5 写大型数据

由于写操作是非阻塞的, 所以即使没有写出所有的数据, 写操作也会在后续完成时返回并通知 ChannelFuture. 当这种情况发生时, 如果仍然不停地写入, 就有内存耗尽的风险. 所以在写大型数据时, 需要考虑到远程节点的连接是慢速连接的情况, 这种情况会导致内存释放的延迟.

在需要将数据从文件系统复制到用户内存中时, 可以使用 ChunkedWriteHandler, 它支持异步写大型数据流, 而又不会导致大量的内存消耗.

<p align="center"><font size=2>表 11-7 ChunkedInput 的实现</font></p>

名称 | 描述
-----|----
ChunkedFile | 从文件中逐块获取数据, 当你的平台不支持零拷贝或者你需要转换数据时使用
ChunkedNioFile | 与 ChunkedFile 类似, 处理使用了NIOFileChannel
ChunkedStream | 从 InputStream 中逐块传输内容
ChunkedNioStream | 从 ReadableByteChannel 中逐块传输内容

<p align="center"><font size=2>代码清单 11-12 使用 ChunkedStream 传输文件内容</font></p>

``` java
    public class ChunkedWriteHandlerInitializer extends ChannelInitializer<Channel> {
        private final File file;
        private final SslContext sslCtx;

        public ChunkedWriteHandlerInitializer(File file, SslContext sslCtx) {
            this.file = file;
            this.sslCtx = sslCtx;
        }

        @Override
        protected void initChannel(Channel ch) throws Exception {
            ChannelPipeline pipeline = ch.pipeline();
            pipeline.addLast(new SslHandler(sslCtx.createEngine());
            // 添加 ChunkedWriteHandler 以处理作为ChunkedInput 传入的数据
            pipeline.addLast(new ChunkedWriteHandler());
            // 一旦连接建立, WriteStreamHandler 就开始写文件数据
            pipeline.addLast(new WriteStreamHandler());
        }

        public final class WriteStreamHandler extends ChannelInboundHandlerAdapter {

            @Override
            // 当连接建立时, channelActive() 方法将使用 ChunkedInput 写文件数据
            public void channelActive(ChannelHandlerContext ctx) throws Exception {
                super.channelActive(ctx);
                ctx.writeAndFlush(new ChunkedStream(new FileInputStream(file)));
            }
        }
    }
```

## 11.6 序列化数据

JDK 提供了 ObjectOutputStream 和 ObjectInputStream 通过网络将原始数据类型和 POJO 进行序列化和反序列化. API并不复杂, 可以应用到任何对象, 支持 java.io.Serializable 接口. 但它并不非常高效.

### 11.6.1 JDK 序列化

### 11.6.2 使用 JBoss Marshalling 进行序列化

### 11.6.3 通过 Protocol Buffers 序列化

# 12 WebSocket

## 12.2 我们的 WebSocket 示例应用程序

![WebSocket 应用程序逻辑](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_12.1_WebSocket应用程序逻辑.png)

## 12.3 添加 WebSocket 支持

在从标准的 HTTP 或者 HTTPS 协议切换到 WebSocket 时, 将会使用一种称为升级握手的机制. 因此, 使用 WebSocket 的应用程序将始终以 HTTP/S 作为开始, 然后再执行升级.

我们的应用程序将采用下面的约定: 如果被请求的 URL 以/ws 结尾, 那么我们将会把该协议升级为 WebSocket; 否则, 服务器将使用基本的 HTTP/S. 在连接已经升级完成之后, 所有数
据都将会使用 WebSocket 进行传输.

![服务器逻辑](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_12.2_服务器逻辑.png)

### 12.3.1 处理 HTTP 请求

<p align="center"><font size=2>代码清单 12-1 HTTPRequestHandler</font></p>

``` java
    // 扩展 SimpleChannelInboundHandler 以处理 FullHttpRequest 消息
    public class HttpRequestHandler extends SimpleChannelInboundHandler<FullHttpRequest> {
        private final String wsUri;
        private static final File INDEX;

        static {
            URL location = HttpRequestHandler.class.getProtectionDomain().getCodeSource().getLocation();
            try {
                String path = location.toURI() + "index.html";
                path = !path.contains("file:") ? path : path.substring(5);
                INDEX = new File(path);
            } catch (URISyntaxException e) {
                throw new IllegalStateException("Unable to locate index.html", e);
            }
        }

        public HttpRequestHandler(String wsUri) {
            this.wsUri = wsUri;
        }

        @Override
        public void channelRead0(ChannelHandlerContext ctx, FullHttpRequest request) throws Exception {
            if (wsUri.equalsIgnoreCase(request.getUri())) {
                // 如果请求了 WebSocket 协议升级, 则调用 retain() 方法增加引用计数防止被释放, 并将它传递给下一个 ChannelInboundHandler
                ctx.fireChannelRead(request.retain());
            } else {
                if (HttpHeaders.is100ContinueExpected(request)) {
                    // 处理 100 Continue 请求以符合 HTTP 1.1 规范
                    send100Continue(ctx);
                }

                // 读取 index.html
                RandomAccessFile file = new RandomAccessFile(INDEX, "r");

                HttpResponse response = new DefaultHttpResponse(request.getProtocolVersion(), HttpResponseStatus.OK);
                response.headers().set(HttpHeaders.Names.CONTENT_TYPE, "text/html; charset=UTF-8");

                boolean keepAlive = HttpHeaders.isKeepAlive(request);

                if (keepAlive) {
                    // 如果请求了keep-alive, 则添加所需要的 HTTP 头信息
                    response.headers().set(HttpHeaders.Names.CONTENT_LENGTH, file.length());
                    response.headers().set(HttpHeaders.Names.CONNECTION, HttpHeaders.Values.KEEP_ALIVE);
                }

                // 将 HttpResponse 写到客户端
                ctx.write(response);

                if (ctx.pipeline().get(SslHandler.class) == null) {
                    // 将 index.html 写到客户端
                    // 如果不需要加密和压缩, 那么可以通过将 index.html 的内容存储到 DefaultFileRegion 中来达到最佳效率.
                    // 这将会利用零拷贝特性来进行内容的传输
                    ctx.write(new DefaultFileRegion(file.getChannel(), 0, file.length()));
                } else {
                    ctx.write(new ChunkedNioFile(file.getChannel()));
                }

                // 写LastHttpContent 并冲刷至客户端
                ChannelFuture future = ctx.writeAndFlush(LastHttpContent.EMPTY_LAST_CONTENT);
                if (!keepAlive) {
                    // 如果没有请求 keep-alive, 则在写操作完成后关闭 Channel
                    future.addListener(ChannelFutureListener.CLOSE);
                }
            }
        }

        private static void send100Continue(ChannelHandlerContext ctx) {
            FullHttpResponse response = new DefaultFullHttpResponse(HttpVersion.HTTP_1_1, HttpResponseStatus.CONTINUE);
            ctx.writeAndFlush(response);
        }

        @Override
        public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause)
                throws Exception {
            cause.printStackTrace();
            ctx.close();
        }
    }
```

### 12.3.2 处理 WebSocket 帧

<p align="center"><font size=2>表 12-1 WebSocketFrame 的类型</font></p>

帧类型 | 描述
----- | ----
BinaryWebSocketFrame  |  包含了二进制数据
TextWebSocketFrame  | 包含了文本数据
ContinuationWebSocketFrame  | 包含属于上一个 BinaryWebSocketFrame 或 TextWebSocketFrame 的文本数据或者二进制数据
CloseWebSocketFrame  | 表示一个 CLOSE 请求, 包含一个关闭的状态码和关闭的原因
PingWebSocketFrame  | 请求传输一个 PongWebSocketFrame
PongWebSocketFrame  | 作为一个对于 PingWebSocketFrame 的响应被发送

<p align="center"><font size=2>代码清单 12-2 处理文本帧</font></p>

``` java
    // 扩展 SimpleChannelInboundHandler, 并处理 TextWebSocketFrame 消息
    public class TextWebSocketFrameHandler extends SimpleChannelInboundHandler<TextWebSocketFrame> {
        private final ChannelGroup group;

        public TextWebSocketFrameHandler(ChannelGroup group) {
            this.group = group;
        }

        @Override
        // 重写 userEventTriggered() 方法以处理自定义事件
        public void userEventTriggered(ChannelHandlerContext ctx, Object evt) throws Exception {
            if (evt == WebSocketServerProtocolHandler.ServerHandshakeStateEvent.HANDSHAKE_COMPLETE) {
                // 如果该事件表示握手成功, 则从该 Channelipeline 中移除 HttpRequestHandler, 因为将不会接收到任何 HTTP 消息了
                ctx.pipeline().remove(HttpRequestHandler.class);
                // 通知所有已经连接的 WebSocket 客户端新的客户端已经连接上了
                group.writeAndFlush(new TextWebSocketFrame("Client " + ctx.channel() + " joined"));//4
                // 将新的 WebSocket Channel 添加到 ChannelGroup 中, 以便它可以接收到所有的消息
                group.add(ctx.channel());
            } else {
                super.userEventTriggered(ctx, evt);
            }
        }

        @Override
        public void channelRead0(ChannelHandlerContext ctx, TextWebSocketFrame msg) throws Exception {
            // 增加消息的引用计数, 并将它写到 ChannelGroup 中所有已经连接的客户端
            group.writeAndFlush(msg.retain());
        }
    }
```

和之前一样, 对于 retain() 方法的调用是必需的, 因为当 channelRead0() 方法返回时, TextWebSocketFrame 的引用计数将会减少. 由于所有的操作都是异步的, 因此, writeAndFlush() 方法可能会在 channelRead0() 方法返回之后完成, 有可能访问一个已经失效的引用.

### 12.3.3 初始化 ChannelPipeline

<p align="center"><font size=2>代码清单 12-3 初始化 ChannelPipeline</font></p>

``` java
    public class ChatServerInitializer extends ChannelInitializer<Channel> {
        private final ChannelGroup group;

        public ChatServerInitializer(ChannelGroup group) {
            this.group = group;
        }

        @Override
        protected void initChannel(Channel ch) throws Exception {
            // 将所有需要的 ChannelHandler 添加到 ChannelPipeline 中
            ChannelPipeline pipeline = ch.pipeline();
            pipeline.addLast(new HttpServerCodec());
            pipeline.addLast(new HttpObjectAggregator(64 * 1024));
            pipeline.addLast(new ChunkedWriteHandler());
            pipeline.addLast(new HttpRequestHandler("/ws"));
            pipeline.addLast(new WebSocketServerProtocolHandler("/ws"));
            pipeline.addLast(new TextWebSocketFrameHandler(group));
        }
    }
```

<p align="center"><font size=2>表 12-2 基于 WebSocket 聊天服务器的 ChannelHandler</font></p>

ChannelHandler　|　职责
-------------- | ----
HttpServerCodec | 将字节解码为 HttpRequest, HttpContent 和 LastHttpContent. 并将 HttpRequest, HttpContent 和 LastHttpContent 编码为字节
ChunkedWriteHandler | 写入一个文件的内容
HttpObjectAggregator | 将一个 HttpMessage 和跟随它的多个 HttpContent 聚合为单个 FullHttpRequest 或者 FullHttpResponse (取决于它是被用来处理请求还是响应). 安装了这个之后, ChannelPipeline 中的下一个 ChannelHandler 将只会收到完整的 HTTP 请求或响应
HttpRequestHandler | 处理 FullHttpRequest (那些不发送到 /ws URI 的请求)
WebSocketServerProtocolHandler | 按照 WebSocket 规范的要求, 处理 WebSocket 升级握手, PingWebSocketFrames, PongWebSocketFrames 和 CloseWebSocketFrames.
TextWebSocketFrameHandler | 处理 TextWebSocketFrame 和握手完成事件

Netty 的 WebSocketServerProtocolHandler 处理了所有委托管理的 WebSocket 帧类型以及升级握手本身. 如果握手成功, 那么所需的 ChannelHandler 将会被添加到 ChannelPipeline 中, 而那些不再需要的ChannelHandler 则将会被移除.

![WebSocket协议升级之前的ChannelPipeline](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_12.3_WebSocket协议升级之前的ChannelPipeline.png)

![WebSocket协议升级之后的ChannelPipeline](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_12.4_WebSocket协议升级之后的ChannelPipeline.png)

### 12.3.4 引导

<p align="left"><font size=2>代码清单 12-4 引导服务器</font></p>

``` java
    public class ChatServer {
        // 创建 DefaultChannelGroup, 其将保存所有已经连接的 WebSocket Channel
        private final ChannelGroup channelGroup = new DefaultChannelGroup(ImmediateEventExecutor.INSTANCE);
        private final EventLoopGroup group = new NioEventLoopGroup();
        private Channel channel;

        public ChannelFuture start(InetSocketAddress address) {
            ServerBootstrap bootstrap  = new ServerBootstrap();
            bootstrap.group(group)
                    .channel(NioServerSocketChannel.class)
                    .childHandler(createInitializer(channelGroup));
            ChannelFuture future = bootstrap.bind(address);
            future.syncUninterruptibly();
            channel = future.channel();
            return future;
        }

        protected ChannelInitializer<Channel> createInitializer(ChannelGroup group) {
           return new ChatServerInitializer(group);
        }

        // 处理服务器关闭, 并释放所有的资源
        public void destroy() {
            if (channel != null) {
                channel.close();
            }
            channelGroup.close();
            group.shutdownGracefully();
        }

        public static void main(String[] args) throws Exception{
            if (args.length != 1) {
                System.err.println("Please give port as argument");
                System.exit(1);
            }
            int port = Integer.parseInt(args[0]);

            final ChatServer endpoint = new ChatServer();
            ChannelFuture future = endpoint.start(new InetSocketAddress(port));

            Runtime.getRuntime().addShutdownHook(new Thread() {
                @Override
                public void run() {
                    endpoint.destroy();
                }
            });
            future.channel().closeFuture().syncUninterruptibly();
        }
    }
```

# 13 使用 UDP 广播事件

## 13.1 UDP 的基础知识

UDP 这样的无连接协议中并没有持久化连接这样的概念, 并且每个消息(一个 UDP 数据报)都是一个单独的传输单元. 此外, UDP 也没有 TCP 的纠错机制. TCP 连接就像打电话, 其中一系列的有序消息将会在两个方向上流动. 相反, UDP 则类似于往邮箱中投入一叠明信片. 你无法知道它们将以何种顺序到达它们的目的地, 或者它们是否所有的都能够到达它们的目的地.

## 13.3 UDP 示例应用程序

我们的示例程序将打开一个文件, 随后将会通过 UDP 把每一行都作为一个消息广播到一个指定的端口.

![广播系统概览](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_13.1_广播系统概览.png)

## 13.4 消息 POJO: LogEvent

在消息处理应用程序中, 数据通常由 POJO 表示, 除了实际上的消息内容, 其还可以包含配置或处理信息.

## 13.5 编写广播者

![通过DatagramPacket发送日志条目](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_13.2_通过DatagramPacket发送日志条目.png)

![ChannelPipeline和LogEvent事件流](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_13.3_ChannelPipeline和LogEvent事件流.png)

正如你所看到的, 所有的将要被传输的数据都被封装在了 LogEvent 消息中. LogEventBroadcaster 将把这些写入到 Channel 中, 并通过 ChannelPipeline 发送它们, 在那里它们将会被转换(编码)为 DatagramPacket 消息. 最后, 他们都将通过 UDP 被广播, 并由远程节点接收.

## 13.6 编写监视器

![LogEventMonitor](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_13.4_LogEventMonitor.png)

# 14 案例研究, 第一部分

### 14.1.2 Droplr 是怎样工作的

Droplr 拥有一个非常简单的工作流: 将一个文件拖动到应用程序的菜单栏图标, 然后 Droplr 将会上传该文件. 当上传完成之后, Droplr 将复制一个短 URL--也就是所谓的拖乐(drop)到剪贴板.

Droplr 原始方案:
1. 接收上传;
2. 上传到 S3;
3. 如果是图片, 则创建略缩图;
4. 应答客户端应用程序.

为了减少上传时间, 衍生出两个新方案.

方案 A:
1. 完整地接收文件;
2. 将文件保存到本地的文件系统，并立即返回成功到客户端;
3. 计划在将来的某个时间点将其上传到 S3.

方案 B:
实时地(流式地)将从客户端上传的数据直接管道给 S3

### 14.2.3 HTTP 1.1 keep-alive 和流水线化

通过 HTTP 1.1 keep-alive 特性, 可以在同一个连接上发送多个请求到服务器. 这使得 HTTP 流水线化 -- 可以发送新的请求而不必等待来自服务器的响应, 成为了可能. 实现对于 HTTP 流水线化以及 keep-alive 特性的支持通常是直截了当的, 但是当混入了长轮询之后, 它就明显变得更加复杂起来.

### 14.3.5 Netty 擅长管理大量的并发连接

Netty 使得可以轻松地在 JVM 平台上支持异步 I/O. 因为 Netty 运行在 JVM 之上, 并且因为 JVM 在 Linux 上将最终使用 Linux 的 epoll 方面的设施来管理套接字文件描述符中所感兴趣的事件(interest), 所以 Netty 使得开发者能够轻松地接受大量打开的套接字--每一个 Linux 进程将近一百万的 TCP 连接, 从而适应快速增长的移动设备的规模.

### 15.1.3 Nifty 服务器的设计

Java Thrift 的初始版本使用了 OIO 套接字, 并且服务器为每个活动连接都维护了一个线程. 使用这种设置, 在下一个响应被读取之前, 每个请求都将在同一个线程中被读取, 处理和应答. 这保证了响应将总会以对应的请求所到达的顺序返回.
较新的异步 I/O 的服务器实现诞生了, 其不需要每个连接一个线程, 而且这些服务器可以处理更多的并发连接, 但是客户端仍然主要使用同步 I/O, 因此服务器可以期望它在发送当前响应之前, 不会收到下一个请求. 这个请求/执行流如图 15-1 所示.

![同步的请求响应流](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_15.1_同步的请求响应流.png)

服务器可能会在它完成处理第一个请求之前, 从单个客户端读取多个请求, 此时为了保证顺序地处理同一个连接上的所有传入消息, 同时不会强制所有这些消息都在同一个执行器线程上运行, 可以使用 Netty 4 的 EventExecutor 或者 Netty 3.x 中的 OrderedMemoryAwareThreadPoolExcecutor.
图 15-2 展示了流水线化的请求是如何被以正确的顺序处理的, 这也就意味着对应于第一个请求的响应将会被首先返回, 然后是对应于第二个请求的响应, 以此类推.

![对于流水线化的请求的顺序化处理的请求响应流](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_15.2_对于流水线化的请求的顺序化处理的请求响应流.png)

我们希望允许来自于单个连接上的多个流水线化的请求的处理器能够被并行处理, 但是那样我们控制不了这些处理器完成的先后顺序, 此时我们使用了一种涉及缓冲响应的方案, 如果客户端要求响应保序, 我们将会缓冲提前完成的响应, 一旦所有较早的响应都已经完成, 我们将按照所要求的顺序将它们一起发送出去. 见图 15-3 所示.

![对于流水线化的请求的并行处理的请求响应流](https://raw.githubusercontent.com/21moons/memo/master/res/img/netty/Figure_15.3_对于流水线化的请求的并行处理的请求响应流.png)

### 15.1.4 Nifty 异步客户端的设计

1. 流水线化
**请求的流水线化**. 流水线化是一种在同一连接上发送多个请求, 而不需要等待其响应的能力.

2. 多路复用
随着我们的基础设施的增长, 我们开始看到在我们的服务器上建立起来了大量的连接. 多路复用(为所有来自于同一 Thrift 客户端的连接共享传输层通道)可以帮助减轻这种状况. 但是在需要按序响应的客户端连接上进行多路复用会导致问题, 解决方案是在发送每个消息时都捎带一个序列标识符, 客户端 Channel 维护一个从序列 ID 到响应处理器的一个映射.

