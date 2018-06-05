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

键有两个用途: 可以作为消息的附加信息, 也可以用来决定消息该被写到主题的哪个分区. 拥有相同键的消息将被写到同一个分区. 也就是说, 如果一个进程只从一个主题的分区读取数据(第 4 章会介绍更多细节), 那么具有相同键的所有记录都会被该进程读取.

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

生成消息使用的序列化器与读取消息使用的反序列化器应该是一一对应的. 我们并不建议使用自定义序列化器和自定义反序列化器. 它们把生产者和消费者紧紧地耦合在一起, 井且很脆弱, 容易出错. 我们建议使用标准的消息格式, 比如 JSON, Thrift, Protobuf 或 Avro.

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

为了彻底把一个键从系统里删除, 应用程序必须发送一个包含该键且值为 null 的消息. 清理线程发现该消息时, 会先进行常规的清理, 只保留值为 null 的消息. 该消息(被称为`墓碑消息`)会被保留一段时间, 时间长短是可配置的. 在这期间, 消费者可以看到这个`墓碑消息`, 井且发现它的值已经被删除. 于是, 如果消费者往数据库里复制 Kafka 的数据, 当它看到这个墓碑消息时, 就知道应该要把相关的用户信息从数据库里删除. 在这个时间段过后, 清理线程会移除这个墓碑消息, 这个键也将从 Kafka 分区里消失. 重要的是, 要留给消费者足够多的时间, 让他看到墓碑消息, 因为如果消费者离线几个小时并错过了墓碑消息, 就看不到这个键, 也就不知道它已经从 Kafka 里删除, 从而也就不会去删除数据库里的相关数据了.

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
* 消费者只能读取已经提交的消息.

这些基本的保证机制可以用来构建可靠的系统, 但仅仅依赖它们是无法保证系统完全可靠的. 构建一个可靠的系统需要作出一些权衡, Kafka 管理员和开发者可以在配置参数上作出权衡, 从而得到他们想要达到的可靠性. 这种权衡一般是指 "消息存储的可靠性和一致性的重要程度" 与 "可用性, 高吞吐量, 低延迟和硬件成本的重要程度" 之间的权衡.

## 6.2 复制

Kafka 的复制机制和分区的多副本架构是 Kafka 可靠性保证的核心.

Kafka 的主题被分为多个分区, 分区是基本的数据块. 分区存储在单个磁盘上, Kafka 可以保证分区里的事件是有序的, 分区可以在线(可用), 也可以离线(不可用). 每个分区可以有多个副本, 其中一个副本是首领. 所有的事件都直接发送给首领副本, 或者直接从首领副本读取事件. 其他副本只需要与首领保持同步, 并及时复制最新的事件. 当首领副本不可用时, 其中一个同步副本将成为新首领.

分区首领是同步副本, 而对于跟随者副本来说, 它需要满足以下条件才能被认为是同步的.
* 与 Zookeeper 间有一个活跃的会话, 也就是说, 它在过去的 6s(可配置) 内向 Zookeeper 发送过心跳.
* 在过去的 10s 内(可配置)从首领那里获取过消息.
* 在过去的 10s 内从首领那里获取过最新的消息. 光从首领那里获取消息是不够的, 它还必须是几乎零延迟的.

如果跟随者副本不能满足以上任何一点, 比如与 Zookeeper 断开连接, 或者不再获取新消息, 或者获取消息滞后了 10s 以上, 那么它就被认为是不同步的. 一个不同步的副本通过与 Zookeeper 重新建立连接, 并从首领那里获取最新消息, 可以重新变成同步的. 这个过程在网络出现临时问题并很快得到修复的情况下会很快完成, 但如果 broker 发生崩溃就需要较长的时间.

>非同步副本
如果一个或多个副本在同步和非同步状态之间快速切换, 说明集群内部出现了问题, 通常是 Java 不恰当的垃圾回收配置导致的. 不恰当的垃圾回收配置会造成几秒钟的停顿, 从而让 broker 与 Zookeeper 之间断开连接, 最后变成不同步的, 进而发生状态切换.

一个滞后的同步副本会导致生产者和消费者变慢, 因为在消息被认为已提交之前, 客户端会等待所有同步副本接收消息. 而如果一个副本不再同步了, 我们就不再关心它是否已经收到消息. 虽然非同步副本同样滞后, 但它并不会对性能产生任何影响. 但是, 更少的同步副本意味着更低的`有效复制系数`, 在发生岩机时丢失数据的风险更大.

我们将在下一节讲解在实际项目中这将意味着什么.

## 6.3 broker 配置

broker 有 3 个配置参数会影响 Kafka 消息存储的可靠性. 与其他配置参数一样, 它们可以应用在 `broker 级别`, 用于控制所有主题的行为, 也可以应用在`主题级别`, 用于控制个别主题的行为.

在主题级别控制可靠性, 意味着 Kafka 集群可以同时拥有可靠的主题和非可靠的主题. 例如, 在银行里, 管理员可能把整个集群设置为可靠的, 但把其中的一个主题设置为非可靠的, 用于保存来自客户的投诉, 因为这些消息是允许丢失的.

### 6.3.1 复制系数

broker 级别: default.replication.factor
主题级别: replication.factor

如果复制系数为 N, 那么在 N-1 个 broker 失效的情况下, 仍然能够从主题读取数据或向主题写入数据. 所以, 更高的复制系数会带来更高的可用性, 可靠性和更少的故障. 另一方面, 复制系数 N 需要至少 N 个 broker, 会有 N 个数据副本, 也就是说它们会占用 N 倍的磁盘空间. 我们一般会在可用性和存储硬件之间作出权衡.

### 6.3.2 不完全的首领选举

unclean.leader.election 只能在 broker 级别进行配置, 它的默认值是 true.

我们之前提到过, 当分区首领不可用时, 一个同步副本会被选为新首领. 如果在选举过程中没有丢失数据, 也就是说提交的数据同时存在于所有的同步副本上, 那么这个选举就是 "完全" 的. 但如果当首领不可用时其他副本都是不同步的, 我们该怎么办呢?

这种情况会在以下两种场景里出现:

