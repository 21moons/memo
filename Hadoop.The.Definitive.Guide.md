# PART I Hadoop Fundamentals
## CHAPTER 1 Meet Hadoop
### Data Storage and Analysis
存储容量与 IO 速度之间的矛盾, 数据越来越多, 但是硬盘的 IO 速度提升有限.
可能解决方法是从多个设备上并行读取, 那么有两个问题需要解决:
1. 如何解决单个设备失效导致的数据丢失(replication)
2. 如何合并多个设备上读取到的文件片段
### Beyond Batch
Hadoop 是一个生态系统:
1. MapReduce 本质上是基于批处理系统, 但是因为响应时间较长, 并不适用于强调实时性的交互性分析.
2. HBase 用来存放 key-value 格式的数据, 使用 HDFS(Hadoop Distributed Filesystem) 作为底层文件系统.HBase 既支持单行的 读写, 也支持大块数据的批处理读写.
3. YARN(Yet Another Resource Negotiator)， 集群资源管理系统, 用来调度所有的分布式程序(包括 MapReduce), 使其可以基于 Hadoop 集群中的数据来运行.
### 新增处理模式
* Interactive SQL(交互式 SQL) 
Hadoop上的SQL查询可以实现低延迟响应
* Iterative processing(迭代处理)
机器学习算法, Spark 代替 MapReduce
* Stream processing(流处理)
像 Storm, Spark Streaming 或 Samza 等流媒体系统可以运行实时分布式计算, 并将结果发送到 Hadoop 存储或外部系统
* Search
Solr 搜索平台可以在 Hadoop 集群上运行, 索引 HDFS 中的文档, 并基于 HDFS 中的索引中提供搜索服务.
### RDBMS(Relational Database Management System) compared to MapReduce
|  | Traditional RDBMS | MapReduce |
| ------| ------ | ------ |
| Data size | Gigabytes | Petabytes |
| Access | Interactive and batch | Batch |
| Updates | Read and write many times | Write once, read many times |
| Transactions | ACID | None |
| Structure | Schema-on-write | Schema-on-read |
| Integrity | High | Low |
| Scaling | Nonlinear | Linear |
<br>
关于传统关系数据库(RDBMS) 与 Hadoop 的区别, 这里提到了关键的四点:
1. 为什么需要 Hadoop, 为什么不能在传统数据库中加入更多硬盘来实现大数据分析? 因为硬盘的 IO 响应速度受寻道时间限制, 传统关系数据库读取大量数据的速度远远小于 MapReduce. MapReduce 流式读写的读取速度仅仅受传输速率的限制. 
2. MapReduce is a good fit for problems that need to analyze the whole dataset
in a batch fashion, particularly for ad hoc analysis. An RDBMS is good for point queries or updates, where the dataset has been indexed to deliver low-latency retrieval and update times of a relatively small amount of data. 
3. MapReduce suits applications where the data is written once and read many times, whereas a relational database is good for datasets that are continually updated.
4. Hadoop 和 RDBMS 之间的另一个区别在于其操作的数据集. 
在 RDBMS 中, 结构化数据被组织成定义好的格式, 例如 XML 文档或数据库表. 另一方面, 半结构化数据则比较松散, 虽然可能有格式, 但经常被忽略, 可能仅作为数据结构的指导: 例如 spreadsheet, 其结构是 cells 组成的网格, 虽然 cells 本身可以保存任何形式的数据.
非结构化数据没有任何特定的内部结构: 例如普通文本或图像数据. Hadoop 在非结构化或半结构化数据上工作良好, 因为它被设计成在处理阶段才解释数据(所谓的模式读取 schema-on-read). 这种机制提供了灵活性并避开了 RDBMS 耗时的数据加载阶段, 因为在  <font color=#fd0209 size=4 >Hadoop 中数据加载只是一个文件复制操作</font>.

关系数据通常被标准化(normalized)以保持其完整性并消除冗余. 但是规范化通常会给 Hadoop 处理带来问题, 因为它会将读取记录变为一个一个非本地操作, 而本地操作是 Hadoop 能够执行(高速)流式读写的一个重要假设.

Web 服务器日志是一个未规范化的记录集合(例如, 即使客户端是同一个客户端, 每次都记录完整的客户端主机名), 这就是各种日志文件特别适合用 Hadoop 进行分析的原因之一. 请注意，Hadoop 可以执行 join 操作; 只是并没有关系数据库中用的那么频繁。

