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

&emsp;&emsp;首先做一些术语解释. MapReduce job 是客户希望执行的某项工作: 它由输入数据, MapReduce 程序和配置信息组成. Hadoop 通过将 job 分成多个任务来执行, 这些任务分为两种类型: map 任务和 reduce 任务. 这些任务使用 YARN 进行调度, 在集群中的节点上运行. 如果某个任务失败, 它将被自动调度到其他节点上再次运行。

&emsp;&emsp;Hadoop 将输入分成固定大小的片段, 然后提交给 MapReduce 任务. 我们将这些片段称之为 input splits 或是 splits. Hadoop 为每个片段创建一个 map 任务, 该任务基于片段中的每条记录运行用户定义的 map 函数.

&emsp;&emsp;对数据进行分片意味着相对于处理整个输入数据集, 处理每个分片所需时间会比较少. 假设我们并行的处理这些分片, 那么在分片越小的情况下, 更容易达成服务器之间的负载均衡, 因为在任务运行过程中, 机器越快, 处理的分片将越多. 即使这些机器没有差异, 考虑到进程可能失败或存在其他正在运行的作业, 负载均衡也是可取的, 而且分割的粒度越细, 负载平衡的质量越好.

&emsp;&emsp;另一方面, 如果分片太小, 则管理分片和创建 map 任务的开销开始主宰整个 job 的执行时间. 对于大多数 job 来说, 分片大小设置为 HDFS 块的大小将是比较合适的, 默认情况下为 128 MB, 当然, HDFS 块的大小可以基于集群(所有新创建的文件)进行更改，或者在文件创建时进行指定.

Hadoop 尽量在输入数据所在的 HDFS 节点上运行 map 任务, 这样可以不占用宝贵的集群带宽. 该特性被称为数据本地优化(data locality optimization). 然而有些时候, 所有存储任务所需分片及其副本的 HDFS 块所在的节点都在满负载运行 map 任务, 此时作业调度程序将尝试在同一个机架的其他节点上运行任务. 如果同一机架上没有空闲的节点, 尽管这种情况非常少见甚至是不可能的, 此时会使用机架外节点，这将导致机架间的网络传输. 图 2-2 描述了这三种情况.

It should now be clear why the optimal split size is the same as the block size: it is the
largest size of input that can be guaranteed to be stored on a single node. If the split
spanned two blocks, it would be unlikely that any HDFS node stored both blocks, so
some of the split would have to be transferred across the network to the node running
the map task, which is clearly less efficient than running the whole map task using local
data.

现在应该清楚为什么最佳分割大小与块大小相同：它是
最大的输入大小可以保证存储在单个节点上。 如果分裂
跨越两个块，所以任何HDFS节点都不太可能存储这两个块
一些拆分将不得不通过网络传输到正在运行的节点
地图任务显然比使用本地运行整个地图任务效率低
数据。

Map tasks write their output to the local disk, not to HDFS. Why is this? Map output is
intermediate output: it’s processed by reduce tasks to produce the final output, and once
the job is complete, the map output can be thrown away. So, storing it in HDFS with
replication would be overkill. If the node running the map task fails before the map
output has been consumed by the reduce task, then Hadoop will automatically rerun
the map task on another node to re-create the map output.

Map任务将其输出写入本地磁盘，而不是HDFS。 为什么是这样？ 地图输出是
中间输出：由减少任务处理以产生最终输出，并且一次
作业完成后，地图输出可以被丢弃。 因此，将它与HDFS一起存储
复制会过度。 如果运行地图任务的节点在地图之前失败
输出已被reduce任务使用，那么Hadoop将自动重新运行
另一个节点上的地图任务重新创建地图输出。



<p align="center"><font size=2>Figure 2-2. Data-local (a), rack-local (b), and off-rack (c) map tasks</font></p>


Reduce tasks don’t have the advantage of data locality; the input to a single reduce task
is normally the output from all mappers. In the present example, we have a single reduce
task that is fed by all of the map tasks. Therefore, the sorted map outputs have to be
transferred across the network to the node where the reduce task is running, where they
are merged and then passed to the user-defined reduce function. The output of the
reduce is normally stored in HDFS for reliability. As explained in Chapter 3, for each
HDFS block of the reduce output, the first replica is stored on the local node, with other
replicas being stored on off-rack nodes for reliability. Thus, writing the reduce output
does consume network bandwidth, but only as much as a normal HDFS write pipeline
consumes.

