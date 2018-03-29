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
3. YARN(Yet Another Resource Negotiator),  集群资源管理系统, 用来调度所有的分布式程序(包括 MapReduce), 使其可以基于 Hadoop 集群中的数据来运行.
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

Web 服务器日志是一个未规范化的记录集合(例如, 即使客户端是同一个客户端, 每次都记录完整的客户端主机名), 这就是各种日志文件特别适合用 Hadoop 进行分析的原因之一. 请注意, Hadoop 可以执行 join 操作; 只是并没有关系数据库中用的那么频繁. 

MapReduce 和 Hadoop 中的其他处理模型, 处理时间随数据量大小线性变化. 数据是分区的, 功能原语(如 map 和 reduce)可以在所有的分区上并行工作. 这意味着如果你输入的数据量翻倍, 分析需要的时间也将会是原来的两倍. 但是, 如果你同时还将集群的大小加倍, 那么分析工作将会像原来一样快. 而 RDBMS 中的 SQL 查询通常不是这样.
### Grid Computing (网格计算)
高性能计算(HPC high-performance computing)和网格计算社区已经使用 API 作为消息传递接口(MPI Message Passing Interface), 进行了多年的大规模数据处理. 大体上, HPC 的方法是将工作分配到机器集群中, 集群中的机器通过存储区域网络(SAN storage area network)访问共享文件系统. HPC 主要适用于计算密集型作业, 但是当节点需要访问大量数据时, 问题出现了, 此时网络带宽变成了瓶颈, 导致计算节点闲置.
Hadoop 试图将数据与计算节点放在一起, 因为数据都在本地, 所以访问速度很快. 这个功能称为数据本地化(data locality), 是 Hadoop 数据处理的核心, 也是其性能良好的原因. 意识到网络带宽是数据中心环境中最宝贵的资源, Hadoop 通过网络拓扑建模来竭尽全力的保护它. 注意这个安排并不妨碍 Hadoop 中的 CPU 密集型的分析任务. 
MPI 不仅要求程序员显式的控制底层的数据流, 也要求程序员熟悉顶层的分析算法. 但是在 Hadoop 中, 程序要只需要考虑数据模型, 底层的数据流是不可见的.
协调大规模分布式计算中的任务是一个挑战. 最难的地方是优雅地处理部分任务失败 (此时你不知道一个远程任务是否失败), 同时其他的计算过程还在继续运行(暗示不可能全部重来). MapReduce 这样的分布式处理框架让程序员不必考虑失败, 因为框架会检测到失败任务并在健康的机器上重新调度. MapReduce 之所以能够做到这一点, 是因为它是一个无共享(shared-nothing)架构,  这意味着任务之间没有相互依赖. (这里有轻微的简化, 因为 mappers 的输出是reducer 的输入, reducer 对 mapper 有依赖, 但是这仍然在 MapReduce 系统的控制下; 重新运行一个失败的 reducer 要比重新运行一个失败的 map 更小心, 因为 reducer 必须能获取到必要的 map 输出, 如果不能, 则通过再次调度相关的 map  来重新生成) 所以从程序员的角度来看, 任务运行的先后顺序并不重要. 相比之下, MPI 程序必须明确地管理自己的检查点并恢复(遇到部分任务失败时), 程序员拥有更多的控制权, 却使程序更难写.
### Volunteer Computing
志愿者计算项目将他们正在尝试的问题分解成工作单位(work units), 然后发送到世界各地的电脑进行分析. 例如, 一个 SETI@home 工作单位包含 0.35 MB 的射电望远镜数据, 并在一台典型的家用电脑上花几个小时或几天的时间来分析. 分析完成后, 结果被发送回服务器, 然后客户端获取另一个工作单元. 为了防止作弊, 每个工作单位都被送到三台不同的机器, 至少其中的两个结果一致才能被接受. 
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
<p align="center"><font size=2>Figure 2-1. MapReduce logical data flow</font></p>
<br>
<br>

#### Java MapReduce

**map function**

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
<p align="center"><font size=2>Example 2-3. Mapper for the maximum temperature example</font></p>