MapReduce 和 Hadoop 中的其他处理模型, 处理时间随数据量大小线性变化. 数据是分区的, 功能原语(如 map 和 reduce)可以在所有的分区上并行工作. 这意味着如果你输入的数据量翻倍, 分析需要的时间也将会是原来的两倍. 但是, 如果你同时还将集群的大小加倍, 那么分析工作将会像原来一样快. 而 RDBMS 中的 SQL 查询通常不是这样.
### Grid Computing (网格计算)
高性能计算(HPC high-performance computing)和网格计算社区已经使用 API 作为消息传递接口(MPI Message Passing Interface), 进行了多年的大规模数据处理. 大体上, HPC 的方法是将工作分配到机器集群中, 集群中的机器通过存储区域网络(SAN storage area network)访问共享文件系统. HPC 主要适用于计算密集型作业, 但是当节点需要访问大量数据时, 问题出现了, 此时网络带宽变成了瓶颈, 导致计算节点闲置.
Hadoop 试图将数据与计算节点放在一起, 因为数据都在本地, 所以访问速度很快. 这个功能称为数据本地化(data locality), 是 Hadoop 数据处理的核心, 也是其性能良好的原因. 意识到网络带宽是数据中心环境中最宝贵的资源, Hadoop 通过网络拓扑建模来竭尽全力的保护它. 注意这个安排并不妨碍 Hadoop 中的 CPU 密集型的分析任务。
MPI 不仅要求程序员显式的控制底层的数据流, 也要求程序员熟悉顶层的分析算法. 但是在 Hadoop 中, 程序要只需要考虑数据模型, 底层的数据流是不可见的.
协调大规模分布式计算中的任务是一个挑战. 最难的地方是优雅地处理部分任务失败 (此时你不知道一个远程任务是否失败), 同时其他的计算过程还在继续运行(暗示不可能全部重来). MapReduce 这样的分布式处理框架让程序员不必考虑失败, 因为框架会检测到失败任务并在健康的机器上重新调度. MapReduce 之所以能够做到这一点, 是因为它是一个无共享(shared-nothing)架构， 这意味着任务之间没有相互依赖. (这里有轻微的简化, 因为 mappers 的输出是reducer 的输入, reducer 对 mapper 有依赖, 但是这仍然在 MapReduce 系统的控制下; 重新运行一个失败的 reducer 要比重新运行一个失败的 map 更小心, 因为 reducer 必须能获取到必要的 map 输出, 如果不能, 则通过再次调度相关的 map  来重新生成) 所以从程序员的角度来看, 任务运行的先后顺序并不重要. 相比之下, MPI 程序必须明确地管理自己的检查点并恢复(遇到部分任务失败时), 程序员拥有更多的控制权, 却使程序更难写.
### Volunteer Computing
志愿者计算项目将他们正在尝试的问题分解成工作单位(work units), 然后发送到世界各地的电脑进行分析. 例如, 一个 SETI@home 工作单位包含 0.35 MB 的射电望远镜数据, 并在一台典型的家用电脑上花几个小时或几天的时间来分析. 分析完成后, 结果被发送回服务器, 然后客户端获取另一个工作单元. 为了防止作弊, 每个工作单位都被送到三台不同的机器, 至少其中的两个结果一致才能被接受。
SETI@home 项目要解决的问题是 CPU 密集型的, 适合在分布于世界各地的数十万台电脑上运行, 因为传输工作单位的时间是相当短的. 志愿者捐献的是 CPU 周期, 而不是带宽.
MapReduce 旨在在一个由可信的硬件组成的高带宽数据中心上运行持续几分钟或几小时的作业. 相比之下, SETI@home 在不受控互联网的不可信机器上运行不间断的计算, 而且没有本地化数据.
<br>
![](https://raw.githubusercontent.com/21moons/memo/master/res/img/hadoop/hadoop_modules.png)
<br>
<br>
## CHAPTER 2 MapReduce
MapReduce 是关于数据处理的 <font color=#fd0209 size=5 >编程模型</font>.
Hadoop 支持运行多种语言写的 MapReduce 程序, 包括 Java, Ruby, 和 Python. 更重要的是, Most MapReduce 程序天生就是并行的.

### A Weather Dataset
### Analyzing the Data with Hadoop
#### Map and Reduce
MapReduce 任务分为两个阶段: map 阶段和 reduce 阶段. 每个阶段输入和输出的格式都是键值对; 键和值的类型可以由程序员自行指定. 程序员还实现了两个函数: map 函数和reduce 函数.
<br>
![](https://raw.githubusercontent.com/21moons/memo/master/res/img/hadoop/MapReduce_logical_data_flow.png)
<p align="center"><font size=2>Figure 2-1. MapReduce logical data flow</font><\p>
<br>
<br>
#### Java MapReduce
``` java
import java.io.IOException;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.LongWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Mapper;

public class MaxTemperatureMapper
        extends Mapper<LongWritable, Text, Text, IntWritable> {

    private static final int MISSING = 9999;

    @Override
    public void map(LongWritable key, Text value, Context context)
        throws IOException, InterruptedException {
        String line = value.toString();
        String year = line.substring(15, 19);
        int airTemperature;

        if (line.charAt(87) == '+') { // parseInt doesn't like leading plus signs
            airTemperature = Integer.parseInt(line.substring(88, 92));
        } else {
            airTemperature = Integer.parseInt(line.substring(87, 92));
        }

        String quality = line.substring(92, 93);

        if (airTemperature != MISSING && quality.matches("[01459]")) {
            context.write(new Text(year), new IntWritable(airTemperature));
        }
    }
}
```

Mapper 类包括四个参数:  input key, input value, output key, output value

