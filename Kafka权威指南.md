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

如果我们往群组里添加更多的消费者, 超过主题的分区数量, 那么有一部分消费者就会被闲置, 不会接收到任何消息, 如:

![5个消费者收到4个分区的消息](https://raw.githubusercontent.com/21moons/memo/master/res/img/kafka/Figure_4.4_5个消费者收到4个分区的消息.jpg)

![两个消费者群组对应一个主题](https://raw.githubusercontent.com/21moons/memo/master/res/img/kafka/Figure_4.5_两个消费者群组对应一个主题.jpg)

Kafka 消费者经常会做一些高延迟的操作, 比如把数据写到数据库或 HDFS, 或者使用数据进行比较耗时的计算. 在这些情况下, 单个消费者无法跟上数据生成的速度, 所以可以增加更多的消费者, 让它们分担负载, 每个消费者只处理部分分区的消息, 这就是横向伸缩的主要手段. 我们有必要为主题创建大量的分区, 在负载增长时可以加入更多的消费者. 不过要注意, 不要让消费者的数量超过主题分区的数量, 因为多余的消费者只会被闲置. 不同于传统的消息系统, 横向伸缩 Kafka 消费者和消费者群组并不会对性能造成负面影响.

简而言之, 可以为每一个需要获取一个或多个主题全部消息的应用程序创建一个消费者群组, 然后往群组里添加消费者来伸缩读取能力和处理能力, 群组里的每个消费者只处理一部分消息.

### 4.1.2 消费者群组和分区再平衡
分区的所有权从一个消费者转移到另一个消费者, 这样的行为被称为`再均衡`.

消费者通过向被指派为群组协调器的 broker(不同的群组可以有不同的协调器)发送心跳来维持它们和群组的从属关系以及它们对分区的所有权关系. 只要消费者以正常的时间间隔发送心跳, 就被认为是活跃的, 说明它还在读取分区里的消息. 消费者会在轮询消息或提交偏移量时发送心跳. 如果消费者停止发送心跳的时间足够长, 会话就会过期, 群组协调器认为它已经死亡, 就会触发一次再均衡.

>分配分区是怎样的一个过程
当消费者要加入群组时, 它会向群组协调器发送一个 JoinGroup 请求. 第一个加入群组的消费者将成为 "群主". 群主从协调器那里获得群组的成员列表(列表中包含了所有最近发送过心跳的消费者, 它们被认为是活跃的), 并负责给每一个消费者分配分区. 它使用一个实现了 PartitionAssigno 接口的类来决定哪些分区应该被分配给哪个消费者. Kafka 内置了两种分配策略, 在后面的配置参数小节我们将深入讨论. 分配完毕之后, 群主把分配情况列表发送给群组协调器, 协调器再把这些信息发送给所有消费者. 每个消费者只能看到自己的分配信息, 只有群主知道群组里所有消费者的分配信息. 这个过程会在每次再均衡时重复发生.

## 4.2 创建 Kafka 消费者

## 4.3 订阅主题

## 4.4 轮询

消息轮询是消费者 API 的核心, 通过一个简单的轮询向服务器请求数据. 一旦消费者订阅了主题, 轮询就会处理所有的细节, 包括群组协调, 分区再均衡, 发送心跳和获取数据.

``` java
    try{
        // 这是一个无限循环. 消费者实际上是一个长期运行的应用程序, 它通过持续轮询向 Kafka请求数据
        while(true) {
            // 这一行代码非常重要. 就像鲨鱼停止移动就会死掉一样, 消费者必须持续对 Kafka 进行轮询, 否则会被认为己经死亡, 它的分区会被移交给群组里的其他消费者. 传给 poll() 方法的参数是一个超时时间, 用于控制 poll() 方法的阻塞时间. 如果该参数被设为 0, poll() 会立即返回, 否则它会在指定的毫秒数内一直等待 broker 返回数据.
            ConsumerRecords<String, String> records = consumer.poll(100);
            //  poll() 方法返回一个记录列表. 每条记录都包含了记录所属主题的信息, 记录所在分区的信息, 记录在分区里的偏移量, 以及记录的键值对. 我们一般会遍历这个列表, 逐条处理这些记录.
            for (ConsumerRecord<String, String> record : records)
            {
                log.debug("topic = %s, partition = s%, offset = d%, customer = %s, country = %s\n",
                record.topic(), record.partition(), record.offset(), record.key(), record.value());

                int updatedCount = 1;
                if(custCountryMap.countainsValue(record.value())){
                    updatedCount = custCountryMap.get(record.value()) + 1;
                }
                custCountryMap.put(record.value(), updatedCount);

                JSONObject json = new JSONObject(custCountryMap);
                system.out.println(json.toString(4));
            }
        }
    } finally {
        // 退出应用程序之前使用 close() 方法关闭消费者, 网络连接和 socket 也会随之关闭, 并立即触发一次再均衡, 而不是等待群组协调器发现它不再发送心跳井认定它已死亡再出发再均衡
        consumer.close();
    }
```

轮询不只是获取数据那么简单. 轮询过程中第一次调用新消费者的 poll() 方法时, 它会负责查找群组协调器, 然后加入群组, 接受分配的分区. 如果发生了再均衡, 整个过程也是在轮询期间进行的. 当然, 心跳也是从轮询里发迭出去的. 所以, 我们要确保在轮询期间所做的任何处理工作都应该尽快完成, 避免轮询阻塞.

>线程安全
在同一个群组里, 我们无法让一个线程运行多个消费者, 也无法让多个结程安全地共享一个消费者. 按照规则, 一个消费者使用一个线程. 如果要在同一个消费者群组里运行多个消费者, 需要让每个消费者运行在自己的线程里. 最好是把消费者的逻辑封装在自己的对象里, 然后使用 Java 的 ExecutorS ervice 启动多个线程, 使每个消费者运行在自己的线程上.

## 4.5 消费者的配置

1. fetch.min.bytes
该属性指定了消费者从服务器获取记录的最小字节数. broker 在收到消费者的数据请求时, 如果可用的数据量小于 fetch.min.bytes 指定的大小, 那么它会等到有足够的可用数据时才把它返回给消费者.

2. fetch.max.wait.ms
用于指定 broker 的等待时间, 默认是 500 ms. 如果没有足够的数据流入 Kafka, 消费者获取最小数据量的要求就得不到满足, 最终导致 500 ms的延迟.

3. max.partition.fetch.bytes
该属性指定了服务器从每个分区里返回给消费者的最大字节数.

4. session.timeout.ms
该属性指定了消费者在被认为死亡之前可以与服务器断开连接的时间, 默认是 3s.

5. auto.offset.reset
该属性指定了消费者在读取一个没有偏移量的分区或者偏移量无效的情况下(因消费者长时间失效, 包含偏移量的记录已经过时井被删除)该作何处理. 它的默认值是 latest, 意思是说, 在偏移量无效的情况下, 消费者将从最新的记录开始读取数据(在消费者启动之后生成的记录). 另一个值是 earlies t, 意思是说, 在偏移量无效的情况下, 消费者将从起始位置读取分区的记录.

6. enable.auto.commit
该属性指定了消费者是否自动提交偏移量, 默认值是 true.

7. partition.assignment.strategy
分区策略, 决定哪些分区应该被分配给哪个消费者.

8. client.id

9. max.poll.records
该属性用于控制单次调用 call() 方法能够返回的记录数量, 可以帮你控制在轮询里需要处理的数据量.

10. receive.buffer.bytes 和 send.buffer.bytes
socket 在读写数据时用到的 TCP 缓冲区也可以设置大小. 如果它们被设为 -1, 就使用操作系统的默认值. 如果生产者或消费者与 broker 处于不同的数据中心内, 可以适当增大这些值, 因为跨数据中心的网络一般都有比较高的延迟和比较低的带宽.

## 4.6 提交和偏移量

每次调用 poll() 方法, 它总是返回由生产者写入 Kafka 但还没有被消费者读取过的记录, 我们因此可以追踪到哪些记录是被群组里的哪个消费者读取的. 之前已经讨论过, Kafka 不会像其他 JMS 队列那样需要得到消费者的确认, 这是 Kafka 的一个独特之处, 反过来看, 消费者可以使用 Kafka 来追踪消息在分区里的位置(偏移量). 我们把更新分区当前位置的操作叫作`提交`.

消费者往一个叫作 ＿consumer _offset 的特殊主题发送消息, 消息里包含每个分区的偏移量. 如果消费者一直正常运行, 那么偏移量就没有什么用处. 不过, 如果消费者发生崩溃或者有新的消费者加入群组, 就会触发再均衡, 完成再均衡之后, 每个消费者可能分配到新的分区, 而不是之前处理的那个. 为了能够继续之前的工作, 消费者需要读取每个分区最后一次提交的偏移量, 然后从偏移量指定的地方继续进行处理.

如果提交的偏移量小于客户端处理的最后一个消息的偏移量, 那么处于两个偏移量之间的消息就会被重复处理.

![提交的偏移量小于客户端处理的最后一个消息的偏移量](https://raw.githubusercontent.com/21moons/memo/master/res/img/kafka/Figure_4.6_提交的偏移量小于客户端处理的最后一个消息的偏移量.jpg)

如果提交的偏移量大于客户端处理的最后一个消息的偏移量, 那么处于两个偏移量之间的消息将会丢失.

![提交的偏移量大于客户端处理的最后一个消息的偏移量](https://raw.githubusercontent.com/21moons/memo/master/res/img/kafka/Figure_4.7_提交的偏移量大于客户端处理的最后一个消息的偏移量.jpg)

### 4.6.1 自动提交

简单的提交方式是让悄费者自动提交偏移量. 如果 enable.auto.commit 被设为 true, 那么每过 5s, 消费者会自动把从 poll() 方法接收到的最大偏移量提交上去. 提交时间间隔由 auto.commit.interval.ms 控制, 默认值是 5s. 与消费者里的其他东西一样, 自动提交也是在轮询里进行的. 消费者每次在进行轮询时会检查是否该提交偏移量, 如果是, 那么就会提交从上一次轮询返回的偏移量.

自动提交是有弊端的, 假设我们使用默认的 5s 提交时间间隔, 在上次提交 3s 后发生了再均衡, 再均衡之后, 消费者从上次提交的偏移量位置开始读取消息. 这个时候偏移量已经落后了 3s, 所以在上次提交后到达的消息会被重复处理. 可以通过修改提交时间间隔来更频繁地进行偏移量提交, 减少重复的消息, 不过这种情况无法完全避免.

使用自动提交时, 每次调用轮询方法都会把上一次调用返回的偏移量提交上去, 它并不知道这些消息是否已经都被处理了, 所以在每次调用之前最好确保所有上次调用返回的消息都已经处理完毕(调用 close() 方法之前也会进行自动提交). 一般情况下不会有什么问题, 不过在处理异常或提前退出轮询时要格外小心.

### 4.6.2 提交当前偏移量

大部分开发者通过控制偏移量提交时间来消除丢失消息的可能性, 井在发生再均衡时减少重复消息的数量. 消费者 API 也提供了另一种提交偏移量的方式, 开发者选择时机提交当前偏移盘, 而不是基于固定的时间间隔.

将 auto.commit.offset 设为 false, 让应用程序决定何时提交偏移量. 使用 commitSync() 提交偏移量最简单也最可靠. 这个 API 会提交由 poll() 方法返回的最新偏移量, 提交成功后马上返回, 如果提交失败就抛出异常. 

要记住, commitSync() 提交的是 poll() 返回的最新偏移量, 所以在处理完所有记录后要确保调用了 commitSync(), 否则还是会有丢失消息的风险. 和自动提交一样, 如果提交前发生了再均衡, 从最近一批消息到发生再均衡之间的所有消息都将被重复处理.

``` java
    while(true){
        ConsumerRecords<String, String> records = consumer.poll(100);
        for (ConsumerRecord<String, String> record : records)
        {
            System.out.printf("topic = %s, partitiion = %s, offset = %d, customer = %s, country = %s\n",
                record.topic(), record.partition(), record.offset(), record.key(), record.value());
        }

        try{
            // 处理完当前批次的消息后, 调用 commitSync() 方法提交当前批次最新的偏移量
            consumer.commitSync();
        }catch (CommitFailedException e){
            // 只要没有发生不可恢复的错误, commitSync() 会一直尝试直至提交成功
            log.error("commit failed", e);
        }
    }
```

### 4.6.3 异步提交

手动提交的不足之处是在 broker 对提交请求作出回应之前, 应用程序会一直阻塞, 这样会限制应用程序的吞吐量. 我们可以通过降低提交频率来提升吞吐量, 但如果提交前发生了再均衡, 重复消息的数量会增加, 这个时候可以使用异步提交 API, 消费者只管发送提交请求, 无需等待 broker 的响应.

``` java
    while(true){
        ConsumerRecords<String, String> records = consumer.poll(100);
        for (ConsumerRecord<String, String> record : records)
        {
            System.out.printf("topic = %s, partitiion = %s, offset = %d, customer = %s, country = %s\n",
                record.topic(), record.partition(), record.offset(), record.key(), record.value());
        }

        consumer.commitAsync();
    }
```

成功提交或碰到无怯恢复的错误之前, commitSync() 会一直重试, 但是 commitAsync() 不会, 这也是 commitAsync() 不好的一个地方. 它之所以不进行重试, 是因为它是异步的, 在它收到服务器响应的时候, 可能有一个更大的偏移量已经提交成功. 假设我们发出一个请求用于提交偏移量 2000, 这个时候发生了短暂的通信问题, 服务器收不到请求, 自然也不会作出任何响应. 同时, 我们已经处理完了另外一批消息, 并成功提交了偏移量 3000. 如果 commitAsync() 重新尝试提交偏移量 2000 成功, 偏移量会从 3000 变成 2000. 这个时候如果发生再均衡, 就会出现重复消息. 

我们之所以提到这个问题的复杂性和提交顺序的重要性，是因为 commitAsync() 也支持回调, 在 broker 作出响应时会执行回调. 回调经常被用于记录提交错误或生成度量指标, 不过如果你要用它来进行重试, 一定要注意提交的顺序.

``` java
    while(true){
        ConsumerRecords<String, String> records = consumer.poll(100);
        for (ConsumerRecord<String, String> record : records)
        {
            System.out.printf("topic = %s, partitiion = %s, offset = %d, customer = %s, country = %s\n",
                record.topic(), record.partition(), record.offset(), record.key(), record.value());
        }

        consumer.commitAsync(new OffsetCommitCallback() {
            public void onComplete(Map<TopicPartition, OffsetAndMetadata> offsets, Exception e){
                if (e != null)
                    log.error("Commit failed for offsets {}", offsets, e);
            }
        });
    }
```

>重试异步提交
我们可以使用一个单调递增的序列号来维护异步提交的顺序. 在每次提交偏移量之后或在回调里提交偏移量时递增序列号. 在进行重试前, 先检查回调的序列号和即将提交的偏移量是否相等, 如果相等, 说明没有新的提交, 那么可以安全地进行重试. 如果序列号比较大, 说明有一个新的提交已经发送出去了, 应该停止重试.

### 4.6.4 同步和异步组合提交

一般情况下, 针对偶尔出现的提交失败, 不进行重试不会有太大问题, 因为如果提交失败是因为临时问题导致的, 那么后续的提交总会有成功的. 但如果这是发生在关闭消费者或再均衡前的最后一次提交, 就要确保能够提交成功. 因此, 在消费者关闭前一般会组合使用 commitAsync() 和 commitSync(). 它们的工作原理如下(后面讲到再均衡监听器时, 我们会讨论如何在发生再均衡前提交偏移量):

``` java
try{
    while(true){
        ConsumerRecords<String, String> records = consumer.poll(100);
        for (ConsumerRecord<String, String> record : records)
        {
            System.out.printf("topic = %s, partitiion = %s, offset = %d, customer = %s, country = %s\n",
                record.topic(), record.partition(), record.offset(), record.key(), record.value());
        }
        // 如果一切正常, 我们使用 commitAsync 方法来提交. 这样速度更快, 而且即使这次提交失败, 下一次提交很可能会成功
        consumer.commitAsync();
    }
} catch (Exception e) {
    log.error("Unexpected error", e);
} finally {
    try {
        // 如果直接关闭消费者, 就没有所谓的 "下一次提交" 了. commitSync 方法会一直重试, 直到提交成功或发生无法恢复的错误.
        consumer.commitSync();
    } finally {
        consumer.close();
    }
}
```

### 4.6.5 提交特定的偏移量

提交偏移量的频率与处理消息批次的频率是一样的. 但如果想要更频繁地提交出怎么办? 如果 poll() 方法返回一大批数据, 为了避免因再均衡引起的重复处理整批消息, 想要在批次未处理完就提交偏移量该怎么办? 这种情况无法通过直接调用 commitSync() 或 commitAsync() 来实现, 因为直接调用时提交的偏移量无法设置, 而此时该批次里的消息还没有全部处理完.

幸运的是, 消费者 API 允许在调用 commitSync() 和 commitAsync() 方法时传进去希望提交的分区和偏移量的 map. 假设你处理了批次中的一部分消息, 最后一个来自主题 "customers" 分区 3 的消息的偏移量是 5000, 你可以调用 commitSync() 方怯来提交它. 不过, 因为消费者可能不只读取一个分区, 你需要跟踪所有分区的偏移量, 所以从这个角度来说控制偏移量的提交会让代码变复杂.

``` java
    private Map<TopicPartitioin, OffsetAndMetaData> currentOffsets = new HashMap<>();
    int count = 0;

    ....

    while(true){
        ConsumerRecords<String, String> records = consumer.poll(100);
        for (ConsumerRecord<String, String> record : records)
        {
            System.out.printf("topic = %s, partitiion = %s, offset = %d, customer = %s, country = %s\n",
                record.topic(), record.partition(), record.offset(), record.key(), record.value());

            currentOffsets.put(new TopicPartition(record.topic(), record.partition()),
                new OffsetAndMetadata(record.offset() + 1, "no metadata"));

            if(count % 1000 == 0)
                consumer.commitAsync(currentOffsets, null);

            count++;
        }
        // 如果一切正常, 我们使用 commitAsync 方法来提交. 这样速度更快, 而且即使这次提交失败, 下一次提交很可能会成功
        consumer.commitAsync();
    }
```

## 4.7 再均衡监听器

在提交偏移量一节中提到过, 消费者在退出和进行分区再均衡之前, 会做一些清理工作. 

你会在消费者失去对一个分区的所有权之前提交最后一个已处理记录的偏移量, 如果消费者准备了一个缓冲区用于处理偶发的事件, 那么在失去分区所有权之前, 需要处理在缓冲区累积下来的记录. 你可能还需要关闭文件句柄, 数据库连接等.

在为消费者分配新分区或移除旧分区时, 可以通过消费者 API 执行一些应用程序代码, 在调用 subscribe() 方法时传进去一个 ConsumerRebalanceListener 实例就可以了. ConsumerRebalanceListener 有两个需要实现的方法.

1. public  void  onPartitionsRevoked(Collection<TopicPartition> partitions) 方法会在再均衡开始之前和消费者停止读取消息之后被调用. 如果在这里提交偏移量, 下一个接管分区的消费者就知道该从哪里开始读取了.

2. public  void  onPartitionsAssigned(Collection<TopicPartition> partitions) 方法会在重新分配分区之后和消者开始读取消息之前被调用.

下面的例子将演示如何在失去分区所有权之前通过 onPartitionsRevoked() 方法来提交偏移量. 在下一节, 我们会演示另一个同时使用了 onPartitionsAssigned() 方法的例子.

``` java
    private Map<TopicPartitioin, OffsetAndMetaData> currentOffsets = new HashMap<>();

    // 实现 ConsumerRebalanceListener 接口
    private class HandleRebalance implements ConsumerRebalanceListener {
        public void onPartitionsAssigned(Collection<TopicPartition> partitions)
        {
        }

        public void onPartitionsRevoked(Collection<TopicPartition> partitions){
            System.out.println("Lost partitions in rebalance. Committing current offsets:"
                + currentOffsets);
            // 发生再均衡, 在即将失去分区所有权时提交偏移量
            consumer.commitSync(currentOffsets);
        }
    }

    try {
        // 订阅分区时将 ConsumerRebalanceListener 对象一起传过去
        consumer.subcribe(topics, new HandleRebalance());

        while (true) {
            ConsumerRecords<String, String> records = consumer.poll(100);

            for(ConsumerRecord<String, String> record : records)
            {
                System.out.printf("topic = %s, partitiion = %s, offset = %d, customer = %s, country = %s\n",
                    record.topic(), record.partition(), record.offset(), record.key(), record.value());
                currentOffsets.put(new TopicPartition(record.topic(), record.partition()),
                    new OffsetAndMetadata(record.offset() + 1, "no metadata"));
            }

            consumer.commitAsync(currentOffsets, null);
        }

    } catch(WakeupException e){
        // 忽略异常, 正在关闭消费者
    } catch(Exception e){
        log.error("Unexpected error", e);
    } finally {
        try {
            consumer.commitSync(currentOffsets);
        } finally {
            consumer.close();
            System.out.println("Closed consumer and we are done");
        }
    }

```

## 4.8 从特定偏移量处开始处理记录

目前为止, 我们知道了如何使用 poll() 方法从各个分区的最近偏移量处开始处理消息. 不过, 有时候我们也需要从指定的偏移量处开始读取消息.

如果你想从分区的起始位置开始读取消息, 或者直接跳到分区的末尾开始读取消息, 可以使用 seekToBeginning(Collection<TopicPatition> tp) 和 seekToEnd(Collection<TopicPatition> tp) 这两个方法.

Kafka 也为我们提供了用于查找特定偏移量的 API. 它有很多用途, 比如向后回退几个消息或者向前跳过几个消息(对时间比较敏感的应用程序在处理滞后的情况下希望能够向前跳过若干个消息). 在使用 Kafka 以外的系统来存储偏移量时, 它将给我们带来更大的惊喜.

想象一下这样的场景: 应用程序从 Kafka 读取事件(可能是网站的用户点击事件流), 对它们进行处理(可能是使用自动程序清理点击操作井添加会话信息), 然后把结果保存到数据库, NoSQL 存储引擎或 Hadoop. 同时假设我们不想丢失任何数据, 也不想在数据库里多次保存相同的结果.

这种情况下, 消费者的代码可能是这样的:

``` java
    while(true){
        ConsumerRecords<String, String> records = consumer.poll(100);
        for (ConsumerRecord<String, String> record : records)
        {
            currentOffsets.put(new TopicPartition(record.topic(), record.partition()),
                new OffsetAndMetadata(record.offset() + 1));

            processRecord(record);
            storeRecordInDB(record);
            consumer.commitAsync(currentOffsets);
        }
    }
```

在这个例子里, 每处理一条记录就提交一次偏移量. 尽管如此, 在记录被保存到数据库之后以及偏移量被提交之前, 应用程序仍然有可能发生崩溃, 导致重复处理数据, 数据库里就会出现重复记录.

如果保存记录到数据库和提交偏移量可以在一个原子操作里完成, 就可以避免出现上述情况. 记录和偏移量要么一起完成, 要么都不完成. 但是, 记录是保存在数据库里而偏移量是提交到 Kafka 上, 无法实现原子操作(原子操作一般粒度都比较小且限制为本地操作, 如果对多个网络IO做原子操作则会导致整个系统长时间阻塞). 不过, 如果在同一个事务里把记录和偏移量都写到数据库里会怎样? 那么我们就可以数据库的事务机制, 记录和偏移量要么都成功, 要么都没有, 然后重新处理记录.

现在的问题是: 如果偏移量是保存在数据库里而不是 Kafka 里, 那么消费者在得到新分区时怎么知道该从哪里开始读取? 这个时候可以使用 seek() 方法. 在消费者启动或分配到新分区时, 可以使用 seek() 方法查找保存在数据库里的偏移量. 下面的例子大致说明了如何使用这个 API. 使用 ConsumerRebalanceListener 和 seek() 方法确保我们是从数据库里保存的偏移量所指定的位置开始处理消息的。

``` java
    public class SaveOffsetsOnRebalance implements ConsumerRebalanceListener {
        public void onPartitionsRevoked(Collection<TopicPartition> partitons){
            // 数据库事务
            commitDBTransaction();
        }

        public void onPartitionsAssigned(Collectino<TopicPartition> partitions){
            for(TopicPartition partition : partitions){
                // 获取偏移量
                consumer.seek(partition, getOffsetFromDB(partition));
            }
        }
    }

    // 订阅主题
    consumer.subscribe(topics, new SaveOffsetOnRebalance(consumer));
    // 消费者加入消费者群组, 并获取分配到的分区
    consumer.poll(0);

    for(TopicPartition partitioin : consumer.assignment())
        // 获取分区偏移量
        consumer.seek(partition, getOffsetFromDB(partition));

    while(true){
        ConsumerRecords<String, String> records = consumer.poll(100);

        for (ConsumerRecord<String, String> record : records)
        {
            processRecord(record);
            storeRecordInDB(record);
            // 更新数据库中保存的偏移量
            storeOffsetInDB(record.topic(), record.partition(), record.offset());
        }
        commitDBTransaction();
    }
```

## 4.9 如何退出

如果确定要退出循环, 需要通过另一个线程调用 consumer.wakeup() 方法. 如果循环运行在主线程里, 可以在 ShutdownHook 里调用该方法. 要记住, consumer.wakeup() 是消费者唯一一个可以从其他线程里安全调用的方法. 调用 consumer.wakeup() 可以退出 poll(), 并抛出 WakeupException 异常, 或者如果调用 cconsumer.wakeup() 时线程没有等待轮询, 那么异常将在下一轮调用 poll() 时抛出.我们不需要处理 WakeupException, 因为它只是用于跳出循环的一种标识. 不过, 在退出线程之前调用 consumer.close() 是很有必要的, 它会提交任何还没有提交的东西, 并向群组协调器发送消息, 告知自己要离开群组, 接下来就会马上触发再均衡, 而不必等待会话超时.

下面是运行在主线程上的消费者退出线程的代码.

``` java
Runtime.getRuntime().addShutdownHook(new Thread(){
    public void run(){
        System.out.println("Starting exit...");
        consumer.wakeup();

        try {
            mainTread.join();
        } catch (InterruptedException e){
            e.printStackTrace();
        }

    }
})

....

try {
    // 按下 Ctrl + C 键, 上面代码注册的关闭钩子函数会在退出时进行清理
    while(true){
        ConeumerRecords<String, String> records = movingAvg.consumer.poll(1000);
        System.out.println(System.currentTimeMillis() + " -- waiting for data --");

        for(ConsumerRecord<String, String> record : records) {
            System.out.printf("offset = %d, key = %s, value = %s\n", record.offset(),
                record.key(), record.value());
        }

        for(TopicPartition tp : consumer.assignment())
            System.out.println("Committing offset at position:" + consumer.position(tp));

        movingAvg.consumer.commitSync();
    }
} catch (WakeupExcetion e) {
    // 忽略关闭异常
} finally {
    consumer.close();
    System.out.println("Closed consumer and we are done");
}
```

## 4.10 反序列化器

消费者需要用反序列化器把从 Kafka 接收到的字节数组转换成 Java 对象. 在前面的例子里, 我们假设每个消息的键值对都是字符串, 所以我们使用了默认的 StringDeserializer.

生成消息使用的序列化器与读取悄息使用的反序列化器应该是一一对应的. 我们并不建议使用自定义序列化器和自定义反序列化器. 它们把生产者和消费者紧紧地耦合在一起, 井且很脆弱, 容易出错. 我们建议使用标准的消息格式, 比如 JSON, Thrift, Protobuf 或 Avro.

##  4.11 独立消费者--为什么以及怎样使用没有群组的消费者

有时候你可能只需要一个消费者从一个主题的所有分区或者某个特定的分区读取数据. 这个时候就不需要消费者群组和再均衡了, 只需要把主题或者分区分配给消费者, 然后开始读取消息井提交偏移量.

如果是这样的话, 就不需要订阅主题, 取而代之的是为自己分配分区. `一个消费者可以订阅主题(并加入消费者群组), 或者为自己分配分区, 但不能同时做这两件事情.` 下面的例子显示了一个消费者是如何为自己分配分区并从分区里读取消息的:

``` java
    List<PartitionInfo> partitionInfos = null;
    // 向集群请求可用的分区
    partitionInfos = consumer.partitionsFor("topic");

    if(partitionInfos != null) {
        for(PartitionInfo partition : partitionInfos)
            partitions.add(new TopicPartition(partition.topic(), partition,partitioin()));

        // 获取分区
        consumer.assign(partitions);

        while(true){
            ConsumerRecords<String, String> records = consumer.poll(1000);

            for(ConsumerRecord<String, String> record : records){
                System.out.println("topic = %s, partition = %s, offset = %d, customer = %s, country = %s\n", 
                    record.topic(), record.partition(), record.offset(), record.key(), record.value());
            }

            consumer.commitSync();
        }
    }
```

除了不会发生再均衡, 也不需要手动查找分区, 其他的看起来一切正常. 不过要记住, 如果主题增加了新的分区, 消费者并不会收到通知. 所以, 要么周期性地调用 consumer.partitionsFor() 方法来检查是否有新分区加入, 要么在添加新分区后重启应用程序.

# 5 深入 Kafka

* Kafka 如何进行复制;
* Kafka 如何处理来自生产者和消费者的请求;
* Kafka 的存储细节, 比如文件格式和索引.

## 5.1 集群成员关系

Kafka 使用 Zookeeper 来维护集群成员的信息. 每个 broker 都有一个唯一标识符, 这个标识符可以在配置文件里指定, 也可以自动生成. 在 broker 启动的时候, 它通过创建临时节点把自己的 ID 注册到 Zookeeper. Kafka 组件订阅 Zookeeper 的 /brokers/ids 路径(broker 在 Zookeeper 上的注册路径), 当有 broker 加入集群或退出集群时, 这些组件就可以获得通知.

在 broker 停机, 出现网络分区或长时间垃圾回收停顿时, broker 会从 Zookeeper 上断开连接, 此时 broker 在启动时创建的临时节点会自动从Zookeeper 上移除. 监听 broker 列表的 Kafka 组件会被告知该 broker 已移除. 

在关闭 broker 时, Zookeeper 上它对应的节点也会消失, 不过它的 ID 会继续存在于其他数据结构中. 例如, 主题的副本列表里就可能包含这些 ID. 在完全关闭一个 broker 之后, 如果使用相同的 ID 启动另一个全新的 broker, 它会立即加入集群, 并拥有与旧 broker 相同的分区和主题.

## 5.2 控制器

控制器其实就是一个 broker, 只不过它除了具有一般 broker 的功能之外, 还负责分区首领的选举. 集群里第一个启动的 broker 通过在 Zookeeper 里创建一个临时节点 /controller 让自己成为控制器. 其他 broker 在启动时也会尝试创建这个节点, 不过它们会收到一个"节点已存在"的异常, 然后 "意识" 到控制器节点已存在, 也就是说集群里已经有一个控制器了. 其他 broker 在控制器节点上创建 Zookeeper watch 对象, 这样它们就可以收到这个节点的变更通知. 这种方式可以确保集群里一次只有一个控制器存在.

**broker 控制器的选举**
如果控制器被关闭或者与 Zookeeper 断开连接, Zookeeper 上的临时节点就会消失. 集群里的其他 broker 通过 watch 对象得到控制器节点消失的通知, 它们会尝试让自己成为新的控制器. 第一个在 Zookeeper 里成功创建控制器节点的 broker 就会成为新的控制器, 然后其他节点会收到 "节点已存在" 的异常, 然后在新的控制器节点上再次创建 watch 对象. 每个新选出的控制器通过 Zookeeper 的条件递增操作获得一个全新的, 数值更大的controller epoch. 其他 broker 在知道当前 controller epoch 后, 如果收到由控制器发出的包含较旧 epocha(原首领因为网络或其他原因假死后复活) 的消息, 就会忽略它们.

**分区首领由控制器指定**
控制器发现一个 broker 已经离开集群(通过观察相关的 Zookeeper 路径), 它就知道, 那些首领刚好是在这个 broker 上的分区需要一个新首领. 控制器遍历这些分区, 并确定谁应该成为新首领(简单来说就是分区副本列表里的下一个副本), 然后向所有包含新首领或现有跟随者的 broker 发送请求. 该请求消息包含了谁是新的分区首领以及谁是分区跟随者的信息. 随后, 新首领开始处理来自生产者和消费者的请求, 而跟随者开始从新首领那里复制消息.

当控制器发现一个 broker 加入集群时, 它会使用 broker ID 来检查新加入的 broker 否包含现有分区的副本. 如果有, 控制器就把变更通知发送给新加入的 broker 和其他 broker,新 broker 上的副本开始从首领那里复制消息.

简而言之, Kafka 使用 Zookeeper 来选举控制器, 并在节点加入集群或退出集群时通知控制器. 控制器负责在节点加入或离开集群时进行分区首领选举. 控制器使用 epoch 来避免 "脑裂". "脑裂" 是指两个节点同时认为自己是当前的控制器.

## 5.3 复制

复制功能是 Kafka 架构的核心. 在 Kafka 的文档里, Kafka 把自己描述成 "一个分布式的, 可分区的, 可复制的提交日志服务". 复制之所以这么关键, 是因为它可以在个别节点失效时仍能保证 Kafka 的可用性和持久性. Kafka 使用主题来组织数据, 每个主题被分为若干个分区, 每个分区有多个副本. 那些副本被保存在 broker 上, 每个 broker可以保存成百上千个属于不同主题和分区的副本.

副本有以下两种类型:
* 首领副本
每个分区都有一个首领副本, 为了保证一致性, 所有生产者和消费者请求都会经过这个副本.
* 跟随者副本
首领以外的副本都是跟随副本. 跟随者副本不处理来自客户端的请求, 它们唯一的任务就是从首领那里复制消息, 保持与首领一致的状态. 如果首领发生崩溃, 其中的一个跟随者会被提升为新首领.

首领的另一个任务是搞清楚哪个跟随者的状态与自己是一致的. 跟随者为了保持与首领的状态一致, 在有新消息到达首领时会尝试从首领那里复制消息, 不过有各种原因会导致同步失败. 例如, 网络拥塞导致复制变慢, broker 发生崩横导致复制滞后, 直到重启 broker 后复制才会继续.

为了与首领保持同步, 跟随者会向首领发送获取数据的请求, 这种请求与悄费者为了读取消息而发送的请求是一样的. 请求消息里包含了跟随者想要获取消息的偏移量, 而且这些偏移量总是有序的.

一个跟随者副本先请求消息1, 接着请求消息2, 然后请求消息3, 在收到这 3 个请求的响应之前, 它是不会发送第 4 个请求消息的. 如果跟随者发送了请求消息4, 那么首领就知道它已经收到了前面 3 个请求的响应. 通过查看每个跟随者请求的最新偏移量, 首领就会知道每个跟随者复制的进度. 如果跟随者在 10s 内没有请求任何消息, 或者虽然在请求消息, 但在 10s 内没有请求最新的数据, 那么它就会被认为是`不同步`的. 如果一个副本无法与首领保持一致, 在首领发生失效时, 它就不可能成为新首领 -- 毕竟它没有包含全部的消息.

相反, 持续请求得到的最新消息的副本被称为`同步的副本`. 在首领发生失效时, 只有`同步副本`才有可能被选为新首领.

跟随者的正常不活跃时间, 以及判定副本是否为`不同步副本`的周期都是通过 replica.lag.time.max.ms 参数来配置的. 这个时间间隔直接影响着首领选举期间的客户端行为和数据保留机制. 我们将在第6章讨论可靠性保证, 到时候会深入讨论这个问题.

除了当前首领之外, 每个分区都有一个`首选首领` -- 创建主题时选定的首领就是分区的首选首领. 之所以把它叫作`首选首领`, 是因为在创建分区时, 需要尽量保证所有分区的首领在 broker 之间均衡分布(后面会介绍在 broker 上编排副本和首领的算法). 因为我们希望某个首选首领在成为真正的首领后, broker 之间能够负载均衡. 默认情况下, Kafka 的 auto.leader.rebalance.enable 被设为 true, 它会检查首选首领是不是当前首领, 如果不是, 并且首选首领所在的副本是同步的, 那么就会激活首领选举, 让首选首领成为当前首领.

## 5.4 处理请求

broker 的大部分工作是处理客户端, 分区副本和控制器发送给分区首领的请求. Kafka 提供了一个二进制协议(基于TCP), 指定了请求消息的格式以及 broker 如何对请求作出响应 -- 包括如何成功处理请求或处理请求过程中遇到的错误. 客户端发起连接并发送请求, broker 处理请求井作出响应. broker 按照请求到达的顺序来处理它们--这个特性让 Kafka 具有了消息队列的特性, 同时也保证了消息的存储也是有序的.

broker 会在它所监听的每一个端口上运行一个 Acceptor 线程, 这个线程会创建一个连接, 并把它交给 Processor 线程去处理. Processor 线程(也被叫作 "网络线程")的数量是可配置的. 网络线程负责从客户端获取请求消息, 把它们放进请求队列, 然后从晌应队列获取响应消息, 把它们发送给客户端. 图 5-1 为 Kafka 处理请求的内部流程. 

请求消息被放到请求队列后, IO 线程会负责处理它们. 下面是几种最常见的请求类型:
* 生产请求
生产者发送的请求, 它包含客户端要写入 broker 的消息.
* 获取请求
在消费者和跟随者副本需要从 broker 读取消息时发送的请求.

![Kafka处理请求的内部流程](https://raw.githubusercontent.com/21moons/memo/master/res/img/kafka/Figure_5.1_Kafka处理请求的内部流程.jpg)

生产请求和获取请求都必须发送给分区的首领副本. 如果 broker 收到一个针对特定分区的请求, 而该分区的首领在另一个 broker 上, 那么发送请求的客户端会收到一个 "非分区首领" 的错误响应. 当针对特定分区的获取请求被发送到一个不含有该分区首领的 broker 上, 也会出现同样的错误. Kafka 要求客户端自己保证把生产请求和获取请求发送到正确的 broker 上.

那么客户端怎么知道该往哪里发送请求呢?客户端使用了另一种请求类型, 也就是`元数据请求`. 这种请求包含了客户端感兴趣的主题列表. 服务器端的响应消息里指明了这些主题所包含的分区, 每个分区都有哪些副本, 以及哪个副本是首领. 元数据请求可以发送给任意一个 broker, 因为所有 broker 都缓存了这些信息. 

一般情况下, 客户端会把这些信息缓存起来, 并直接往目标 broker 上发送生产请求和获取请求. 它们需要通过定时发送元数据请求来刷新这些信息(刷新的时间间隔通过 metadata.max.age.ms 参数来配置), 从而知道元数据是否发生了变更 -- 比如, 在新 broker 加入集群时, 部分副本会被移动到新的 broker 上(如图 5-2 所示). 另外, 如果客户端收到 "非首领" 错误, 它会在尝试重发请求之前先刷新元数据, 因为这个错误说明了客户端正在使用过期的元数据信息, 之前的请求被发到了错误的 broker 上.

![客户端路由请求](https://raw.githubusercontent.com/21moons/memo/master/res/img/kafka/Figure_5.2_客户端路由请求.jpg)

### 5.4.1 生产请求

我们在第3章讨论如何配置生产者的时候, 提到过 acks 这个配置参数--该参数指定了需要多少个 broker 确认才可以认为一个消息写入是成功的.不同的配置对 "写入成功" 的界定是不一样的, 如果 acks=1, 那么只要首领收到消息就认为写入成功; 如果 acks=all, 那么需要所有同步副本收到消息才算写入成功; 如果 acks=0, 那么生产者在把消息发出去之后, 完全不需要等待 broker 的响应. 

包含首领副本的 broker 在收到生产请求时, 会对请求做一些验证.
* 发送数据的用户是否有主题写入权限?
* 请求里包含的 acks 值是否有效(只允许出现 0, 1 或 all)?
* 如果 acks=all, 是否消息已经被写入足够多的同步副本?(我们可以对 broker 进行配置, 如果同步副本的数量不足, broker 可以拒绝处理新消息. 在第6章介绍 Kafka 持久性和可靠性保证时, 我们会讨论更多这方面的细节.)

之后, 消息被写入本地磁盘. 在 Linux 系统上, 消息会被写到文件系统缓存里, Kafka 并不保证它们何时会被刷新到磁盘上. Kafka 不关心数据何时被写入磁盘, 它依赖复制功能来保证消息的持久性.

在消息被写入分区的首领之后, Kafka 开始检查 acks 配置参数--如果 acks 被设为 0 或 1, 那么 broker 立即返回响应; 如果 acks 被设为 0 l或 1, 那么请求会被保存在一个叫作`炼狱`的缓冲区里, 直到首领发现所有跟随者副本都复制了消息, 响应才会被返回给客户端.

### 5.4.2 获取请求

broker 处理获取请求的方式与处理生产请求的方式很相似. 客户端发送请求, 向 broker 请求主题分区里具有特定偏移量的消息, 类似于 "请把主题Test 分区 0 偏移量从 53 开始的消息以及主题 Test 分区 3 偏移量从 64 开始的消息发给我." 客户端还可以指定 broker 最多可以从一个分区里返回多少数据. 这个限制是非常重要的, 因为客户端需要为 broker 返回的数据分配足够的内存. 如果没有这个限制, broker 返回的大量数据有可能耗尽客户端的内存.

我们之前讨论过, 请求需要先到达指定的分区首领上, 客户端通过查询元数据来确保请求的路由是正确的. 首领在收到请求时, 它会先检查请求是否有效--比如指定的偏移量在分区上是否存在? 如果客户端请求的是已经被删除的数据, 或者请求的偏移量不存在, 那么 broker 将返回一个错误. 

如果请求的偏移量存在, broker 将按照客户端指定的数量上限从分区里读取消息, 再把消息返回给客户端. Kafka 使用`零拷贝技术`向客户端发送消息, 也就是说, Kafka 直接把消息从文件(更确切地说是 Linux 文件系统缓存)里发送到网络通道, 而不需要经过任何中间缓冲区. 这是 Kafka 与其他大部分数据库系统不一样的地方, 其他数据库在将数据发送给客户端之前会先把它们保存在本地缓存里. 这项技术避免了字节复制, 也不需要管理内存缓冲区, 从而获得更好的性能.

客户端除了可以设置 broken 返回数据的上限, 也可以设置下限. 例如, 如果把下限设置为 10KB, 就好像是在告诉 broken: "等到有 10 KB 数据的时候再把它们发送给我." 在主题消息流量不是很大的情况下, 这样可以减少 CPU 和网络开销. 客户端发送一个请求, broken 等到有足够的数据时才把它们返回给客户端, 然后客户端再发出请求, 而不是让客户端每隔几毫秒就发送一次请求, 每次只能得到很少的数据甚至没有数据.(如图 5-3 所示)对比这两种情况, 它们最终读取的数据总量是一样的, 但前者的来回传送次数更少, 因此开销也更小.

![broken延迟作出响应以便累积足够的数据](https://raw.githubusercontent.com/21moons/memo/master/res/img/kafka/Figure_5.3_broken延迟作出响应以便累积足够的数据.jpg)

当然, 我们也不会让客户端一直等待 broker 累积数据, 在等待了一段时间之后, 哪怕数据量不满足要求也会把可用的数据拿回来处理, 而不是一直等下去.

有意思的是, 并不是所有保存在分区首领上的数据都可以被客户端读取. 大部分客户端只能读已经被写入所有同步副本的消息(对于跟随者副本来说也是一样的, 如果不这样, 复制功能就无法工作). 分区首领知道每个消息会被复制到哪个副本上, 在消息还没有被写入所有同步副本之前, 是不会发送给消费者的--尝试获取这些消息的请求会得到空的响应而不是错误.

还没有拷贝足够多个副本的消息被认为是 "不安全" 的, 一旦首领发生崩溃, 另一个副本成为新首领(首领是从同步副本中选取, 但是有可能消息还没拷贝到同步副本时首领就崩溃了), 那么这些消息就丢失了. 如果我们允许消费者读取这些消息, 可能就会破坏一致性. 试想一个消费者读取并处理了这样的一个消息, 而对于另一个消费者来说这个消息并不存在(如果每个消费者读取的分区不重复, 是不会有这种错误的). 所以, 我们会等到所有同步副本复制了这些消息, 才允许消费者读取它们(如图 5-4 所示). 也意味着, 如果 broker 间的消息复制因为某些原因变慢, 那么消息到达消费者的时间也会随之变长(因为我们会先等待消息复制完毕). 延迟时间可通过参数 replica.lag.time.max.ms 来配置, 它指定了副本在复制消息时可被允许的最大延迟时间.

![消费者只能看到已经复制到ISR的消息](https://raw.githubusercontent.com/21moons/memo/master/res/img/kafka/Figure_5.4_消费者只能看到已经复制到ISR的消息.jpg)

## 5.5 物理存储

Kafka 的基本存储单元是分区, 分区的大小受到单个挂载点可用空间的限制. 在配置 Kafka 的时候, 管理员指定了一个用于存储分区的目录清单 -- 也就是 log.dirs 参数. 该参数一般会包含每个挂载点的目录.

### 5.5.1 分区分配

创建主题时, Kafka 首先会决定如何在 broker 间分配分区. 假设你有 6 个 broker, 打算创一个包含 10 个分区的主题, 并且复制系数为 3. 那么Kafka 一共会有 30 个分区副本, 们可以被分配给 6 个 broker. 在进行分区分配时, 我们要达到如下的目标.
* 在 broker 间平均的分布分区副本. 在这个例子里, 就是要保证每个 broker 可以分到 5 个副本.
* 确保每个分区的每个副本分布在不同的 broker 上. 假设分区 0 的首领副本在 broker 2 上, 那么可以把跟随者副本放在 broker 3 和 broker 4上, 但不能放在 broker 2 上, 也不能两个都放在 broker 3 上.
* 如果为 broker 指定了机架信息, 那么尽可能把每个分区的副本分配到不同机架上. 这样做是为了保证单个机架的不可用不会导致整体分区不可用.

为分区和副本选好合适的 broker 后, 接下来要决定这些分区应该使用 broker 上的哪个目录. 我们为每个分区分配单独的目录, 规则很简单: 计算每个目录里的分区数量, 新的分区总是被添加到分区数量最少的那个目录. 也就是说, 如果添加了一个新磁盘, 所有新的分区都会被创建到这个磁盘上. 因为在完成分配工作之前, 新磁盘的分区数量总是最少的.

>注意磁盘空间
要注意, 在为 broker 分配分区时并没有考虑可用空间和工作负载问题, 但在将分区映射到磁盘上时会考虑分区数量, 但不会不考虑分区大小. 也就是说, 如果有些 broker 的磁盘空间比其他 broker 要大, 就会导致有些分区异常大, 或者同一个 broker 上有大小不同的磁盘, 这些情况下在分配分区时要格外小心. 在后面的章节中, 我们会讨论 Kafka 管理员该如何解决这种 broker 负载不均衡的问题.

### 5.5.2 文件管理

保留数据是 Kafka 的一个基本特性, Kafka 不会一直保留数据, 也不会等到所有消费者都读取了消息之后才删除消息. 相反, Kafka 管理员为每个主题配置了数据保留期限, 规定数据被删除之前可以保留多长时间, 或者清理数据之前可以保留的数据量大小.

因为在一个大文件里查找和删除消息是很费时的, 也很容易出错, 所以我们把分区分成若干个片段. 默认情况下，每个`片段`包含 lGB 或一周的数据, 以较小的那个为准. broker 往分区写入数据时, 如果达到片段上限, 就关闭当前文件, 并打开一个新文件.

当前正在写入数据的片段叫作`活跃片段`. 活动片段永远不会被删除, 所以如果你设置保留数据 1 天, 但当前片段里包含了 5 天的数据, 那么这些数据会被保留至少 5 天, 因为在片段被关闭之前这些数据无法被删除. 如果你要保留数据一周, 而且每天使用一个新片段, 那么你就会看到, 每天在使用一个新片段的同时会删除一个最老的片段 -- 所以大部分时间该分区会有7个片段存在.

我们在第 2 章讲过, broker 会为分区里的每个片段打开一个文件句柄, 哪怕片段是不活跃的. 这样可能会导致打开过多的文件句柄, 所以操作系统必须根据实际情况做一些调优.

### 5.5.3 文件格式

存在磁盘上的数据格式, 从生产者发送过来数据的格式, 还有发送给消费者的消息的格式都是一样的. 因为使用了相同的消息格式进行磁盘存储和网络传输, broken 可以使用零复制技术给消费者发送消息, 同时避免了对生产者已经压缩过的消息进行解压和再压缩.

除了键, 值和偏移量外, 消息里还包含了消息大小, 校验和, 消息格式版本号, 压缩算法(Snappy, GZip 或 LZ4)和时间戳(在 0.10.0 版本里引入的). 时间戳可以是生产者发送消息的时间, 也可以是消息到达 broker 的时间, 这个是可配置的.

如果生产者发送的是压缩过的消息, 那么同一个批次的消息会被压缩在一起, 被当作 "包装消息" 进行发送(如图 5-6 所示). 于是, broker 就会收到一个这样的消息, 然后再把它发送给消费者. 消费者在解压这个消息之后, 会看到整个批次的消息, 它们都有自己的时间戳和偏移量.

![普通消息和包装消息](https://raw.githubusercontent.com/21moons/memo/master/res/img/kafka/Figure_5.6_普通消息和包装消息.jpg)

也就是说, 如果在生产者端使用了压缩功能(极力推荐), 那么发送的批次越大, 就意味着在网络传输和磁盘存储方面的性能越好, 同时意味着如果修改了消费者使用的消息格式(例如, 在消息里增加了时间戳), 那么网络传输和磁盘存储的格式也要随之修改, 而且 broker 要知道如何处理包含了两种消息格式的文件.

### 5.5.4 索引

消费者可以从 Kafka 的任意可用偏移量位置开始读取消息. 假设消费者要读取从偏移量 100 开始的 lMB 消息, 那么 broker 必须立即定位到偏移量 100(可能是在分区的任意一个片段里), 然后开始从这个位置读取消息. 为了帮助 broker 更快地定位到指定的偏移量, Kafka 为每个分区维护了一个索引. 索引把偏移量映射到片段文件和偏移量在文件里的位置.

索引也被分成片段, 所以在删除消息时, 也可以删除相应的索引. Kafka 不维护索引的校验和. 如果索引出现损坏(索引损坏如何感知), Kafka 会通过读取消息来重新生成索引. 如果有必要, 管理员可以删除索引, 这样做是绝对安全的, Kafka 会自动重新生成这些索引.

### 5.5.5 清理

Kafka 通过改变主题的保留策略来满足使用场景. 早于保留时间的旧事件会被删除, 为每个键保留最新的值, 从而达到清理的效果. 很显然, `只有当应用程序生成的事件里包含了键值对时, 为这些主题设置 compact 策略(紧凑型保留策略)才有意义. 如果主题包含 null 键, 清理就会失败.`

### 5.5.6 清理的工作原理

每个日志片段可以分为以下两个部分.
* 干净的部分
这些消息之前被清理过, 每个键只有一个对应的值, 这个值是上一次清理时保留下来的.
* 污浊的部分
这些消息是在上一次清理之后写入的. 两个部分的日志片段示意如图 5-7 所示.

![包含干净与污浊两个部分的分区](https://raw.githubusercontent.com/21moons/memo/master/res/img/kafka/Figure_5.7_包含干净与污浊两个部分的分区.jpg)

如果在 Kafka 启动时启用了清理功能(通过配置 log.cleaner.enabled 参数), 每个 broker 会启动一个`清理管理器线程`和多个`清理线程`, 它们负责执行清理任务. 这些线程会选择污浊率(污浊消息占分区总大小的比例)较高的分区进行清理.

为了清理分区, 清理线程会读取分区的污浊部分, 井在内存里创建一个 map. map 里的每个元素包含了消息键的散列值和消息的偏移量, 键的散列值是 16B, 加上偏移量总共是 24B. 如果要清理一个 1GB 的日志片段, 并假设每个消息大小为 1KB, 那么这个片段就包含一百万(1000 * 1000)个消息, 而我们只需要用 24MB 的 map 就可以清理这个片段.(如果有重复的键, 可以重用散列项, 从而使用更少的内存.)这是非常高效的!

管理员在配置 Kafka 时可以对 map 使用的内存大小进行配置. 每个线程都有自己的 map,而配置的是所有线程可使用的内存总大小. 如果你为 map 分配了 1GB 内存, 并使用了 5 个清理线程, 那么每个钱程可以使用 200MB 内存来创建自己的 map. Kafka 井不要求依据分区污浊部分的大小来配置 map 占用的空间, 但要求 map 的大小至少可以用来清理一个完整的片段. 如果不符合, 那么 Kafka 就会报错, 管理员要么分配更多的内存, 要么减少清理线程数. 如果只有少部分片段可以完全符合, Kafka 将从最旧的片段开始清理, 等待下一次清理剩余的部分.

清理线程在创建好偏移量 map 后, 开始从干净的片段处读取消息, 从最旧的消息开始, 把它们的内容与 map 里的内容进行比对. 它会检查消息的键是否存在于 map 中, 如果不存在, 那么说明消息的值是最新的, 就把消息复制到替换片段上. 如果键已存在, 消息会被忽略, 因为在分区的后部已经有一个具有相同键的消息存在. 在复制完所有的消息之后, 我们就将替换片段与原始片段进行交换, 然后开始清理下一个片段. 完成整个清理过程之后, 每个键对应一个不同的消息-- 这些消息的值都是最新的. 清理前后的分区片段如图 5-8 所示.

![清理前后的分区片段](https://raw.githubusercontent.com/21moons/memo/master/res/img/kafka/Figure_5.8_清理前后的分区片段.jpg)

### 5.5.7 被删除的事件

如果只为每个键保留最近的一个消息, 那么当需要删除某个特定键所对应的所有消息时, 我们该怎么办? 这种情况是有可能发生的, 比如一个用户不再使用我们的服务, 那么完全可以把与这个用户相关的所有信息从系统中删除.

为了彻底把一个键从系统里删除, 应用程序必须发送一个包含该键且值为 null 的消息. 清理线程发现该悄息时, 会先进行常规的清理, 只保留值为 null 的消息. 该悄息(被称为`墓碑消息`)会被保留一段时间, 时间长短是可配置的. 在这期间, 消费者可以看到这个`墓碑消息`, 井且发现它的值已经被删除. 于是, 如果消费者往数据库里复制 Kafka 的数据, 当它看到这个墓碑消息时, 就知道应该要把相关的用户信息从数据库里删除. 在这个时间段过后, 清理线程会移除这个墓碑消息, 这个键也将从 Kafka 分区里消失. 重要的是, 要留给消费者足够多的时间, 让他看到墓碑消息, 因为如果消费者离线几个小时并错过了墓碑消息, 就看不到这个键, 也就不知道它已经从 Kafka 里删除, 从而也就不会去删除数据库里的相关数据了.

### 5.5.8 何时会清理主题

像 delete 策略不会删除当前活跃的片段一样, compact 策略也不会对当前片段进行清理. 只有旧片段里的消息才会被清理.

在 0.10.0 和更早的版本里, Kafka 会在包含脏记录的主题数量达到 50% 时进行清理. 这样做的目的是避免太过频繁的清理(因为清理会影响主题的读写性能), 同时也避免保存太多脏记录(因为它们会占用磁盘空间). 在主题花费一半的磁盘空间存放脏记录时触发一次清理, 这是个合理的折中, 管理员当然也可以自主进行调整.

我们计划在未来的版本中加入宽限期, 在宽限期内, 我们保证消息不会被清理. 对于想看到主题的每个消息的应用程序来说, 它们就有了足够的时间, 即使时间有点滞后.

# 6 可靠的数据传递

可靠性是系统的一个属性, 而不是一个独立的组件, 所以在讨论 Kafka 的可靠性保证时, 还是要从系统的整体出发.

说到可靠性, 那些与 Kafka 集成的系统与 Kafka 本身一样重要. 正因为可靠性是系统层面的概念, 所以它不只是某个个体的事情. Kafka 管理员, Linux 系统管理员, 网络和存储管理员以及应用程序开发者, 所有人必须协同作战, 才能构建一个可靠的系统.

## 6.1 可靠性保证

Kafka 可以在哪些方面作出保证呢?
* Kafka 可以保证分区消息的顺序. 如果使用同一个生产者往同一个分区写入消息, 而且消息 B 在消息 A 之后写入, 那么 Kafka 可以保证消息 B 的偏移量比消息 A 的偏移量大, 而且消费者会先读取消息 A 再读取消息 B.
* 只有当消息被写入分区的所有同步副本时(但不一定要写入磁盘), 它才被认为是 "已提交" 的. 生产者可以选择接收不同类型的确认, 比如在消息被完全提交时的确认, 或者在消息被写入首领副本时的确认, 或者在消息被发送到网络时的确认.
* 只要还有一个副本是活跃的, 那么已经提交的消息就不会丢失.
* 消费者只能读取已经提交的悄息.

这些基本的保证机制可以用来构建可靠的系统, 但仅仅依赖它们是无法保证系统完全可靠的. 构建一个可靠的系统需要作出一些权衡, Kafka 管理员和开发者可以在配置参数上作出权衡, 从而得到他们想要达到的可靠性. 这种权衡一般是指 "消息存储的可靠性和一致性的重要程度" 与 "可用性, 高吞吐量, 低延迟和硬件成本的重要程度" 之间的权衡.

## 6.2 复制