Mapper 类包括四个参数:  input key, input value, output key, output value types

为了针对网络序列化场景进行优化, Hadoop 提供了一组自己的基本类型, 用于替换原生 Java 类型. 这些类型可以在 org.apache.hadoop.io 包中找到. 这里我们使用的 LongWritable, 它对应于 Java 中的 Long 类型, Text 对应 Java 中的 字符串, 和 IntWritable 对应 Java 中的 Integer.

**reduce function**

``` java
import java.io.IOException;

import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Reducer;

public class MaxTemperatureReducer
        extends Reducer<Text, IntWritable, Text, IntWritable> {

    @Override
    public void reduce(Text key, Iterable<IntWritable> values, Context context)
        throws IOException, InterruptedException {
        int maxValue = Integer.MIN_VALUE;
        for (IntWritable value : values) {
            maxValue = Math.max(maxValue, value.get());
        }
        
        context.write(key, new IntWritable(maxValue));
    }
}
```
<p align="center"><font size=2>Example 2-4. Reducer for the maximum temperature example</font></p>


**some code to run the job**

``` java
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;

public class MaxTemperature {

    public static void main(String[] args) throws Exception {
        if (args.length != 2) {
            System.err.println("Usage: MaxTemperature <input path> <output path>");
            System.exit(-1);
        }

        Job job = new Job();
        job.setJarByClass(MaxTemperature.class);
        job.setJobName("Max temperature");

        FileInputFormat.addInputPath(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));

        job.setMapperClass(MaxTemperatureMapper.class);
        job.setReducerClass(MaxTemperatureReducer.class);

        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);

        System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```
<p align="center"><font size=2>Example 2-5. Application to find the maximum temperature in the weather dataset</font></p>

在 Hadoop 集群上运行任务时, 我们将把代码打包成 jar 包, 然后通过方法 setJarByClass() 指定需要加载的类名

构造 Job 对象后, 我们指定 input 和 output 路径.

接下来, 我们通过 setMapperClass() 和 setReducerClass() 方法指定 mapper 类和 reduce 类的类型.

setOutputKeyClass() 和 setOutputValueClass() 方法设置 reduce 函数输出结果的类型, 并且必须与 reduce 类生成的内容相匹配. map 函数的输出类型默认和 reduce 是相同的, 所以通常情况下不需要单独设置. 但是, 如果输出类型不同的话, 必须使用 setMapOutputKeyClass() 和 setMapOutputValueClass() 方法来设置 map 输出类型.
<br>

#### Data Flow

&emsp;&emsp;首先做一些术语解释. MapReduce job 是客户希望执行的某项工作: 它由输入数据, MapReduce 程序和配置信息组成. Hadoop 通过将 job 分成多个任务来执行, 这些任务分为两种类型: map 任务和 reduce 任务. 这些任务使用 YARN 进行调度, 在集群中的节点上运行. 如果某个任务失败, 它将被自动调度到其他节点上再次运行. 

&emsp;&emsp;Hadoop 将输入分成固定大小的片段, 然后提交给 MapReduce 任务. 我们将这些片段称之为 input splits 或是 splits. Hadoop 为每个片段创建一个 map 任务, 该任务基于片段中的每条记录运行用户定义的 map 函数.

&emsp;&emsp;对数据进行分片意味着相对于处理整个输入数据集, 处理每个分片所需时间会比较少. 假设我们并行的处理这些分片, 那么在分片越小的情况下, 更容易达成服务器之间的负载均衡, 因为在任务运行过程中, 机器越快, 处理的分片将越多. 即使这些机器没有差异, 考虑到进程可能失败或存在其他正在运行的作业, 负载均衡也是可取的, 而且分割的粒度越细, 负载平衡的质量越好.

&emsp;&emsp;另一方面, 如果分片太小, 则管理分片和创建 map 任务的开销开始主宰整个 job 的执行时间. 对于大多数 job 来说, 分片大小设置为 HDFS 块的大小将是比较合适的, 默认情况下为 128 MB, 当然, HDFS 块的大小可以基于集群(所有新创建的文件)进行更改, 或者在文件创建时进行指定.

