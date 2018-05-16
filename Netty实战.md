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