* 分区有 3 个副本, 其中的两个跟随者副本不可用(比如有两个 broker 发生崩溃). 这个时候, 如果生产者继续往首领写入数据，所有消息都会得到确认并被提交(因为此时首领是唯一的节点). 现在我们假设首领也不可用了(又一个 broker 发生崩溃), 这个时候, 如果之前的一个跟随者重新启动, 它就成为了分区的唯一不同步副本.

* 分区有 3 个副本, 因为网络问题导致两个跟随者副本复制消息滞后, 所以尽管它们还在复制消息, 但已经不同步了. 首领作为唯一的同步副本继续接收消息. 这个时候, 如果首领变为不可用, 另外两个副本就再也无法变成同步的了.

对于这两种场景, 我们要作出一个选择.

* 如果要求不同步的副本不能被提升为新首领, 那么分区在旧首领(最后一个同步副本)恢复之前是不可用的. 有时候这种状态会持续数小时(比如更换内存芯片).

* 如果允许提升不同步的副本为新首领, 那么在这个副本变为不同步之后写入旧首领的消息会全部丢失, 导致数据不一致. 为什么会这样呢? 假设在`副本0`和`副本1`不可用时, 将偏移量为 100-200 的消息写入`副本2`(首领). 然后`副本2`变为不可用, `副本0`变为可用. `副本0`只包含偏移量 0-100 的消息, 不包含偏移量 100-200 的消息. 如果我们允许`副本0`成为新首领, 生产者就可以继续写入数据, 消费者可以继续读取数据. 于是, `副本0` 就有了偏移量 100-200 的新消息, 但是新写入的消息与`副本2`上的相同偏移量的消息不同. 这样, 部分消费者会读取到偏移量 100-200 的旧消息, 部分消费者会读取到偏移量 100-200 的新消息, 还有部分消费者读取的是二者的混合. 这样会导致非常不好的结果, 比如生成不准确的报表. 另外, `副本2`可能会重新变为可用, 并成为新首领的跟随者, 这时它会把比当前首领旧的消息全部删除, 而这些被删除消息中的一些消息对于所有消费者来说都是不可见的.

简而言之, 如果我们允许不同步的副本成为首领, 那么就要承担`丢失数据`和出现`数据不一致`的风险. 如果不允许它们成为首领, 那么就要接受较低的`可用性`, 因为我们必须等待原先的首领恢复到可用状态.

如果把 unclean.leader.election.enable 设为 true, 就是允许不同步的副本成为首领(也就是 "不完全选举"), 那么我们将面临丢失消息的风险. 如果把这个参数设为 false, 就要等待原先的首领重新上线, 从而降低了可用性. 我们经常看到一些对数据质量和数据一致性要求较高的系统会禁用这种不完全的首领选举(把这个参数设为 false). 银行系统是这方面最好的例子, 大部分银行系统宁愿选择在几分钟甚至几个小时内不处理信用卡支付事务, 也不会冒险处理错误的消息. 不过在对可用性要求较高的系统里, 比如实时点击流分析系统, 一般会启用不完全的首领选举.

### 6.3.3 最小同步副本

在主题级别和 broker 级别上, 这个参数都叫 mim.insync.replicas.

我们知道, 即使为一个主题配置了3个副本, 还是会出现只有一个同步副本的情况. 一旦这个同步副本变为不可用, 我们就必须在可用性和一致性之间作出选择. 根据 Kafka 对可靠性保证的定义, 消息只有在被写入到所有同步副本之后才被认为是已提交的. 但如果这里的 "所有副本" 数量为 1, 那么在这个副本变为不可用时, 数据就会丢失.

如果要确保已提交的数据被写入不止一个副本, 就需要把最少同步副本数量设置为大一点的值. 对于一个包含 3 个副本的主题, 如果mim.insync.replicas 被设为 2, 那么至少要存在两个同步副本才能向分区写入数据.

如果 3 个副本都是同步的, 或者只有一个副本变为不可用, 都不会有什么问题. 不过, 如果有两个副本变为不可用, 那么 broker 就会停止接受生产者的请求. 尝试发送数据的生产者会收到 NotEnoughReplicasException 异常. 此时消费者仍然可以继续读取已有的数据. 实际上, 如果使用这样的配置, 那么当只剩下一个同步副本时, 它就变成只读了, 这是为了避免在发生不完全选举时数据的写入和读取出现非预期的行为. 为了从只读状态中恢复, 必须让两个不可用分区中的一个重新变为可用的(比如重启 broker), 并等待它变为同步副本.

## 6.4 在可靠的系统里使用生产者

即使我们尽可能把 broker 配置得很可靠, 但如果没有对生产者进行可靠性方面的配置, 整个系统仍然有可能出现突发性的数据丢失.

请看以下两个例子.
* 为 broker 配置了3个副本, 井且禁用了不完全首领选举, 这样应该可以保证万无一失. 我们把生产者发送消息的 acks 设为1(只要首领接收到消息就可以认为消息写入成功). 生产者发送一个消息给首领, 首领成功写入, 但跟随者副本还没有接收到这个消息. 首领向生产者发送了一个响应, 告诉它 "消息写入成功", 发送完后马上首领崩溃了, 而此时消息还没有被其他副本复制过去. 另外两个副本此时仍然被认为是同步的(毕竟判定一个副本不同步需要一小段时间), 而且其中的一个副本成了新的首领. 因为消息还没有被写入这个副本, 所以对于 broker 和生产者来说消息已经丢失了, 但发送消息的客户端却认为消息已成功写入. 因为消费者看不到丢失的消息, 所以此时的系统仍然是`一致`的(因为副本没有收到这个消息，所以消息不算已提交).

* 为 broker 配置了3个副本, 并且禁用了`不完全首领选举`. 我们接受了之前的教训, 把生产者的 acks 设为 all. 假设现在往 Kafka 发送消息, 恰好遇上首领崩溃, 正在选举新的首领, Kafka 会向生产者返回 "首领不可用" 的响应. 在这个时候, 如果生产者没能正确处理这个错误, 也没有重试发送消息直到发送成功, 那么消息也有可能丢失. 这算不上是 broker 可靠性问题, 因为 broker 并没有收到这个消息. 这也不是`一致性`问题, 因为消费者井没有读到这个消息. 问题在于如果生产者没能正确处理这些错误, 弄丢消息的是它们自己.