&emsp;&emsp;Hadoop 尽量在输入数据所在的 HDFS 节点上运行 map 任务, 这样可以不占用宝贵的集群带宽. 该特性被称为数据本地优化(data locality optimization). 然而有些时候, 所有存储任务所需分片及其副本的 HDFS 块所在的节点都在满负载运行 map 任务, 此时作业调度程序将尝试在同一个机架的其他节点上运行任务. 如果同一机架上没有空闲的节点, 尽管这种情况非常少见甚至是不可能的, 此时会使用机架外节点, 这将导致机架间的网络传输. 图 2-2 描述了这三种情况.

&emsp;&emsp;现在应该清楚为什么最佳分片的大小就是 HDFS 文件系统块的大小: 它是能够确认存储在单个节点上的最大大小. 如果分片跨越两个或更多的块, 任何 HDFS 节点都不太可能同时存储这两个块(HDFS 文件系统的特性), 一些分片将不得不通过网络传输到正在运行 map 任务的节点上, 这显然比数据全部在本地运行效率要低.

&emsp;&emsp;Map 任务将其输出写入本地磁盘, 而不是 HDFS. 为什么会这样?因为 Map 的输出是中间输出: 它们随后将交给 reduce 任务处理并生成最终输出, 并且一旦 job 完成后, map 任务的输出可以丢弃. 因此, 将它存储在 HDFS 上是一种浪费. 如果节点运行 map 任务失败, 没有生成中间结果, 然后又被 reduce 任务占用, 那么 Hadoop 将自动在另一个节点上重新运行 map 任务.



<p align="center"><font size=2>Figure 2-2. Data-local (a), rack-local (b), and off-rack (c) map tasks</font></p>
<br>

&emsp;&emsp;对于 Reduce 任务来说, 并不存在本地处理数据优势; 单个 reduce 任务的输入通常是所有 mappers 的输出. 在当前的例子中, 我们使用一个 reduce 任务处理所有 map 任务的输出. 因此, 排序后的 map 输出必须通过网络传输到 reduce 任务运行的节点上, 它们在那里被合并, 然后传递给用户定义的 reduce 函数. reduce 任务的输出通常存储在 HDFS 中以保证可靠性. 正如第3章所解释的那样 HDFS, 对于每一个存储 reduce 输出的 HDFS 块, 通过把第一个副本存储在本地节点上, 其他副本存储在机架外节点上, 来保证可靠性. 因此, 在 HDFS 上写入 reduce 任务的输出确实消耗了网络带宽, 但属于正常的 HDFS 写入消耗(并没有引入性能损失).

&emsp;&emsp;图 2-3 描述了单个 reduce 任务的整个数据流. 虚线框表示节点, 虚线箭头表示节点上的数据传输, 实线箭头显示节点之间的数据传输.


<p align="center"><font size=2>Figure 2-3. MapReduce data flow with a single reduce task</font></p>
<br>

&emsp;&emsp;reduce 任务的数量不受输入大小的控制, 而是单独指定的. 在 214 页的 "The Default MapReduce Job" 中, 你将看到如何为特定作业选择 reduce 任务的数量. 

&emsp;&emsp;当有多个 reducers 时, map 任务将对其输出进行分区, 每个 map 任务都会为每个 reduce 任务创建一个分区. 分区中可以有许多键(和它们的相关值), 但任何给定 key 的记录都全部在某个分区中(key 一定对应一个分区, 一个分区对应多个 key). 分区可以由用户定义的分区函数来控制, 但通常情况下, 使用默认分区程序(使用 hash 函数对 keys 进行存储)就已经很好了. 

&emsp;&emsp;图 2-4 说明了多个 reduce 任务通常情况下的数据流. 该图清楚地说明了为什么 map 和 reduce 任务之间的数据流被称为"洗牌(the shuffle)", 因为每个 reduce 任务都由许多 map 任务提供. 实际的洗牌比图表中描述的更加复杂, 调整它对 job 执行时间有巨大影响, 你将在 197 页中的"Shuffle and Sort 随机排序"中看到. 

<p align="center"><font size=2>Figure 2-4. MapReduce data flow with multiple reduce tasks</font></p>

