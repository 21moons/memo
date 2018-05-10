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

```flow
op1=>operation: Socket
op2=>operation: 读/写
op3=>operation: Thread
op1->op2->op3
```

### 1.1.1 Java NIO

### 1.1.2 选择器

```flow
op1=>operation: Socket
op2=>operation: 读/写
op3=>operation: Selector
op4=>operation: Thread
op1->op2->op3->op4
```

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

<font color=#fd0209 size=6 >问题：这里提到的事件是谁发出的, socket 由谁来监视?</font>

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

### 1.3.5 把它们放在一起

**Future, 回调和 ChannelHandler**
Netty 的异步编程模型是建立在 Future 和回调的概念之上的, 而将事件派发到 ChannelHandler 的方法则发生在更深的层次上(操作系统层面?). 
拦截操作以及高速地转换入站数据和出站数据, 都只需要你提供回调或者利用操作所返回的 Future. 这使得链接操作变得既简单又高效, 并且促进了可重用的通用代码的编写.

<font color=#fd0209 size=6 >问题：什么场景下只提供回调, 什么场景下利用操作所返回的 Future?</font>

**选择器, 事件和 EventLoop**
Netty 通过触发事件将 Selector 从应用程序中抽象出来, 消除了所有本来将需要手动编写的派发代码. 在内部, 将会为每个 Channel 分配一个 EventLoop, 用以处理所有事件, 包括:
- 注册感兴趣的事件;
- 将事件派发给 ChannelHandler;
- 安排进一步的动作.

EventLoop 本身只由一个线程驱动, 其处理了一个 Channel 的所有 I/O 事件, 并且在该 EventLoop 的整个生命周期内都不会改变. 这个简单而强大的设计消除了你可能有的在 ChannelHandler 实现中需要进行同步的任何顾虑, 因此, 你可以专注于提供正确的逻辑.

<font color=#fd0209 size=6 >问题：Channel 可以共用 EventLoop 吗?</font>

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