那么，我们该如何避免这些悲剧性的后果呢? 从上面两个例子可以看出, 每个使用 Kafka 的开发人员都要注意两件事情.
* 根据可靠性需求配置恰当的 acks 值.
* 在参数配置和代码里正确处理错误.

第3章已经深入讨论了生产者的几种模式, 现在回顾几个要点.

### 6.4.1 发送确认

生产者可以选择以下3种不同的确认模式.

* ack=0 意味着只要生产者能够通过网络把消息发送出去, 那么就认为消息已成功写入 Kafka. 在这种情况下还是有可能发生错误, 比如发送的对象无法被序列化或者网卡发生故障, 但如果是分区离线或整个集群长时间不可用, 那就不会收到任何错误. 即使是在发生完全首领选举的情况下, 这种模式仍然会丢失消息, 因为在新首领选举过程中生产者并不知道首领已经不可用了. 不过该模式下的运行速度是非常快的(这就是为什么很多基准测试都是基于这个模式), 你可以得到惊人的吞吐量和带宽利用率, 不过如果选择了这种模式, 一定会有消息丢失.

* acks=1 意味着首领在收到消息并把它写入到分区数据文件(不一定同步到磁盘上)时会返回确认或错误响应. 在这个模式下, 如果发生正常的首领选举, 生产者会在选举时收到一个 LeaderNotAvailableException 异常, 如果生产者能恰当地处理这个错误(参考 6.4.2 节), 它会重试发送消息, 最终消息会安全到达新的首领那里. 不过在这个模式下仍然有可能丢失数据, 比如消息已经成功写入首领, 但在消息被复制到跟随者副本之前首领发生崩溃.

* acks=all 意味着首领在返回确认或错误响应之前, 会等待所有同步副本都收到消息. 如果和 min.insync.replicas 参数结合起来, 就可以决定在返回确认前至少有多少个副本能够收到消息. 这是最保险的做法--生产者会一直重试直到消息被成功提交. 不过同时这也是最慢的做法, 生产者在继续发送其他消息之前需要等待所有副本都收到当前的消息. 可以通过使用异步模式和更大的批次来加快速度, 但这样做通常会降低吞吐量.

### 6.4.2 配置生产者的重试参数

生产者需要处理的错误包括两部分: 一部分是生产者可以自动处理的错误, 还有一部分是需要开发者手动处理的错误.

如果 broker 返回的错误可以通过`重试`来解决, 那么生产者会自动处理这些错误. 生产者向 broker 发送消息时, broker 可以返回一个成功响应码或者一个错误响应码. 错误响应码可以分为两种, 一种是在重试之后可以解决的, 还有一种是无法通过重试解决的. 例如, 如果 broker 返回的 LEADER_ NOT_ AVAILABLE 错误, 生产者可以尝试重新发送消息. 也许在这个时候一个新的首领被选举出来了, 那么这次发送就会成功. 也就是说, LEADER_ NOT_ AVAILABLE 是一个`可重试错误`. 另一方面, 如果 broker 返回的是 INVALID_CONFIG 错误, 即使通过重试也无能改变配置选项, 所以这样的重试是没有意义的. 这种错误是`不可重试错误`。

一般情况下, 如果你的目标是不丢失任何消息, 那么最好让生产者在遇到`可重试错误`时能够保持重试. 为什么要这样? 因为像首领选举或网络连接这类问题都可以在几秒钟之内得到解决, 让生产者保持重试就好. 经常会有人会问:"重试次数配置为多少次比较好?" 这个要看你在生产者放弃重试并抛出异常之后想做些什么. 如果你想抓住异常并多重试几次, 那么就可以把重试次数设置得多一点; 如果你觉得多次重试造成的延迟使得发送消息毫无意义, 那么可以将重试次数设置的少一点; 如果在发送失败后你想把消息保存到某个地方然后回过头来再继续处理, 那就停止重试. Kafka 的跨数据中心复制工具(MirrorMaker, 我们将在第8章介绍)默认会进行无限制的重试(例如 retries=MAX_INT). 作为一个具有高可靠性的复制工具, 它决不会丢失消息.

要注意, 重新发送一个已经失败的消息会带来一些风险, 假如两个消息都写入成功, 会导致消息重复. 例如, 生产者因为网络问题没有收到 broker 的确认, 但实际上消息已经写入成功, 生产者认为网络出现了故障, 重新发送该消息. 在这种情况下, broker 会收到两个相同的消息. 重试和恰当的错误处理可以保证每个消息 "至少被保存一次", 但当前的 Kafka 版本(0.10.0)无法保证每个消息 "只被保存一次". 现实中的很多应用程序在消息里加入唯一标识符, 用于检测重复消息, 消费者而不是 Kafka 在读取消息时可以对它们进行清理. 还要一些应用程序可以做到消息的`"幕等", 也就是说, 即使出现了重复消息, 也不会对处理结果的正确性造成影响.` 例如, 消息 "这个账号里有 110 美元" 就是`幕等`的, 因为即使多次发送这样的消息, 产生的结果都是一样的. 不过消息 "往这个账号里增加 10 美元" 就`不是幕等`的.

### 6.4.2 额外的错误处理

* 可重试的 broker 错误, 例如消息大小错误, 认证错误等.
* 在消息发送之前发生的错误, 如序列化错误.
* 在生产者达到重试次数上限时或者在消息占用的内存达到上限时发生的错误.

如果错误处理只是为了重试发送消息, 那么最好还是使用生产者内置的重试机制.

## 6.5 在可靠的系统里使用消费者

只有那些被提交到 Kafka 的数据, 也就是那些已经被写入所有同步副本的数据, 对消费者是可用, 这意味着消费者得到的消息已经具备了一致性. 消费者唯一要做的是跟踪哪些消息是已经读取过的, 哪些是还没有读取过的. 这是在读取消息时不丢失消息的关键.

在从分区读取数据时, 消费者会获取一批事件, 检查这批事件里最大的偏移量, 然后从这个偏移量开始读取下一批事件. 这样可以保证消费者总能以正确的顺序获取新数据, 不会错过任何事件.