&emsp;&emsp;最后提一下, 我们也可以不需要 reduce 任务. 此时因为处理流程可以完全并行进行而不需要洗牌(234 页的 "NLineInputFormat" 中讨论了一些例子). 在这种情况下, 唯一的节点间数据传输是 map 任务将输出写入 HDFS 文件系统(参见图 2-5).
<br>

### Combiner Functions

许多 MapReduce job 受到集群上可用带宽的限制, 因此在最小化 map 任务和 reduce 任务间的数据传输上耗费了不少精力. Hadoop 允许用户指定一个 combiner 函数, 运行在 map 任务的输出上, combiner 函数的输出作为 reduce 函数的输入. 因为 combiner 函数只是一个优化, Hadoop 不会保证它会调用combiner 函数多少次. 换句话说, 无论调用 combiner 函数零次, 单次还是多次, reducer 的输出结果都应该是一致的.

<p align="center"><font size=2>Figure 2-5. MapReduce data flow with no reduce tasks</font></p>
<br>



#### Specifying a combiner function



``` java
public class MaxTemperatureWithCombiner {

    public static void main(String[] args) throws Exception {
        if (args.length != 2) {
            System.err.println("Usage: MaxTemperatureWithCombiner <input path> " + "<output path>");
            System.exit(-1);
        }

        Job job = new Job();
        job.setJarByClass(MaxTemperatureWithCombiner.class);
        job.setJobName("Max temperature");

        FileInputFormat.addInputPath(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));

        job.setMapperClass(MaxTemperatureMapper.class);
        job.setCombinerClass(MaxTemperatureReducer.class);
        job.setReducerClass(MaxTemperatureReducer.class);

        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);

        System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```
<p align="center"><font size=2>Example 2-6. Application to find the maximum temperature, using a combiner function for efficiency</font></p>



### Hadoop Streaming
Hadoop Streaming 使用 Unix 标准流作为 Hadoop 和你的程序之间的接口, 所以你可以使用任何支持读取标准输入和写入标准输出语言的来编写你的 MapReduce 程序. 
对于 C ++程序员, 可以使用 Hadoop Pipes.


## CHAPTER 3 The Hadoop Distributed Filesystem

Hadoop Distributed Filesystem(HDFS)

对于分布式文件系统, 挑战之一就是在节点故障时而不丢失数据.
Hadoop 实际上有一个通用文件系统抽象.

### The Design of HDFS
* Very large files
* Streaming data access
最有效的数据处理模式是一次写入, 多次读取, HDFS 基于这个理念来构建. 数据集通常从数据源生成或复制, 随后基于数据集进行各种分析.
每次分析都会涉及很大一部分(如果不是全部)数据集, 那么读取整个数据集花费的时间比读取数据集中第一条记录的延迟更重要(言下之意是吞吐量比单个文件的寻道时间更重要).
* Commodity hardware
* Low-latency data access
需要低延迟访问数据的应用程序, 例如数十毫秒范围, 不适合用 HDFS 存储. 请记住, HDFS 已针对吞吐量进行了优化, 这是以高延迟为代价的. HBase(参见第 20 章) 是低延迟访问的更好选择. 
* Lots of small files
由于 namenode 在内存中保存文件系统元数据, 因此文件系统中的文件数量由 namenode 的内存量决定. 
* Multiple writers, arbitrary file modifications
HDFS 中的文件写入操作是互斥的. 文件写入操作总是在文件的结尾添加数据. 不支持同时写入或修改文件中的任意偏移量.(未来可能会被支持, 但会相对低效)

### HDFS Concepts
* **Blocks**

Block 是磁盘能够读取或写入的最小单位.

HDFS 也有块的概念, 但它是一个更大的单位 - 默认情况下为128 MB. 就像单个磁盘上的文件系统, HDFS 中的文件也是以数据块的方式来存放. 与之不同的是, 当文件大小小于块大小时, 不占用块中的所有空间.

HDFS 中的块设置的比较大, 是为了减少寻道时间.

MapReduce 中的 map 任务一次只操作一个块.

对分布式文件系统进行块抽象带来了一些好处.