减少任务不具有数据局部性的优势; 输入到单个reduce任务
通常是所有映射器的输出。 在目前的例子中，我们有一个单一的减少
由所有地图任务提供的任务。 因此，排序后的地图输出必须是
通过网络传输到reduce任务正在运行的节点，它们在哪里
被合并，然后传递给用户定义的reduce函数。 的输出
减少通常存储在HDFS中以保证可靠性。 正如第3章所解释的那样
HDFS块的减少输出，第一个副本存储在本地节点上，与其他节点一起存储
复制品被存储在机架外节点上以保证可靠性。 因此，编写减少输出
确实消耗了网络带宽，但只是和普通的HDFS写入管道一样多
消耗。

The whole data flow with a single reduce task is illustrated in Figure 2-3. The dotted
boxes indicate nodes, the dotted arrows show data transfers on a node, and the solid
arrows show data transfers between nodes.

图2-3说明了单个reduce任务的整个数据流。 点缀
框表示节点，虚线箭头表示节点上的数据传输，以及实体
箭头显示节点之间的数据传输。


<p align="center"><font size=2>Figure 2-3. MapReduce data flow with a single reduce task</font></p>

The number of reduce tasks is not governed by the size of the input, but instead is
specified independently. In “The Default MapReduce Job” on page 214, you will see how
to choose the number of reduce tasks for a given job.
When there are multiple reducers, the map tasks partition their output, each creating
one partition for each reduce task. There can be many keys (and their associated values)
in each partition, but the records for any given key are all in a single partition. The
partitioning can be controlled by a user-defined partitioning function, but normally the
default partitioner—which buckets keys using a hash function—works very well.
The data flow for the general case of multiple reduce tasks is illustrated in Figure 2-4.
This diagram makes it clear why the data flow between map and reduce tasks is collo‐
quially known as “the shuffle,” as each reduce task is fed by many map tasks. The shuffle
is more complicated than this diagram suggests, and tuning it can have a big impact on
job execution time, as you will see in “Shuffle and Sort” on page 197.

减少任务的数量不受输入大小的控制，而是由输入大小决定
独立指定。在第214页的“默认MapReduce作业”中，您将看到如何
为特定作业选择减少任务的数量。
当有多个缩减器时，映射任务将对其输出进行分区，每个创建
每个减少任务一个分区。可以有许多键（和它们的相关值）
在每个分区中，但任何给定密钥的记录都在单个分区中。该
分区可以由用户定义的分区功能来控制，但通常情况下，
默认分区程序 - 使用散列函数对密钥进行存储 - 效果很好。
图2-4说明了多个减少任务的一般情况的数据流。
该图清楚地说明了为什么map和reduce任务之间的数据流是collo-
一般称为“洗牌”，因为每个减少任务都由许多地图任务提供。洗牌
比这个图表更复杂，调整它可以产生很大的影响
作业执行时间，您将在第197页上的“随机排序”中看到。

<p align="center"><font size=2>Figure 2-4. MapReduce data flow with multiple reduce tasks</font></p>

Finally, it’s also possible to have zero reduce tasks. This can be appropriate when you
don’t need the shuffle because the processing can be carried out entirely in parallel (a
few examples are discussed in “NLineInputFormat” on page 234). In this case, the only
off-node data transfer is when the map tasks write to HDFS (see Figure 2-5).

最后，还可以有零减少任务。 这可以适合你的时候
不需要洗牌，因为处理可以完全并行进行（a
在第234页的“NLineInputFormat”中讨论了一些例子）。 在这种情况下，唯一的
离线节点数据传输是在地图任务写入HDFS时（参见图2-5）。

#### Combiner Functions

Many MapReduce jobs are limited by the bandwidth available on the cluster, so it pays
to minimize the data transferred between map and reduce tasks. Hadoop allows the user
to specify a combiner function to be run on the map output, and the combiner function’s
output forms the input to the reduce function. Because the combiner function is an
optimization, Hadoop does not provide a guarantee of how many times it will call it for
a particular map output record, if at all. In other words, calling the combiner function
zero, one, or many times should produce the same output from the reducer.


许多MapReduce作业受到群集上可用带宽的限制，因此付费
以最小化地图和减少任务之间传输的数据。 Hadoop允许用户
指定要在地图输出上运行的组合器功能，以及组合器功能
输出形成了reduce函数的输入。 因为组合函数是一个
优化时，Hadoop不会保证它将调用它多少次
一个特定的地图输出记录，如果有的话。 换句话说，调用组合器功能
零，一次或多次应该从减速器产生相同的输出。

<p align="center"><font size=2>Figure 2-5. MapReduce data flow with no reduce tasks</font></p>