如果一个消费者退出, 另一个消费者需要知道从什么地方开始继续处理, 它需要知道前一个消费者在退出前处理的最后一个偏移量是多少. 这也就是为什么消费者要 "提交" 它们的偏移量. 它们把当前读取的偏移量保存起来, 在退出之后, 同一个群组里的其他消费者就可以接替它们的工作. `如果消费者提交了偏移量却未能处理完消息, 那么就有可能造成消息丢失, 这也是消费者丢失消息的主要原因.` 在这种情况下, 如果其他消费者接手了工作, 那些没有被处理完的消息就会被忽略, 永远得不到处理. 这就是为什么我们非常重视`偏移量提交的时间点和提交的方式`.

### 6.5.1 消费者的可靠性配置

第l个是 group.id. 这个参数在第4章已经详细解释过了, 如果两个消费者具有相同的 group.id, 井且订阅了同一个主题, 且主题有多个分区, 那么每个消费者会分到主题分区的一个子集, 也就是说它们只能读到所有消息的一部分(消费者组成的群组会读取主题所有的消息). 如果你希望消费者可以看到主题的所有消息, 那么需要为所有消费者设置不同的 group.id.

第2个是 auto.offset.reset. 这个参数指定了在没有偏移量可提交时(比如消费者第1次启动时)或者请求的偏移量在 broker 上不存在时(第4章已经解释过这种场景), 消费者会做些什么. 这个参数有两种配置, 一种是 earliest, 如果选择了这种配置, 消费者会从分区的开始位置读取数据, 不管偏移量是否有效,这样会导致消费者读取大量的重复数据, 但可以保证最少的数据丢失. 一种是 latest, 如果选择了这种配置, 消费者会从分区的末尾开始读取数据, 这样可以减少重复处理消息, 但很有可能会错过一些消息.

第3个是 enable.auto.commit. 这是一个非常重要的配置参数, 你可以让消费者基于任务调度自动提交偏移量, 也可以在代码里手动提交偏移量. 自动提交的一个最大好处是, 在实现消费者逻辑时可以少考虑一些问题. 如果你在消费者轮询操作里处理所有的数据, 那么自动提交可以保证只提交已经处理过的偏移量. `自动提交的主要缺点是, 有可能会重复处理消息(比如消费者已经处理完消息, 但在自动提交偏移量之前崩溃或被强制退出), 而且如果把消息交给另外一个后台线程去异步处理, 自动提交机制可能会在消息还没有处理完毕就提交偏移量.`

第4个配置参数 auto.commit.interval.ms 与第3个参数有直接的联系. 如果选择了自动提交偏移量, 可以通过该参数配置提交的频度, 默认值是每5秒钟提交一次. 一般来说, 频繁提交会增加额外的开销, 但也会降低重复处理消息的概率.

### 6.5.2 显式提交偏移量

如果希望能够对偏移量提交的时间点进行更精细的控制, 那么就要仔细想想该如何提交偏移量了--要么是为了减少重复处理消息, 要么是因为消息处理逻辑被放到了轮询之外. 我们会着重说明在开发具有可靠性的消费者应用程序时需要注意的几个事项.

1. 总是在处理完事件后再提交偏移量
如果所有的处理都是在轮询里完成, 并且不需要在多轮轮询之间维护状态(如为了实现聚合操作), 那么可以使用自动提交, 或者在轮询结束时进行手动提交.

2. 提交频度是性能和重复消息数量之间的权衡
即使是在最简单的场景里, 比如所有的处理都在轮询里完成, 井且不需要在多轮轮询之间维护状态, 你仍然可以在一个循环里多次提交偏移量(甚至可以在每处理完一个事件之后), 或者多个循环里只提交一次(与生产者的 acks=all 配置有点类似), 这完全取决于你在性能和重复处理消息之间作出的权衡.

3. 确保对提交的偏移量心里有数
在轮询过程中提交偏移量有一个不好的地方, 就是提交的偏移量有可能是读取到的最新偏移量, 而不是处理过的最新偏移量. 要记住, 在处理完消息后再提交偏移量是非常关键的否则会导致消费者错过消息. 我们已经在第4章给出了示例.

4. 再均衡
在设计应用程序时要注意处理消费者的再均衡问题. 我们在第4章举了几个例子, 一般要在分区被撤销之前提交偏移量, 并在分配到新分区时清理之前的状态.

5. 消费者可能需要重试
有时候, 轮询获取到的消息不会被全部处理, 你想稍后再来处理. 例如, 假设要把 Kafka 的数据写到数据库里, 不过那个时候数据库不可用, 于是你想稍后重试. 要注意, 你提交的是偏移量, 而不是对消息的 "确认", 这个与传统的发布和订阅消息系统不太一. 如果记录 #30 处理失败, #31 处理成功, 那么你不应该提交 #31, 否则系统会认为 #31 以前的消息都处理成功. 不过可以采用以下两种模式来解决这个问题.

第一种模式, 在遇到可重试错误时, 提交最后一个处理成功的偏移量, 然后把还没有处理好的消息保存到缓冲区里(这样下一个轮询就不会把它们覆盖掉), 调用消费者的 pause() 方法来确保其他的轮询不会返回数据(不需要担心在重试时缓冲区溢出), 在保持轮询的同时尝试重新处理(关于为什么不能停止轮询, 请参考第4章). 如果重试成功, 或者重试次数达到上限并决定放弃, 那么把错误记录下来并丢弃消息, 然后调用 resume() 方法让消费者继续从轮询里获取新数据.

第二种模式, 在遇到可重试错误时, 把错误写入一个独立的主题, 然后继续. 一个独立的消费者群组负责从该主题上读取错误消息, 井进行重试, 或者使用其中的一个消费者同时从该主题上读取错误消息并进行重试, 不过在重试时需要暂停该主题. 这种模式有点像其他消息系统里的 dead-letter-queue.