首先文件可以大于网络中的任何单个磁盘, 不要求将文件中的块存储在同一个磁盘上, 所以大型文件可以利用群集中的任何磁盘. 

其次, 选择块作为抽象单元而不是文件可以简化存储子系统. 简单是所有系统的目标, 但对于分布式系统来说尤其重要, 因为分布式系统失效模式实在是太多了. 

存储子系统处理块, 简化存储管理(因为块都是固定大小, 很容易计算出在给定的磁盘上可以存储多少)并消除元数据问题(因为块中只有数据, 文件元数据, 如权限信息不需要与块一起存储, 由另一个独立的系统单独处理).

此外, 分布式文件系统使用复制以提供容错和可用性, 块很适合这样的场景. 为了在块数据丢失, 磁盘和机器故障等场景下保证数据可用, 每个块都会复制到少量物理独立的机器上(通常是三个). 如果一个块不可用, 可以在客户不感知的情况下读取副本. 由于以为物理原因导致块永久性损失, 那么文件系统会读取副本并复制到其他机器上, 使复制因子回到正常水平. (更多信息请参阅 97 页的"数据完整性") 同样, 一些应用程序可能会针对经常读取的文件设置较高的复制因子, 从而使集群更好的支持读取操作.
```
% hdfs fsck / -files -blocks
```
列出所有文件与块的对应关系
<br>
<br>
* **Namenodes and Datanodes**

HDFS 集群有两种类型的节点: namenode(the master) 和一些 datanode(workers). namenode 管理文件系统命名空间. 它维护文件系统树和所有文件的元数据. 这些信息永久的存储在本地磁盘上, 它分为两个文件: 命名空间图像(namespace image)和编辑日志(edit log).  namenode 知道文件的每个块都在哪个的 datanode 上; 但是它不会持久化这些数据, 因为系统启动时会重建这些信息.

为了防止 namenode 单点故障, 一种解决方法是把文件元数据同步到其他远程文件系统
另一种方法是对 namenode 做热备.
<br>
* **Block Caching**

通常情况下, datanode 会从磁盘读取数据块, 但对于频繁访问的文件, 则会将数据块缓存在内存中, 这块内存被称为堆外块缓存(off-heap block cache).
<br>
* **HDFS Federation**

namenode 在内存中保存文件系统中每个文件和块的引用, 这意味着在存储很多文件的超大群集上, 内存成为限制存储集群扩展的原因(请参阅 294 页的 "Namenode 需要多少内存").

2.x 发行版中引入的 HDFS Federation 允许通过添加  来扩展集群, 每个 namenode 管理文件系统名称空间的一部分. 例如, 一个 namenode 可能会管理所有 /user 目录下的文件, 第二个节点管理 /share 目录下的文件.

HDFS federation 中的每个 namenode 管理一个名字空间卷, 它由名字空间元数据和包含空间中所有文件块的 block pool 组成. 名字空间卷彼此独立, 这意味着
namenodes 之间不会相互通信, 而且单个 namenode 的失效不会影响其他 namenode 管理的名字空间的可用性. block pool 并不分区存储, 因此 datanodes 会向每个 namenode 注册, 并存储来自多个 block pool 的块.

要访问 HDFS federation 集群, 客户使用客户侧加载表将文件路径映射到 namenodes, 配置时使用 ViewFileSystem 和 viewfs:// 格式的 URI.
<br>
* **HDFS High Availability**

通过把 namenode 上的元数据复制到多个文件系统, 同时在次要 namenode 上创建检查点可以防止数据丢失, 但它不能提供文件系统的高可用性. 所有的文件系统元数据都在 namenode 上, 如果 namenode 失效, 所有客户端(包括 MapReduce 作业)都将无法读取, 写入或列出文件, 仍然会触发单点故障 single point of failure (SPOF). 因为 namenode 是唯一存储元数据和文件-块的映射的地方. 在这种情况下, 整个 Hadoop 系统将会停止服务, 直到新的名称节点可用.

