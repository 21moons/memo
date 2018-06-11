# 1 为何选择 Redis

Redis 是`单线程`应用程序, 占用较少的内存. 它通过在数据中心和云供应商的多核处理器上运行多个实例达到持久性和可伸缩性. 多台 Redis 组成的主从复制及正式发布的Redis 集群, 让运行多个 Redis 实例在内存和 CPU 需求方面的运营成本相对低廉. 这就允许在兼顾伸缩性的同时, 增强大型应用程序的持久性.

在基于 SQL 的关系型数据库中, 开发者和数据库管理员通过将数据规范化为列, 行和表, 并通过外键关系建立关联的方式创建数据库模式.

![关系数据库和NoSQL](https://raw.githubusercontent.com/21moons/memo/master/res/img/redis/Figure_1.1_关系数据库和NoSQL.jpg)