6. 消费者可能需要维护额外的状态
有时候你希望在多个轮询之间维护状态, 例如, 你想计算消息的移动平均数, 希望在首次轮询之后计算平均数, 然后在后续的轮询中更新这个结果. 如果进程重启, 你不仅需要从上一个偏移量开始处理数据, 还要恢复移动平均数. 有一种办法是在提交偏移量的同时把最近计算的平均数写到一个"结果"主题上. 消费者线程在重新启动之后, 它就可以拿到最近的平均数并接着计算. 不过这并不能完全地解决问题, 因为 Kafka 并没有提供`事务支持`. 消费者有可能在写入平均数之后来不及提交偏移量就崩溃了, 或者反过来也一样. 这是一个很复杂的问题, 你不应该尝试自己去解决这个问题, 建议尝试一下 `KafkaStreams` 这个类库, 它为聚合, 连接, 时间窗和其他复杂的分析提供了高级的 DSL API.

7. 长时间处理
有时候处理数据需要很长时间: 你可能会从发生阻塞的外部系统获取信息, 或者把数据写到外部系统, 或者进行一个非常复杂的计算. 要记住, 暂停轮询的时间不能超过几秒钟. 即使不想获取更多的数据, 也要保持轮询, 这样客户端才能往 broker 发送心跳. 在这种情况下, 一种常见的做法是使用一个线程池来处理数据, 因为使用多个线程可以进行并行处理, 从而加快处理速度. 在把数据移交给线程池去处理之后, 你就可以暂停消费者, 然后保持轮询, 但不获取新数据, 直到工作线程处理完成. 在工作线程处理完成之后, 可以让消费者继续获取新数据. 因为消费者一直保持轮询, 心跳会正常发送, 就不会触发再均衡.

8. 仅一次传递
有些应用程序不仅仅需要 `"至少一次" (at-least-once)`语义(意味着没有数据丢失), 还需要`"仅一次"(exactly-once)`语义. 尽管 Kafka 现在还不能完全支持`仅一次`语义, 消费者还是有一些办法可以保证 Kafka 里的每个消息只被写到外部系统一次(但不会处理向 Kafka 写入数据时可能出现的重复数据).

实现`仅一次`处理最简单且最常用的办能是把结果写到一个支持唯一键的系统里, 比如键值存储引擎, 关系型数据库, ElasticSearch 或其他数据存储引擎. 在这种情况下, 要么消息本身包含一个唯一键(通常都是这样), 要么使用主题, 分区和偏移量的组合来创建唯一键--它们的组合可以唯一标识一个 Kafka 记录. 如果你把消息和一个唯一键写入系统, 然后碰巧又读到一个相同的消息, 只要把原先的键值覆盖掉即可. 数据存储引擎会覆盖已经存在的键值对, 就像没有出现过重复数据一样. 这个模式被叫作`幂等性写入`, 它是一种很常见也很有用的模式.

如果被写入消息的系统支持事务, 那么就可以使用另一种方法. 最简单的是使用关系型数据库, 不过 HDFS 里有一些被重新定义过的原子操作也经常用来达到相同的目的. 我们把消息和偏移量放在同一个事务里, 这样它们就能保持同步. 在消费者启动时, 它会获取最近处理过的消息偏移量, 然后调用 seek() 方法从该偏移量位置继续读取数据. 我们在第4章已经介绍了一个相关的例子.

# 7 构建数据管道

在使用 Kafka 构建数据管道时, 通常有两种使用场景: 

第一种, 把 Kafka 作为数据管道的两个端点之一, 例如, 把 Kafka 里的数据移动到 S3 上, 或者把 MongoDB 里的数据移动到 Kafka 里;

第二种, 把 Kafka 作为数据管道两个端点的中间媒介, 例如, 为了把 Twitter 的数据移动到 ElasticSearch 上, 需要先把它们移动到 Kafka 里, 再将它们从 Kafka 移动到 ElasticSearch 上.

Kafka 为数据管道带来的主要价值在于, 它可以作为数据管道各个数据段之间的大型缓冲区, 有效地解耦管道数据的生产者和消费者. Kafka 的解耦能力以及在安全和效率方面的可靠性, 使它成为构建数据管道的最佳选择. 为了支持数据管道场景, Kafka 引入了 `connect API`.

## 7.1 构建数据管道时需要考虑的问题

### 7.1.1 及时性

有些系统希望每天定时接收大量数据, 而有些则希望在数据生成几毫秒之内就能拿到它们. 大部分数据管道介于这两者之间. 一个好的数据集成系统能够很好地支持数据管道的各种即时需求, 而且在业务需求发生变更时, 具有不同即时需求的数据表之间可以方便地进行迁移. Kafka 作为一个基于流的数据平台, 提供了可靠且可伸缩的数据存储, 可以支持几近实时的数据管道和基于小时的批处理. 生产者可以频繁地向 Kafka 写入数据, 也可以按需写入; 消费者可以在数据到达的第一时间读取它们, 也可以每隔一段时间读取一次积压的数据.

Kafka 在这里扮演了一个大型缓冲区的角色, 降低了生产者和消费者之间的时间敏感度. 实时的生产者和基于批处理的消费者可以同时存在, 也可以任意组合. 实现`反压策略`也因此变得更加容易, Kafka 本身就使用了`反压策略(必要时可以延后向生产者发送确认)`, 消费速率完全由消费者自己决定.

### 7.1.2 可靠性

我们要避免单点故障, 并能够自动从各种故障中快速恢复. 数据通过数据管道到达业务系统, 哪怕管道出现几秒钟的故障, 也会造成灾难性的影响, 对于那些要求毫秒级的及时性系统来说尤为如此. 数据传递保证是可靠性的另一个重要因素. 有些系统允许数据丢失, 不过在大多数情况下, 它们要求`至少一次传递`. 也就是说, 源系统的每一个事件都必须到达目的地, 不过有时候需要进行重试, 而重试可能造成重复传递. 有些系统甚至要求`仅一次传递`--源系统的每一个事件都必须到达目的地, 不允许丢失, 也不允许重复.

我们已经在第6章深入讨论了 Kafka 的可用性和可靠性保证. Kafka 本身就支持 "至少一次传递", 如果再结合具有事务模型或唯一键特性的外部存储系统, Kafka 也能实现"仅一次传递". 因为大部分的端点都是数据存储系统, 它们提供了"仅一次传递"的原语支持, 所以基于 Kafka 的数据管道也能实现 "仅一次传递". 值得一捷的是, Connect API 为集成外部系统提供了处理偏移量的 API, 连接器因此可以构建`仅一次传递`的端到端数据管道. 实际上, 很多开源的连接器都支持仅一次传递.