在这种情况下, 要在 namenode 失效的情况下恢复, 管理员将启动一个新的 namenode, 将文件系统元数据复制过来, 并配置 datanode 和客户端使用这个新的 namenode. 新的 namenode 在完成下列步骤前无法响应请求:
1. 将 namespace image 加载到内存  
2. 重放 edit log
3. 从 datanode 接收足够的块报告, 离开安全模式. 

在大型集群上, namenode 冷启动需要 30 分钟或更多时间.

恢复时间长也是日常维护的一个问题. 实际环境中, 因为 namenode 故障非常罕见, 计划停机的情况在实践中更重要. 

Hadoop 2 通过添加对 HDFS 高可用性 (HA) 的支持弥补了这种情况. 通过配置一对主备 namenode, 在主 namenode 节点失效的情况下, 备 namenode 接管其职责
继续为客户提供服务, 从而不造成重大中断. 该方案涉及几个架构层面的修改:

* namenodes 必须使用高可用共享存储来共享 edit log. 当备节点升为主节点时, 它通过读取共享 edit log, 使其状态与主节点同步, 然后继续读取之前主节点写入的新条目. 

* 数据节点必须将块报告发送到主备名称节点, 因为文件与块的映射关系存放在 namenode 节点的内存中, 而不是磁盘上. 

* 必须为客户端配置一种机制来处理 namenode 故障恢复, 使其对用户透明. 

* 备用名称节点需要定期基于主 namenode 名称空间创建检查点. 


高可用性共享存储有两种选择:NFS 文件管理器或 quorum journal manager (QJM). QJM 是一个专用的 HDFS 实现, 唯一目就是提供高可用的 edit log, 是 HDFS 部署的推荐选择. QJM 作为一组日志节点运行, 每次编辑必须写入大多数日志节点. 通常情况下会配置三个日志节点, 所以系统可以容忍其中一个节点的丢失.  这种安排与 ZooKeeper 的工作方式类似, 但是 QJM 并没有使用 ZooKeeper. (但是请注意, HDFS HA 确实使用 ZooKeeper 用于选择主 namenode, 如下一节所述.) 

如果主 namenode 失败, 备用可以很快接管 (几十秒内), 因为备节点的内存中已经有最新状态: 包括最新的 edit log 条目和最新的文件-块映射.  实际观察到的故障恢复时间将会慢一些 (大约一分钟左右), 因为系统需要谨慎的确认 主 namenode 节点是否失败. 

如果主节点失败时备节点也同时出现故障, 尽管这种情况不太可能, 管理员仍然可以对备节点实施冷启动.  这并不比没有 HA 的情况更糟, 并且从操作的角度来看, 这是一个改进, 因为这个过程是一个标准的 Hadoop 内置操作流程. 


#### Failover and fencing


从主节点到备节点的转换由一个新的实体管理, 该系统称为故障转移控制器(failover controller). 有很多种故障切换控制器, 默认实现使用 ZooKeeper 来确保只有一个名称节点处于活动状态. 每个 namenode 都运行一个轻量级故障转移控制器进程, 该进程将监视其工作状态 (使用简单的心跳机制), 一旦失败则触发故障恢复. 

故障转移也可以由管理员手动触发, 例如, 在例行维护时. 因为这是故障恢复控制器主动命令主备节点切换角色, 这被称为优雅故障恢复. 

如果倒换属于非优雅故障恢复, 则不能确定失败的 namenode 是否真的停止运行. 例如, 即使主名称节点仍然存在并正常运行, 低速网络或网络分区也会触发故障恢复. HA 的实现尽全力确保在这种情况下, 先前的主节点不会造成任何损坏, 这被称为围栏(fencing). 

QJM 在同一时刻只允许一个 namenode 写入 edit log; 但是, 在双主情况下, 先前的主 namenode 仍然可以为客户端提供陈旧的读取请求, 因此发送一个可以终止 namenode 进程的SSH fencing 命令是一个不错的想法. 使用 NFS 文件系统共享 edit log 时, 则需要更强大的防护方法, 因为没法保证同一时间只有一个 namenode 编辑 edit log(这就是为什么建议使用 QJM).  围栏机制的职责包括撤销名称节点访问共享存储目录的权限 (通常使用特定于供应商的NFS命令) , 并通过远程管理命令禁用其网络端口. 作为最后的手段, 还有另一种称为 STONITH 的方法, 或者叫做 "对节点进行爆头", 它使用专门的配电单元强制关闭前主 namenode 节点所在的主机. 

