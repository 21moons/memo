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
*  Channel 可用于写数据.

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
* 访问非堆缓冲区 ByteBuf 的数组会导致UnsupportedOperationException， 可以使用 ByteBuf.hasArray()来检查是否支持访问数组。
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
* read() 和 write() 操作, 从给定的索引开始, 并且会根据已经访问过的字节数递增当前的写索引或
读索引.

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

- Channel
- ChannelHandler
- ChannePipeline
- ChannelHandlerContext

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
