### 7.1.3 高吞吐量和动态吞吐量

为了满足现代数据系统的要求, 数据管道需要支持非常高的吞吐量. 更重要的是, 在某些情况下, 数据管道还需要能够应对突发的吞吐量增长.

由于我们将 Kafka 作为生产者和消费者之间的缓冲区, 消费者的吞吐量和生产者的吞吐量就不会耦合在一起了. 我们也不再需要实现复杂的反压机制, 如果生产者产生的数据量超过了消费者消耗的数据量, 可以把数据积压在 Kafka 里, 等待消费者追赶上来. 通过增加额外的消费者或生产者可以实现 Kafka 的伸缩, 因此我们可以在数据管道的任何一边进行动态的伸缩, 以便满足持续变化的需求.

因为 Kafka 是一个高吞吐量的分布式系统, 一个适当规模的集群每秒钟可以处理数百兆的数据, 所以根本无需担心数据管道无法满足伸缩性需求. 另外, Connect API 不仅支持伸缩, 而且擅长并行处理任务. 稍后, 我们将会介绍`数据源`和`数据池(Data Sink)`如何在多个线程间拆分任务, 最大限度地利用CPU资源, 哪怕是运行在单台机器上. Kafka 支持多种类型的压缩, 在增长吞吐量时, Kafka 用户和管理员可以通过压缩来调整网络和存储资源的使用.

### 7.1.4 数据格式

数据管道需要协调各种数据格式和数据类型, 这是数据管道的一个非常重要的因素. 数据类型取决于不同的数据库和数据存储系统. 你可能会通过 Avro 将 XML 或关系型数据加载到 Kafka 里, 然后将它们转成JSON 写入 ElasticSearch, 或者转成 Parquet 写入 HDFS, 或者转成 csv 写入 S3.

Kafka 和 connect API 与数据格式无关. 我们已经在之前的章节介绍过, 生产者和消费者可以使用各种序列化器来表示任意格式的数据. connect API 有自己的内存对象模型, 包括数据类型和 schema.  不过, 可以使用一些可插拔的转换器将这些对象保存成任意的格式, 也就是说, 不管数据是什么格式的, 都不会限制我们使用连接器.

很多数据源和数据池都有 schema, 我们从数据源读取 schema, 把它们保存起来, 井用它们验证数据格式的兼容性, 甚至用它们更新数据池的 schema. 从 MySQL 到 Hive 的数据管道就是一个很好的例子. 如果有人在 MySQL 里增加了一个字段, 那么在加载数据时, 数据管道可以保证 Hive 里也添加了相应的字段.

另外, 数据地连接器将 Kafka 的数据写入外部系统, 因此需要负责处理数据格式. 有些连接器把数据格式的处理做成可插拔的, 比如 HDFS 的连接器就支持 Avro 和 Parquet.

通用的数据集成框架不仅要支持各种不同的数据类型, 而且要处理好不同数据掘和数据地之间的行为差异. 例如, 在关系型数据库向 Syslog 发起抓取数据请求时, Syslog 会将数据推送给它们, 而 `HDFS 只支持追加写入模式, 只能向 HDFS 写入新数据, 而对于其他很多系统来说, 既可以追加数据, 也可以更新已有的数据.`

### 7.1.5 转换

数据转换比其他需求更具争议性. 数据管道的构建可以分为两大阵营, 即`ETL`和`ELT`.

`ETL` 表示`提取-转换-加载(Extract-Transform-Load)`, 也就是说, 当数据流经数据管道时, 数据管道会负责处理它们. 这种方式为我们节省了时间和存储空间, 因为不需要经过保存数据, 修改数据, 再保存数据这样的过程. 不过, 这种好处也要视情况而定. 有时候, 这种方式会给我们带来实实在在的好处, 但也有可能给数据管道造成不适当的计算和存储负担. 这种方式有一个明显不足, 就是数据的转换会给数据管道下游的应用造成一些限制, 特别是当下游的应用希望对数据进行进一步处理的时候. 假设有人在 MongoDB 和 MySQL 之间建立了数据管道, 井且过滤掉了一些事件记录, 或者移除了一些字段, 那么下游应用从 MySQL 中访问到的数据是不完整的. 如果它们想要访问被移除的字段, 只能重新构建管道, 井重新处理历史数据.

`ELT`表示`提取-加载-转换(Extract-Load-Transform)`. 在这种模式下, 数据管道只做少量的转换(主要是数据类型转换), 确保到达数据地的数据尽可能地与数据源保持一致. 这种情况也被称为高保真(highfidelity) 数据管道或数据湖(datalake)架构. 目标系统收集 "原始数据", 并负责处理它们. 这种方式为目标系统的用户提供了最大的灵活性, 因为它们可以访问到完整的数据. 在这些系统里诊断问题也变得更加容易, 因为数据被集中在同一个系统里进行处理, 而不是分散在数据管道和应用里. 这种方式的不足在于, 数据的转换占用了目标系统太多的 CPU 和存储资源. 有时候, 目标系统造价高昂, 如果有可能, 人们希望能够将计算任务移出这些系统.

### 7.1.6 安全性

Kafka 支持加密传输数据, 从数据源到 Kafka, 再从 Kafka 到数据池. 它还支持认证(通过 SASL 来实现)和授权, 所以你可以确信, 如果一个主题包含了敏感信息, 在不经授权的情况下, 数据是不会流到不安全的系统里的. Kafka 还提供了审计日志用于跟踪访问记录. 通过编写额外的代码, 还可能跟踪到每个事件的来源和事件的修改者, 从而在每个记录之间建立起整体的联系.

### 7.1.7 故障处理能力

Kafka 会长时间地保留数据, 所以我们可以在适当的时候回过头来重新处理出错的数据.

### 7.1.8 耦合性和灵活性

数据管道最重要的作用之一是`解耦``数据源`和`数据池`. 它们在很多情况下可能发生耦合.

* 临时数据管道