客户端的故障转移适配由客户端库自行处理. 最简单的实现是使用客户端配置来控制故障转移. HDFS URI 将逻辑主机名映射到一对 namenode 地址 (在配置文件中), 客户端库依次尝试每个namenode地址, 直到操作成功. 
<br>

### The Command-Line Interface

```
hadoop fs -help                      -- 帮助
hadoop fs -copyFromLocal input/docs/quangle.txt hdfs://localhost/user/tom/quangle.txt   -- 复制
hadoop fs -mkdir books               -- 创建目录, 目录保存在 namenode
hadoop fs -ls .                      -- 列出文件
hadoop fs -ls file:///               -- 查看本地文件系统类型
```
<br>

### Hadoop Filesystems

| Filesystem | URI scheme | Java implementation | Description |
| ------| ------ | ------ | ------ |
| Local  | file | fs.LocalFileSystem | A filesystem for a locally connected disk with client-side checksums. Use RawLocal FileSystem for a local filesystem with no checksums. See "LocalFileSystem" on page 99. |
| HDFS | hdfs | hdfs.DistributedFileSystem | Hadoop’s distributed filesystem. HDFS is designed to work efficiently in conjunction with MapReduce. |
| WebHDFS | webhdfs | hdfs.web.WebHdfsFileSystem | A filesystem providing authenticated read/write access to HDFS over HTTP. See "HTTP" on page 54. |
| Secure WebHDFS | swebhdfs | hdfs.web.SWebHdfsFileSystem | The HTTPS version of WebHDFS. |
| HAR | har | fs.HarFileSystem | A filesystem layered on another filesystem for archiving files. Hadoop Archives are used for packing lots of files in HDFS into a single archive file to reduce the namenode’s memory usage. Use the  hadoop archive command to create HAR files. |
| View | viewfs | viewfs.ViewFileSystem | A client-side mount table for other Hadoop filesystems. Commonly used to create mount points for federated namenodes (see “HDFS Federation” on page 48). |
| FTP | ftp | fs.ftp.FTPFileSystem | A filesystem backed by an FTP server. |
| S3 | s3a | fs.s3a.S3AFileSystem | A filesystem backed by Amazon S3. Replaces the older s3n (S3 native) implementation. |
| Azure | wasb | fs.azure.NativeAzureFileSystem | A filesystem backed by Microsoft Azure. |
| Swift | swift | fs.swift.snative.SwiftNativeFile System | A filesystem backed by OpenStack Swift. |

<p align="center"><font size=2>Table 3-1. Hadoop filesystems</font></p>

#### Interfaces


* **HTTP**

Hadoop 通过将其文件系统接口公开为 Java API, 来支持 Java 应用程序访问 HDFS. 而对于非 java 语言, 则需要通过 WebHDFS 协议与 HTTP REST API 交互. 要注意的是，HTTP 接口比本地 Java 客户端慢, 因此如果使用 HTTP 接口, 应该尽可能的避免转移非常大的数据.

有两种方法可以通过 HTTP 访问 HDFS: 直接访问 HDFS, HDFS 守护进程向客户端提供 HTTP 服务; 通过访问 HDFS 的代理客户端代表使用通常的 DistributedFileSystem API. 这两种方式是如图 3-1 所示. 两者都使用 WebHDFS 协议。


<p align="center"><font size=2>Figure 3-1. Accessing HDFS over HTTP directly and via a bank of HDFS proxies</font></p>


在第一种情况下, namenode 和 datanodes 中的嵌入式 web 服务器充当 WebHDFS 端点.(WebHDFS 默认是使能的, 因为 dfs.webhdfs.enabled 默认值为 true). 文件元数据操作由 namenode 处理, 而文件读取 (还有写入) 操作首先发送到 namenode, 它将 HTTP 重定向报文发送给客户端, 指示应该从/到哪个 datanode 获取/写入数据.

