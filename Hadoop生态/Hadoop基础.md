# Hadoop基础

### 一. Hadoop概述

#### 1.1 为什么要用Hadoop

我们生活在数据大爆炸的年代，2020年达到84.5ZB，比预计的61ZB整整多出20+ZB，在疫情时期，数据达到空前爆发式增长，单位换算后在840亿TB以上，也就是全球每人一块1TB硬盘都存储不下。

> 注：1 Terabyte (TB) = 1024 GB. 1 Petabyte (PB) = 1024 TB. 1 Exabyte (EB) = 1024 PB. 1 Zettabyte (ZB) = 1024 EB

一些数据集的大小更是远远超过1TB，也就是数据的存储是一个要解决的问题。同时，硬盘技术也面临一个技术瓶颈，也就是硬盘的传输速度（读数据的速度）。如下表格：

| 年份 | 硬盘大小    | 传输速率 | 所需时间 |
| ---- | ----------- | -------- | -------- |
| 1990 | 1370MB      | 4.4MB/s  | 5分钟    |
| 2010 | 1TB（主流） | 100MB/s  | 3小时    |

可以看到，容量提升了将近千倍，而传输速度才提升了20倍，读完一个硬盘的所需时间相对更长了，读都要这么久更别说写了。

对于如何提高读数据的效率，可以将一个数据集存储到多个硬盘里，然后并行读取。如1T数据平均100份存储到100个1TB硬盘上，同时读取，则读取完成整个数据集花不了两分钟，至于剩下的99%容量可以用来存储其它的数据集。

但，如果我们要对多个硬盘进行读/写操作时，又有了新的问题需要解决；

> 硬件故障问题。一旦硬件数量过多，如上述的100个1TB硬盘，那么故障的总数相对1个1TB硬盘就会偏高，为了避免数据丢失，常见的做法就是复制（replication），一旦发生故障则使用另外的副本；
>
> 读取数据的正确性问题。如何把100份数据合并成一个数据集且保证正确性，也是一个非常大的挑战；

针对上述问题，Hadoop为我们提供了一个可靠的且可扩展的存储和分析平台，此外，Hadoop还是开源的。具体可参考：https://hadoop.apache.org/docs/r1.0.4/cn/hdfs_design.html



#### 1.2 Hadoop的简要介绍

Hadoop是Apache基金会旗下一个开源的分布式存储和分析计算平台，使用Java语言开发，具有很好的跨平台性，开源，用户无需了解分布式底层细节即可开发分布式程序，充分使用集群的高速计算和存储。



#### 1.3 谷歌的三篇论文

~~~
- 2003年发表的《GFS》
  基于硬盘不够大、数据存储单份的安全隐患问题，提出了分布式文件系统用于存储的理论思想。
  * 解决了如何存储大数据集的问题
  
- 2004年发表《MapReduce》
  基于分布式文件系统的计算分析编程框架模型。移动计算而非移动数据，分而治之。
  * 解决了如何快速分析大数据集的问题

- 2006年发表的《BigTable》
  针对传统关系型数据库不适合存储非结构化数据的缺点，提出了另一种适合存储大数据集的解决方案。
~~~



#### 1.4 Hadoop的发展历史

~~~
- 起源于Apache Nutch项目（一个网页爬取工具和搜索引擎系统，后来遇到大数据量的存储问题）
- 2003年，谷歌发表《GFS》给了Apache Nutch项目的开发者灵感。
- 2004年，Nutch的开发者开始着手NDFS（Nutch的分布式文件系统）。
- 2005年，谷歌发表一篇介绍MapReduce系统的论文。
- 2006年，开发人员将NDFS和MapReduce移出Nutch项目形成一个子项目，命名Hadoop。
- 2008年，Hadoop已成为Apache的顶级项目。
- 2008年4月，Hadoop打破世界纪录，成为最快排序1TB数据的系统，排序时间为209秒。
- 2009年，Hadoop把1TB数据的排序时间缩短到62秒。
~~~



#### 1.5 Hadoop的组成部分

~~~
Hadoop2.0以后的四个模块：
  - Hadoop Common: Hadoop模块的通用组件
  - Hadoop distributed File System: 分布式文件系统
  - Hadoop YARN: 作业调度和资源管理框架
  - Hadoop MapReduce: 基于YARN的大型数据集并行计算处理框架
  
Hadoop3.0新扩展的两个模块：
  - Hadoop Ozone: Hadoop的对象存储机制
  - Hadoop Submarine: Hadoop的机器学习引擎
~~~



#### 1.6 Hadoop的生态系统

2.0

![1657767090189](assets/1657767090189.png)

> 在3.0版本中，除了性能方面的提升。在生态，分布式计算框架除了Spark外，Flink也尤为亮眼，且支持更多NameNode，具体参考：https://hadoop.apache.org/docs/r3.0.0/



### 二. Hadoop集群安装

#### 2.1 集群规划

| 集群规划       | 规划                                                         |
| -------------- | ------------------------------------------------------------ |
| 操作系统       | Windows、Mac                                                 |
| 虚拟软件       | VMware（windows）、Parallel Desktop（Mac）<br />或者直接去云厂商开3台centos7.7 |
| 虚拟机         | 主机名：ben01<br />主机名：ben02<br />主机名：ben03          |
| 配置           | 主机01：2核,4G,30G<br />主机02：1核,2G,30G<br />主机03：1核,2G,30G |
| 软件包上传路径 | /root/softwares                                              |
| 软件包安装路径 | /usr/local                                                   |
| JDK            | Jdk-8u321-linux-x64.tar.gz                                   |
| Hadoop         | hadoop-3.3.1.tar.gz                                          |
| 用户           | root                                                         |



#### 2.2 安装JDK

> 三台机器都需要做

2.2.1 检查是否有内置JDK，如有则卸载

~~~shell
[root@ben01 ~]# rpm -qa | grep jdk  # 查看是否有
[root@ben01 ~]# rpm -e xxxxxxxx --nodeps  # 有则卸载
~~~

2.2.2 上传jdk1.8到/root/softwares下

2.2.3 解压jdk到/usr/local/下

~~~shell
[root@ben01 softwares]# tar -zxvf jdk-8u321-linux-x64.tar.gz -C /usr/local
~~~

2.2.4 更名jdk

~~~shell
[root@ben01 softwares]# cd /usr/local
[root@ben01 local]# mv jdk1.8.0_321/  jdk
~~~

2.2.5 配置jdk的环境变量：/etc/profile

~~~shell
[root@ben01 local]# vi /etc/profile  # 在最下面添加如下内容

#jdk environment
export JAVA_HOME=/usr/local/jdk
export PATH=$JAVA_HOME/bin:$JAVA_HOME/jre/bin:$PATH
~~~

2.2.6 使当前窗口生效

~~~shell
[root@ben01 local]# source /etc/profile
~~~

2.2.7 验证jdk换季

~~~shell
[root@ben01 local]# java -version
[root@ben01 local]# java
~~~

![1657779529246](assets/1657779529246.png)

