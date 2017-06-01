# memo

##算法
- [**算法**图示](http://www.cs.usfca.edu/~galles/visualization/Algorithms.html)
- [算法学习笔记](https://brandeath.gitbooks.io/al/content/index.html)

---



##运维
- [集中式日志系统 ELK 协议栈详解](https://www.ibm.com/developerworks/cn/opensource/os-cn-elk/  "Title")

---


##大数据
- [数据处理平台架构中的SMACK组合：`Spark`、Mesos、Akka、Cassandra以及Kafka](http://blog.dataman-inc.com/untitled-23/  "Title")

---


##机器学习
- [【机器学习笔记1】Logistic回归总结](http://blog.csdn.net/dongtingzhizi/article/details/15962797)
- [【机器学习笔记2】Linear Regression总结](http://blog.csdn.net/dongtingzhizi/article/details/16884215)
- [【机器学习笔记3】Stanford公开课Exercise 2——Linear Regression](http://blog.csdn.net/dongtingzhizi/article/details/16949755)
- [【机器学习笔记4】Stanford公开课Exercise 3——Multivariate Linear Regression](http://blog.csdn.net/dongtingzhizi/article/details/16979103)
- [DeepLearningBook](https://github.com/HFTrader/DeepLearningBook)

---


##论文
- [arxiv 论文下载网站](https://arxiv.org/)
- [Concurrent Programming for Scalable Web Architectures](https://github.com/tpn/pdfs/blob/master/Concurrent%20Programming%20for%20Scalable%20Web%20Architectures%20-%20Benjamin%20Erb%20-%20Thesis%20(April%202012)%20(vts_8082_11772).pdf)

---

##C++
- [Foundations of C++](http://www.stroustrup.com/ETAPS-corrected-draft.pdf)
- [cppreference.com](http://en.cppreference.com)

---

##JAVA
- [servlet-3-1-specification](https://waylau.gitbooks.io/servlet-3-1-specification/)
- [spring-framework-4-reference](https://www.gitbook.com/book/waylau/spring-framework-4-reference/details)
- [Essential Netty in Action](https://www.gitbook.com/book/waylau/essential-netty-in-action/details)
- [OSGI specifications](https://www.osgi.org/developer/specifications/)
- [OSGI企业应用开发](http://blog.csdn.net/masusan/article/details/69951536)
- [OSGi的缘起缘灭](http://lanlingzi.cn/post/technical/2015/0422_remove_osgi/)

---

##技术Blog
- [martinfowler](https://martinfowler.com/)
- [张逸](http://zhangyi.farbox.com/)

---

##trading ststem
- [trading ststem -Urban Jaekle](http://jamescarl.github.io/CEN4020/assets/pdf/013.pdf)

---


```javascript
var ihubo
```

* [yosuke-furukawa](https://github.com/yosuke-furukawa) -
**Yosuke Furukawa** &lt;yosuke.furukawa@gmail.com&gt;

Use the `printf()` function.

- 1
- 2
- 3
- 4

## 其他
- [布局天下](http://bbs.feng.com/read-htm-tid-8302536.html)


## 设计模式
- [Domain-driven design](http://dddcommunity.org/)
- [图说设计模式](http://design-patterns.readthedocs.io)


## java
- 在Java中，构造方法无法被继承，无法设置默认值
- Jboss同时是Web容器和EJB容器。Tomcat只是Web容器
- HashMap几乎可以等价于Hashtable，除了HashMap是非synchronized的，并可以接受null(HashMap可以接受为null的键值(key)和值(value)，而Hashtable则不行)
- HashMap是非synchronized，而Hashtable是synchronized，这意味着Hashtable是线程安全的，多个线程可以共享一个Hashtable；而如果没有正确的同步的话，多个线程是不能共享HashMap的。


## docker
- docker 要求必须部署在64位机器上
- 容器内的数据是临时性的，它会随着容器生命周期的结束而消失
- 默认的 Docker volume （driver = ‘loclal’）不管是哪种形式，本质上都是将容器所在的主机上的一个目录 mount 到容器内的一个目录，因此，它不具备可移植性。