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
### Relational Database Management Systems
* MapReduce is a good fit for problems that need to analyze the whole dataset
in a batch fashion, particularly for ad hoc analysis. An RDBMS is good for point queries
or updates, where the dataset has been indexed to deliver low-latency retrieval and
update times of a relatively small amount of data. 
* MapReduce suits applications where
the data is written once and read many times, whereas a relational database is good for
datasets that are continually updated. 


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
<br>
## CHAPTER 2 MapReduce
MapReduce 是关于数据处理的 <font color=#fd0209 size=5 >编程模型</font>.