有些公司为每一对应用程序建立单独的数据管道. 例如, 他们使用 Logstash 向 ElasticSearch 导入日志, 使用 Flume 向 HDFS 导入日志, 使用 GoldenGate 将 Oracle 的数据导到 HDFS, 使用          Informatica 将 MySQL 的数据或 XML 导到 Oracle, 等等. 他们将数据管道与特定的端点耦合起来, 井创建了大量的集成点, 需要额外的部署, 维护和监控. 当有新的系统加入时, 他们需要构建额外的数据管道, 从而增加了采用新技术的成本, 同时遏制了创新.

* 元数据丢失

如果数据管道没有保留 schema 元数据, 而且不允许 schema 发生变更, 那么最终会导致生产者和消费者之间发生紧密的耦合. 如果没有 schema, 生产者和消费者需要额外的信息来解析数据. 假设数据从Oracle 流向 HDFS, 如果 DBA 在 Oracle 里添加了一个字段, 而且数据管道没有保留 schema 信息, 也不允许修改 schema, 那么应用从 HDFS 读取数据时可能会发生错误, 因此需要双方的开发人员同时升级应用程序才能解决这个问题. 不管是哪一种情况, 它们的解决方案都不具备灵活性. 如果数据管道允许 schema 发生变更, 应用程序各方就可以自由修改自己的代码, 而无需担心对整个系统造成破坏.

* 末端处理

我们在讨论数据转换时就已提到, 数据管道难免要做一些数据处理. 在不同的系统之间移动数据肯定会碰到不同的数据格式和不同的应用场景. 不过, 如果数据管道在这一块做的太多太细致, 那么就会给下游的系统造成一些限制. 在构建数据管道时所做的设计决定都会对下游的系统造成束缚, 比如应该保留哪些字段或应该如何聚合数据, 等等. 如果下游的系统有新的需求, 那么数据管道就要作出相应的变更, 这种方式不仅不灵活, 而且低效, 不安全. `更为灵活的方式是尽量保留原始数据的完整性, 让下游的应用自己决定如何处理和聚合数据.`

## 7.2 如何在 Connect API 和客户端 API 之间作出选择

Kafka 客户端是要被内嵌到应用程序里的, 应用程序使用它们向 Kafka 写入数据或从 Kafka 读取数据. 如果你是开发人员, 你会使用客户端将应用程序连接到 Kafka, 并修改应用程序的代码, 将数据推送到 Kafka 或者从 Kafka 读取数据.

如果要用 Kafka 连接两个数据存储系统, 可以使用 Connect, 因为这些存储系统不是你开发的, 你无法修改它们的代码. Connect 可以用于从外部数据存储系统读取数据, 或者将数据推送到外部存储系统. 如果数据存储系统提供了相应的`连接器`, 那么非开发人员就可以通过配置连接器的方式来使用 Connect.

如果你要连接的数据存储系统没有相应的`连接器`, 那么可以考虑使用客户端 API 或 Connect API开发一个. 我们建议首选 Connect, 因为它提供了一些开箱即用的特性, 比如配置管理, 偏移量存储, 井行处理, 错误处理, 而且支持多种数据类型及标准的 REST 管理 API.

## 7.3 Kafka Connect

Connect 是 Kafka 的一部分, 它提供了一种可靠且可伸缩的方式, 用来支持在 Kafka 和外部数据存储系统之间移动数据. 它为连接器插件提供了一组 API 和一个运行时环境(Connect 负责运行插件, 插件则负责移动数据).

Connect 以 Worker 进程集群(相当于多个客户端)的方式运行，我们使用 Worker 进程加载连接器插件，然后使用 REST API来管理和配置`连接器(Connector)`, 这些 Worker 进程都是长时间持续运行的作业. 连接器还会启动额外的 task, 有效地利用工作节点的资源, 以并行的方式移动大量的数据. 数据源的连接器负责从源系统读取数据, 并把数据对象提供给 Worker 进程. 数据池的连接器负责从 Worker 进程获取数据, 井把它们写入目标系统. Connect 通过连接器在 Kafka 里存储不同格式的数据. Kafka 支持JSON, 而且Confluent Schema Registry 提供了 Avro 转换器. 开发人员可以选择数据的存储格式, 这些与所使用的连接器无关.

>单机模式
要注意, Connect 也支持单机模式. 单机模式与分布式模式类似, 只是在启动时使用 bin/Connect-standalone.sh 代替 bin/Connect-disributed.sh, 也可以通过命令行传入连接器的配置文件，这样就不需要使用 REST API 了. 在单机模式下, 所有的连接器和任务都运行在唯一的 worker 进程上. 单机模式使用起来更简单, 特别是在开发和诊断问题的时候, 或者是在需要让连接器和任务运行在某台特定机器上的时候.

### 7.3.4 深入理解 Connect

1. 连接器和任务

连接插件实现了 Connect API, API 包含了两部分内容.

#### 连接器

连接器负责以下3件事情.

* 决定需要运行多少个任务.
* 按照任务来拆分数据复制.
* 从 Worker 进程获取任务配置并将其传递下去. 例如, JDBC 连接器会连接到数据库, 统计需要复制的数据表, 并确定需要执行多少个任务, 然后在配置参数 max.tasks 和实际数据量之间选择数值较小的那个作为任务数. 在确定了任务数之后, 连接器会为每个任务生成一个配置, 配置里包含了连接器的配置项(比如 connection.url)和该任务需要复制的数据表. taskConfigs() 方法也返回一个映射列表, 这些映射包含了任务的相关配置. Worker 进程负责启动和配置任务, 每个任务只复制配置项里指定的数据表. 如果通过 REST API 启动连接器, 有可能会启动任意节点上的连接器, 那么连接器的任务就在该节点上执行.

#### 任务

任务负责将数据移入或移出 Kafka. 任务在初始化时会得到由 Worker 进程分配的一个上下文--`源系统上下文`(Source Context) 包含了一个对象, 可以将源系统记录的偏移量保存在上下文里(例如, 文件连接器的偏移量就是文件里的字节位置, JDBC 连接器的偏移量可以是数据表的主键 ID). 目标系统连接器的上下文提供了一些方法, 连接器可以用它们操作从 Kafka 接收到的数据, 比如进行数据清理, 错误重试或者将偏移量保存到外部系统以便实现仅一次传递. 任务在完成初始化之后, 就开始按照连接器指定的配置(包含在一个 Properties 对象里)启动工作. 源系统任务对外部系统进行轮询, 并返回一些记录, worker 进程将这些记录发送到 Kafka. 数据池任务通过 Worker 进程接收来自 Kafka 的记录, 井将它们写入外部系统.

