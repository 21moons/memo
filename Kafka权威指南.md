# 1 初识 Kafka

数据管道

## 1.1 发布与订阅消息系统

特点:
1. 消息发送者不会直接把消息发送给接收者
2. 发布者会对消息进行分类
3. 一般有一个 broker, 发布者与接收者在互不感知的情况下通过 broker 中转消息

#### 消息系统的演化:

![发送方接收方直连场景](https://raw.githubusercontent.com/21moons/memo/master/res/img/kafka/Figure_1.1_发送方接收方直连场景.png)

![多对发送方接收方直连场景](https://raw.githubusercontent.com/21moons/memo/master/res/img/kafka/Figure_1.2_多对发送方接收方直连场景.jpg)

![度量指标发布与订阅系统](https://raw.githubusercontent.com/21moons/memo/master/res/img/kafka/Figure_1.3_度量指标发布与订阅系统.jpg)

![多个发布与订阅系统](https://raw.githubusercontent.com/21moons/memo/master/res/img/kafka/Figure_1.4_多个发布与订阅系统.jpg)

## 1.2 Kafka

Kafka 一般被称为 "分布式提交日志" 或 "分布式流平台". 文件系统或数据库通过日志来提供所有事务的持久化记录, 通过重放这些日志可以重建系统的状态. Kafka 的数据是按照一定顺序持久化保存的, 可以按需读取, 此外 Kafka 的数据分布在整个系统里, 具备数据故障保护和性能伸缩的能力.

### 1.2.1 消息与批次

Kafka 的数据单元被称为`消息`. 消息由字节数组组成, 可以有一个可选的元数据, 也就是`键`, 键同样是一个字节数组.
当消息以一种可控的方式写入不同的分区时, 会用到键, 最简单的例子就是为键生成一个一致性散列值, 然后使用散列值对主题分区数进行取模, 为消息选择分区. 这样可以保证具有相同键的消息总是被写到相同的分区.

为了提高写入效率, 消息被分`批次`写入 Kafka. 批次就是一组消息, 这些消息属于同一个主题和分区. 如果每一个消息都单独传输, 会导致大量的网络开销, 把消息分批次进行传输可以减少网络开销. 不过, 这需要在`时间延迟`和`吞吐量`之间作出权衡.

### 1.2.2 模式

模式(schema)用来定义消息结构, 让它们易于理解.
Kafka 的许多开发者喜欢使用 Apache Avro.

### 1.2.3 主题和分区

Kafka 的消息通过`主题`进行分类. 主题可以进一步分为若干个`分区`, 消息以追加的方式写入分区, 然会以先入先出的顺序读取.
要注意, 由于一个主题一般包含几个分区, 因此无法在整个主题范围内保证消息的顺序, 但可以保证消息在单个分区内的顺序.
Kafka 通过分区来实现数据冗余和伸缩性, 分区可以分布在不同的服务器上.

![包含多个分区的主题](https://raw.githubusercontent.com/21moons/memo/master/res/img/kafka/Figure_1.5_包含多个分区的主题.jpg)

### 1.2.4 生产者与消费者

生产者在默认情况下把消息均衡的分布到主题的所有分区上, 而并不关心特定消息会被写到哪个分区.

消费者通过检查消息的`偏移量`来区分已经读取过的消息.
消费者把每个分区最后读取的消息偏移量保存在 Zookeeper 或 Kafka 上, 如果消费者关闭或重启, 它的读取状态不会丢失.

多个消费者组成消费者群组, 会有一个或多个消费者共同读取一个主题, 群组保证每个分区只能被一个消费者使用.
消费者与分区之间的映射通常被称为消费者对分区的所有权关系.

![消费者群组从主题读取消息](https://raw.githubusercontent.com/21moons/memo/master/res/img/kafka/Figure_1.6_消费者群组从主题读取消息.jpg)

### 1.2.5 broker与集群

一个独立的 Kafka 服务器被称为 broker. broker 接收来自生产者的消息, 为消息设置偏移量, 并提交消息到磁盘保存.
根据特定的硬件及其性能特征, 单个 broker 可以轻松处理数千个分区以及每秒百万级的消息量.

broker 是集群的组成部分, 每个集群都有一个 broker 同时充当了`集群控制器`的角色.
控制器通过算法从 broken 中选出, 它负责将分区分配给 broker 和监控 broker 状态.
在集群中, 一个分区从属于一个 broker, 该 broker 被称为分区的首领. 一个分区可以分配给多个 broker, 这个时候会发生分区复制, 这种复制机制为分区提供了消息冗余, 防止节点失效导致的消息丢失.

![集群里的分区复制](https://raw.githubusercontent.com/21moons/memo/master/res/img/kafka/Figure_1.7_集群里的分区复制.jpg)

保留消息(在一定期限内)是 Kafka 的一个重要特性. Kafka broker 默认的消息保留策略是这样的, 要么保留一段时间, 要么保留到消息达到一定大小的字节数.

### 1.2.6 多集群

Kafka 的消息复制机制只能在单个集群里进行, 不能在多个集群之间进行.
Kafka 提供了一个叫做 MirrorMaker 的工具, 可以用它来实现集群间的消息复制.

![多数据中心架构](https://raw.githubusercontent.com/21moons/memo/master/res/img/kafka/Figure_1.8_多数据中心架构.jpg)

## 1.3 为什么选择 Kafka

* 支持多个生产者
* 支持多个消费者
* 基于磁盘的数据存储
每个主题可以单独设置保留规则
* 伸缩性
要提高集群的容错能力, 需要配置较高的复制系数.
* 高性能
* 生态系统

使用场景
* 用户活动跟踪
* 传递消息
* 度量指标和日志记录
* 提交日志
* 流处理

# 2 安装 Kafka

Kafka 使用 Zookeeper 来保存 broker, 主题和分区的元数据信息和消费者信息.

磁盘性能影响生产者, 内存容量影响消费者.

Kafka 对 Zookeeper 的延迟和超时比较敏感, 与 Zookeeper 群组之间的一个通信异常就可能导致 Kafka 服务器出现无法预测的行为.

# 3 Kafka 生产者--向 Kafka 写入数据

Kafka 可以作为消息队列, 消息总线还有数据存储平台. 不同的使用场景意味着不同的需求:
是否每个消息都很重要?
是否允许丢失一小部分消息?
消息重复是否可以接受?
是否有严格的延迟和吞吐量要求?

![Kafka生产者组件图](https://raw.githubusercontent.com/21moons/memo/master/res/img/kafka/Figure_3.1_Kafka生产者组件图.jpg)

## 3.2 创建 Kafka 生产者

发送消息主要有下面三种方式:
* 发送并忘记
* 同步发送
* 异步发送

``` java
    // 发送并忘记
    ProducerRecord<String, String> record = new ProducerRecord<>("CustomerCountry", "Precision Products", "France");

    try{
        producer.send(record);
    } catch (Exception e) {
        // 可能的异常包括, SerializationException(序列化失败), BufferExhaustedException 或 TimeoutException (缓冲区已满) 
        // InterruptException(发送线程被中断)
        e.printStackTrace();
    }
```

### 3.3.1 同步发送消息

``` java
    // 同步发送
    ProducerRecord<String, String> record = new ProducerRecord<>("CustomerCountry", "Precision Products", "France");

    try{
        // producer.send() 方法先返回一个 Future 对象, 然后调用 Future 对象的 get() 方法等待 Kafka 响应
        producer.send(record).get();
    } catch (Exception e) {
        e.printStackTrace();
    }
```

KafkaProducer 一般会发生两类错误, 一类是可重试错误, 这类错误可以通过重发消息来解决. 比如对于连接错误, 可以通过再次建立连接来解决, "no leader" 错误则可以通过重新为分区选举首领来解决. KafkaProducer 可以被配置成自动重试, 如果多次重试后仍无法解决问题, 应用程序会收到一个重试异常. 另一类错误无法通过重试解决, 比如 "消息太大" 异常, 对于这类错误, KafkaProducer 不会进行任何重试, 直接抛出异常.

### 3.3.1 异步发送消息

``` java
// 实现回调接口
private class DemoProducerCallback implements Callback {
    @Override
    public void onCompletion(RecordMetadata recordMetadata, Exception e){
        if(e != null){
            e.printStackTrace();
        }
    }
}

ProducerRecord<String, String> record = new ProducerRecord<>("CustomerCountry", "Biomedical Materials", "USA");

// 发送消息时传进去一个回调对象
producer.send(record, new DemoProducerCallback());
```

## 3.4 生产者的配置

1. acks
acks 参数指定了必须要有多少个分区副本收到消息, 生产者才会认为消息写入是成功的.
* acks=0, 生产者写入消息前不会等待任何来自服务器的响应, 强调消息的吞吐量
* acks=1, 只要集群的首领节点收到消息, 生产者就会收到一个来自服务器的成功响应.
* acks=all, 只有当所有参与复制的节点全部收到消息时, 生成者才会收到一个来自服务器的成功响应.延迟最高.

2. buffer.memory
设置生产者内存缓冲区的大小, 如果应用程序生成消息的速度超过发送消息的速度, 会导致生产者空间不足.

3. compression.type
消息压缩类型

4. retries
生产者可以重发消息的次数, 如果重发达到这个次数, 生产者会放弃重试并返回错误.
建议在设置重试次数和重试时间间隔之前, 先测试一下恢复一个崩溃节点需要多少时间, 让总的重试时间比 Kafka 集群的崩溃恢复时间要长, 否则生产者会过早的放弃重试.

5. batch.size
当有多个消息需要被发送到同一个分区时, 生产者会把它们放在同一个批次里. 该参数制定了一个批次可以使用的内存大小, 按照字节数计算.

6. linger.ms
该参数制定了生产者在发送批次之前等待更多消息加入批次的时间. KafkaProducer 会在批次填满或 linger.ms 达到上限时把批次发送出去.
可以通过增加 linger.ms 的值, 从而牺牲延迟来增加吞吐量.

7. client.id
标识消息来源

8. max.in.flight.requests.per.connection
参数指定了生产者在收到服务器响应之前可以发送多少个消息.
它的值越高, 就会占用越多的内存, 不过也会提升吞吐量.

9. timeout.ms,  request.timeout.ms 和 metadata.fetch.timeout.ms
request.timeout.ms 指定了生产者在发送数据时等待服务器返回响应的时间, metadata.fetch.timeout.ms 指定了生产者在获取元数据(比如目标分区的首领是谁)时等待服务器返回响应的时间. 如果等待响应超时, 要么生产者重试发送数据, 要么返回一个错误(抛出异常或执行回调). timeout.ms 指定了 broker 等待同步副本返回消息确认的时间, 与 acks 的配置相匹配--如果在指定时间内没有收到同步副本的确认, 那么 broker 就会返回一个错误.

10. max.block.ms
该参数指定了在调用 send() 方法或使用 partitionsFor() 方法获取元数据时生产者的阻塞时间. 当生产者的发送缓冲区已满， 或者没有可用的元数据时, 这些方法就会阻塞. 在阻塞时间达到 max.block.ms 时, 生产者会抛出超时异常.

11. max.request.size
参数用于控制生产者发送的请求大小. 另外, broker 对可接收的消息最大值也有自己的限制(message.max.bytes), 所以两边的配置最好可以匹配, 避免生产者发送的消息被broker 拒绝.

12. receive.buffer.bytes 和 send.buffer.bytes
这两个参数分别指定了 TCP socket 接收和发送数据包的缓冲区大小. 如果它们被设为－1, 就使用操作系统的默认值. 如果生产者或消费者与 broker 处于不同的数据中心, 那么可以适当增大这些值, 因为跨数据中心的网络一般都有比较高的延迟和比较低的带宽.

>顺序保证
Kafka 可以保证同一个分区里的消息时有序的. 也就是说, 如果生产者按照一定的顺序发送消息, broker 就会按照这个顺序把它们写入分区, 消费者也会按照同样的顺序读取它们. 在某些情况下, 顺序是非常重要的. 例如, 往一个账户存入 1OO 元再取出来, 这个与先取钱再存钱是截然不同的! 不过, 有些场景对顺序不是很敏感. 
如果把 retries 设为非零整数, 同时把 max.in.flight.requests.per.connection 设为比 1 大的数, 那么, 如果第一个批次消息写入失败, 而第二个批次写入成功, broker 会重试写入第一个批次. 如果此时第一个批次也写入成功, 那么两个批次的顺序就反过来了.
一般来说, 如果某些场景要求消息是有序的, 那么消息是否写入成功也是很关键的, 所以不建议把 retries 设为 0. 可以把 max.in.flight.requests.per.connection 设为 1, 这样在生产者尝试发送第一批消息时, 就不会有其他的消息发送给 broker. 不过这样会严重影响生产者的吞吐量, 所以只有在对消息的顺序有严格要求的情况下才能这么做.

## 3.5 序列化器

### 3.5.1 自定义序列化器

假设你创建了一个简单的类来表示一个客户:

``` java
public class Customer {
    private int customerID;
    private String customerName;

    public Customer(int ID, String name){
        this.customerID = ID;
        this.customerName = name;
    }

    public int getID(){
        return customerID;
    }

    public String getName(){
        return customerName;
    }
}
```

在我们要为这个类创建一个序列化器, 它看起来可能是这样的:

``` java
import org.apache.kafka.common.errors.SerializationException;

import java.nio.ByteBuffer;
import java.util.Map;

public class CustomerSerializer implements Serializer<Customer> {

    @Override
    public void configure(Map configs, boolean isKey){
        // 不做任何配置
    }

    @Override
    /* Customer 对象将被序列化成:
    表示 customeID 的 4 字节整数
    表示 customerName 长度的 4 字节整数(如果 customeName 为空, 则长度为0)
    表示 customerName 的 N 个字节
    */
    public byte[] serialize(String topic, Customer data){
        try{
            byte[] serializedName;
            int stringSize;

            if(data == null) return null;
            else {
                if(data.getName() != null){
                    serializedName = data.getName().getBytes("UTF-8");
                    stringSize = serializedName.length;
                }else{
                    serilizedName = new byte[0];
                    stringSize = 0;                }
            }

            ByteBuffer buffer = BuyeBuffer.allocate(4 + 4 + stringSize);
            buffer.putInt(data.getID());
            buffer.putInt(stringSize);
            buffer.put(serializedName);

            return buffer.array();
        } catch (Exception e) {
            throw new SerializationException("Error when serializing Customer to byte[]" + e);
        }

    }

    @Override
    public void close(){
        // 不需要关闭任何东西
    }
}
```

### 3.5.2 使用 Avro 序列化

Apache Avro 是一种与编程语言无关的序列化格式.

Avro 数据通过与语言无关的 schena 来定义. schema 通过 JSON 来描述, 数据被序列化成二进制文件或 JSON 文件, 不过一般会使用二进制文件. Avro 在读写文件时需要用到 schema, `schema 一般会被内嵌在数据文件里`.

Avro 有一个很有意思的特性是, 当负责写消息的应用程序使用了新的 schema, 负责读消息的应用程序可以继续处理消息而无需做任何改动.

假设有这样一个 schema:

{
    "namespace": "customerManagement.avro",
    "type": "record",
    "name": "Customer",
    "fields": [
        {"name": "id", "type" : "int"},
        {"name": "name", "type" : "string"},
        {"name": "faxNumber", "type" : ["null", "string"], "default": "null"}
    ]
}

id 和 name 字段是必须的, faxNumber 字段是可选的, 默认为 null.

在新版 schema 中, 我们将 faxNumer 字段改为 email 字段
{
    "namespace": "customerManagement.avro",
    "type": "record",
    "name": "Customer",
    "fields": [
        {"name": "id", "type" : "int"},
        {"name": "name", "type" : "string"},
        {"name": "email", "type" : ["null", "string"], "default": "null"}
    ]
}

使用 Avro 的好处是: 在我们修改了消息的 schema 后, 但没有在读取方更新 schema, 而这样只会导致某个字段为空, 不会出现异常或阻断性错误, 也不需要对现有数据进行大幅更新. 不过这里有以下两个需要注意的地方:
* 用于写入数据和读取数据的 schema 必须是相互兼容的, 这里的兼容是指满足一些兼容性原则.
* 反序列化器需要用到已经写入数据的 schema, 即使它可能与用于读取数据的 schema 不一样. Avro 数据文件里包含了用于写入数据的 schema, 不过在 Kafk里有一种更好的处理方式, 下一小节我们会介绍它.

### 3.5.3 在 Kafka 里使用 Avro

Avro 的数据文件里包含了整个 schema, 但是 Kafka 要处理成千上万的消息, 这样的开销是不可接受的. 为了解决这个问题, 我们通常用 "schema 注册表" 来达到目的. 我们把所有写人数据需要用到的 schema 保存在注册表里, 然后在记录里引用 schema 的标识符. 负责读取数据的应用程序使用标识符从注册表里拉取 schema 来反序列化记录. 序列化器和反序列化器分别负责处理 schema 的注册和拉取.

## 3.6 分区

键有两个用途: 可以作为消息的附加信息, 也可以用来决定消息该被写到主题的哪个分区. 拥有相同键的悄息将被写到同一个分区. 也就是说, 如果一个进程只从一个主题的分区读取数据(第 4 章会介绍更多细节), 那么具有相同键的所有记录都会被该进程读取.

如果键值为 null, 井且使用了默认的分区器, 那么记录将被随机地发送到主题内各个可用的分区上. 分区器使用轮询(RoundRobin)算法将消息均衡地分布到各个分区上.

如果键不为空, 并且使用了默认的分区器, 那么 Kafka 会对键进行散列(使用 Kafka 自己的散列算法, 即使升级 Java 版本, 散列值也不会发生变化), 然后根据散列值把消息映射到特定的分区上. 这里的关键之处在于, 同一个键总是被映射到同一个分区上, 所以在进行映射时, 我们会使用主题所有的分区, 而不仅仅是可用的分区. 这也意味着, 如果写入数据的分区是不可用的, 那么就会发生错误. 但这种情况很少发生, 我们将在第 6 章讨论 Kafka 的复制功能和可用性.

只有在不改变主题分区数量的情况下, 键与分区之间的映射才能保持不变. 举个例子, 在分区数量保持不变的情况下, 可以保证用户 045189 的记录总是被写到分区 34. 在从分区读取数据时, 可以进行各种优化. 不过, 一旦主题增加了新的分区, 这些就无陆保证了--旧数据仍然留在分区 34, 但新的记录可能被写到其他分区上. 如果要使用键来映射分区, 那么最好在创建主题的时候就把分区规划好(第 2 章介绍了如何确定合适的分区数量), 而且永远不要增加新分区.

支持实现自定义分区策略, 分区最重要的一个原则是数据在分区之间的分布均衡.

# 4 Kafka 消费者--从 Kafka 读取数据

### 4.1.1 消费者和消费者群组

假设我们有一个应用程序需要从－个 Kafka 主题读取消息井验证这些消息, 然后再把它们保存起来. 应用程序需要创建一个消费者对象, 订阅主题并开始接收消息, 然后验证消息井保存结果. 过了一阵子, 生产者往主题写入消息的速度超过了应用程序验证数据的速度, 这个时候该怎么办? 如果只使用单个消费者处理消息, 应用程序会远跟不上消息生成的速度. 显然, 此时很有必要对消费者进行横向伸缩. 就像多个生产者可以向相同的主题写入消息一样, 我们也可以使用多个消费者从同一个主题读取消息, 对消息进行分流.

Kafka 消费者从属于`消费者群组`. 一个群组里的消费者订阅的是同－个`主题`, 每个消费者接收主题`一部分分区`的消息.

![1个消费者收到4个分区的消息](https://raw.githubusercontent.com/21moons/memo/master/res/img/kafka/Figure_4.1_1个消费者收到4个分区的消息.jpg)

![2个消费者收到4个分区的消息](https://raw.githubusercontent.com/21moons/memo/master/res/img/kafka/Figure_4.2_2个消费者收到4个分区的消息.jpg)

![4个消费者收到4个分区的消息](https://raw.githubusercontent.com/21moons/memo/master/res/img/kafka/Figure_4.3_4个消费者收到4个分区的消息.jpg)

![5个消费者收到4个分区的消息](https://raw.githubusercontent.com/21moons/memo/master/res/img/kafka/Figure_4.4_5个消费者收到4个分区的消息.jpg)

![两个消费者群组对应一个主题](https://raw.githubusercontent.com/21moons/memo/master/res/img/kafka/Figure_4.5_两个消费者群组对应一个主题.jpg)