通过 HTTP 访问 HDFS 的第二种方式依赖于一个或多个独立的代理服务器.(这些代理是无状态的, 所以它们可以在标准负载均衡器后面运行.)所有到集群的流量都通过代理, 所以客户端永远不会直接访问 namenode 或 datanode, 这需要配套更严格的防火墙和带宽限制策略. 通常在位于不同的数据中心 Hadoop 集群之间使用代理进行传输, 或者从外部网络访问部署在云上的 Hadoop 集群.

HttpFS 代理公开了与 WebHDFS 相同的 HTTP(和 HTTPS)接口, 因此客户端可以使用 webhdfs(或 swebhdfs)URI 访问这两者. HttpFS 代理独立于 namenode 和 datanode 守护进程, 使用 httpfs.sh 脚本启动, 默认情况下监听 14000 端口号.

* **C**
* **NFS**
可以使用 Hadoop 的 NFSv3 网关在本地客户端的文件系统上挂载 HDFS. 支持在一个文件尾部添加数据, 但不支持随机修改文件, 因为 HDFS 只能写入文件的末尾.
* **FUSE(Filesystem in Userspace)**

### The Java Interface

#### Reading Data from a Hadoop URL

``` java
public class URLCat {

    static {
        URL.setURLStreamHandlerFactory(new FsUrlStreamHandlerFactory());
    }

    public static void main(String[] args) throws Exception {
        InputStream in = null;
        try {
            in = new URL(args[0]).openStream();
            IOUtils.copyBytes(in, System.out, 4096, false);
        } finally {
            IOUtils.closeStream(in);
        }
    }
}
```
<p align="center"><font size=2>Example 3-1. Displaying files from a Hadoop filesystem on standard output using a URLStreamHandler</font></p>

#### Reading Data Using the FileSystem API

``` java
public class FileSystemCat {

    public static void main(String[] args) throws Exception {
        String uri = args[0];
        Configuration conf = new Configuration();
        FileSystem fs = FileSystem.get(URI.create(uri), conf);
        InputStream in = null;

        try {
            in = fs.open(new Path(uri));
            IOUtils.copyBytes(in, System.out, 4096, false);
        } finally {
            IOUtils.closeStream(in);
        }
    }
}
```
<p align="center"><font size=2>Example 3-2. Displaying files from a Hadoop filesystem on standard output by using the FileSystem directly</font></p>

#### Writing Data

``` java
public class FileSystemDoubleCat {

    public static void main(String[] args) throws Exception {
        String uri = args[0];
        Configuration conf = new Configuration();
        FileSystem fs = FileSystem.get(URI.create(uri), conf);
        InputStream in = null;

        try {
            in = fs.open(new Path(uri));
            IOUtils.copyBytes(in, System.out, 4096, false);
            in.seek(0); // go back to the start of the file
            IOUtils.copyBytes(in, System.out, 4096, false);
        } finally {
            IOUtils.closeStream(in);
        }
    }
}
```
<p align="center"><font size=2>Example 3-3. Displaying files from a Hadoop filesystem on standard output twice, by using seek()</font></p>

#### Directories
#### Querying the Filesystem
#### Deleting Data


### Data Flow
#### Anatomy of a File Read

<p align="center"><font size=2>Figure 3-2. A client reading data from HDFS</font></p>

这种设计的一个重要方面是客户直接从 datanodes 检索数据, 并且由 namenode 引导到最优的 datanode. 这个设计允许 HDFS 支持大量的并发客户端, 因为数据流量分散到集群中的所有数据节点上. 同时， namenode 只提供数据块位置(它们存储在内存中, 使得查询操作非常高效), 如果不这样做, 随着客户数量的增长, 大量的数据读取很快就会让系统遇到瓶颈.

* **Network Topology and Hadoop**
两个节点间距离的大小用带宽来衡量.

#### Anatomy of a File Write

<p align="center"><font size=2>Figure 3-4. A client writing data to HDFS</font></p>

#### Coherency Model

### Parallel Copying with distcp
#### Keeping an HDFS Cluster Balanced




## CHAPTER 4 YARN