2. worker 进程

`worker 进程是连接器和任务的 "容器".` 它们负责处理 HTTP 请求, 这些请求用于定义连接器和连接器的配置. 它们还负责保存连接器的配置, 启动连接器和连接器任务, 并把配置信息传递给任务. 如果一个Worker 进程停止工作或者发生崩溃, 集群里的其他 Worker 进程会感知到(Kafka 的消费者协议提供了心跳检测机制), 并将崩溃进程的连接器和任务重新分配给其他进程. 如果有新的进程加入集群, 其他进程也会感知到, 并将自己的连接器和任务分配给新的进程, 确保工作负载的均衡. 进程还负责提交偏移量, 如果任务抛出异常, 可以基于这些偏移量进行重试. `为了更好地理解 Worker 进程, 我们可以将其与连接器和任务进行简单的比较. 连接器和任务负责 "数据的移动", 而 Worker 进程负责 REST API, 配置管理, 可靠性, 高可用性, 伸缩性和负载均衡.`

这种关注点分离是 Connect API 给我们带来的最大好处, 而这种好处是普通客户端 API 所不具备的. 有经验的开发人员都知道, 编写代码从 Kafka 读取数据并将其插入数据库只需要一到两天的时间, 但是如果要处理好配置, 异常, REST API, 监控, 部署, 伸缩, 失效等问题, 可能需要几个月. 如果你使用连接器来实现数据复制, 连接器插件会为你处理掉一大堆复杂的问题.

3. 转化器和 Connect 的数据模型

数据模型和转化器是 Connect API 需要讨论的最后一部分内容. Connect 提供了一组数据 API, 它们包含了数据对象和用于描述数据的 schema. 例如, JDBC连接器从数据库读取了一个字段, 并基于这个字段的数据类型创建了一个 Connect Schema 对象. 然后使用这些 Schema 对象创建一个包含了所有数据库字段的 Struct--我们保存了每一个字段的名字和它们的值. 源连接器所做的事情都很相似--从源系统读取事件, 并为每个事件生成 schema 和值(值就是数据对象本身). 目标连接器正好相反, 它们获取 schema 和值, 并使用 schema 来解析值, 然后写入到目标系统.

源连接器只负责基于 Data API 生成数据对象, 那么 worker 进程是如何将这些数据对象保存到 Kafka 的? 这个时候, 转换器就派上用场了. 用户在配置 worker 进程(或连接器)时可以选择使用合适的转化器, 用于将数据保存到 Kafka. 目前可用的转化器有 Avro, JSON 和 String. JSON 转化器可以在转换结果里附带上 schema, 当然也可以不使用 schema, 这个是可配的. Kafka 系统因此可以支持结构化的数据和半结构化的数据. 连接器通过 Data API 将数据返回给 worker 进程, worker 进程使用指定的转化器将数据转换成 Avro 对象, JSON 对象或者字符串, 然后将它们写入 Kafka.

对于目标连接器来说, 过程刚好相反--在从 Kafka 读取数据时, worker 进程使用指定的转换器将各种格式(Avro, JSON 或 String)的数据转换成 Data API 格式的对象, 然后将它们传给目标连接器, 目标连接器再将它们插入到目标系统.

Connect  API 因此可以支持多种类型的数据, 数据类型与连接器的实现是相互独立的--只要有可用的转换器, 连接器和数据类型可以自由组合.

4. 偏移量管理

worker 进程的 REST API 提供了部署和配置管理服务, 除此之外, worker 进程还提供了`偏移量管理服务`. 连接器只要知道哪些数据是已经被处理过的，就可以通过 Kafka 提供的 API 来维护偏移量.

源连接器返回给 worker 进程的记录里包含了一个`逻辑分区`和一个`逻辑偏移量`. `它们并非 Kafka 的分区和偏移量, 而是源系统的分区和偏移量.` 例如, 对于文件源来说, 分区可以是一个文件, 偏移量可以是文件里的一个行号或者字符号; 而对于 JDBC 源来说, 分区可以是一个数据表, 偏移量可以是一条记录的主键. 在设计一个源连接器时, 要着重考虑如何对源系统的数据进行分区以及如何跟踪偏移量, 这将影响连接器的并行能力, 也决定了连接器是否能够实现至少一次传递或者仅一次传递.

源连接器返回的记录里包含了源系统的分区和偏移量, worker 进程将这些记录发送给 Kafka. 如果Kafka 确认记录保存成功, worker 进程就把偏移量保存下来. 偏移量的存储机制是可插拔的, 一般会使用 Kafka 主题来保存. 如果连接器发生崩溃并重启, 它可以从最近的偏移量继续处理数据.

目标连接器的处理过程恰好相反, 不过也很相似. 它们从 Kafka 上读取包含了主题, 分区和偏移量信息的记录, 然后调用连接器的 put() 方法, 该方法会将记录保存到目标系统里. 如果保存成功, 连接器会通过消费者客户端将偏移量提交到 Kafka 上.

框架提供的偏移量跟踪机制简化了连接器的开发工作, 并在使用多个连接器时保证了一定程度的行为一致性.

### 7.4.3 流式处理框架

几乎所有的`流式处理`框架都具备从 Kafka 读取数据并将数据写入外部系统的能力. 如果你的目标系统支持流式处理, 井且你已经打算使用流式框架处理来自 Kafka 的数据, 那么使用相同的框架进行数据集成看起来是很合理的. 这样可以省掉一个处理步骤(不需要保存来自 Kafka 的数据, 而是直接从 Kafka 读取数据然后写到其他系统), 不过在发生数据丢失或者出现脏数据时, 诊断问题会变得很困难, 因为这些框架并不知道数据是什么时候丢失的, 或者什么时候出现了脏数据.

#8 跨集群数据镜像




