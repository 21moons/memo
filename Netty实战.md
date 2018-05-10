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

<font color=#fd0209 size=6 >问题：这里提到的事件是谁发出的, socket 由谁来监视?,</font>



