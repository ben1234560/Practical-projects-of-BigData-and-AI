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
| 虚拟机         | 主机名：ben01，IP地址192.168.10.101<br />主机名：ben02，IP地址192.168.10.102<br />主机名：ben03，IP地址192.168.10.103 |
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
[root@ben01 ~]# cd softwares/
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



#### 2.3 完全分布式环境需求及安装

~~~~
1. 三台机器的防火墙必须是关闭的
2. 确保三台机器的网络配置通畅
3. 确保/etc/hosts文件配置了IP和hosts的映射关系
4. 确保配置了三台机器的免密登录认证
5. 确保所有机器的时间同步
6. 三台机器JDK和Hadoop的环境变量配置
~~~~

2.3.1 关闭防火墙

~~~shell
[root@ben01 ~]# systemctl stop firewalld
[root@ben01 ~]# systemctl disable firewalld
[root@ben01 ~]# systemctl stop NetworkManager
[root@ben01 ~]# systemctl disable NetworkManager

# 最好也把selinux关闭，将SELINUXTYPE设置为disabled
[root@ben01 ~]# vi /etc/selinux/config
SELINUXTYPE=disabled
~~~

2.3.2 静态IP和主机名配置

> 作者直接开的云厂商的服务

~~~shell
# 1.配置静态IP（确保NAT模式）
vi /etc/sysconfig/network-scripts/ifcfg-ens33

BOOTPROTO=static  # dhcp改为static
............
ONBOOT=yes        # no改为yes
IPADDR=192.168.10.101 # 添加IPADDR属性和ip地址
PREFIX=24  # 添加NETMASK=255.255.255.0 或者 PREFIX=24 
GATEWAY=192.168.10.2  # 添加GATEWAY网关
DNS1=114.114.114.114  # 天假DNS1和备份DNS
DNS2=8.8.8.8

# 2.重启网络服务
systemctl restart network
service network restart

# 3.修改主机名
hostnamectl set-hostname ben01  # 可以设置成自己的名字
hostname  # 即可查看是否设置完成，需要重连才能显示
~~~

2.3.3  配置/etc/hosts文件

> 作者是用的云厂商机器，IP有所不同

~~~shell
[root@ben01 ~]# vi /etc/hosts

127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

# 添加本机的静态IP和本机主机名之间的映射关系
10.206.0.10 ben01  
10.206.16.4 ben02
10.206.16.8 ben03
~~~

![1657781271931](assets/1657781271931.png)

2.3.4 免密登录认证

~~~shell
# 1.使用rsa加密技术，生成公钥和密钥，一路回车即可
[root@ben01 ~]# cd ~
[root@ben01 ~]# ssh-keygen -t rsa

# 2.进入~/.ssh目录下，使用ssh-copy-id命令，需要输入密码
[root@ben01 ~]# cd ~/.ssh
[root@ben01 .ssh]# ssh-copy-id  root@ben01

# 3.进行验证
[root@ben01 .ssh]# ssh ben01
# 下面第一次执行时输入yes后，不提示输入密码就对了
[root@ben01 ~]# ssh localhost
[root@ben01 ~]# ssh 0.0.0.0
~~~

2.3.5 时间同步

> 云厂买的则不需要，已经自动同步了

~~~shell
# 1.选择集群中的某一台机器作为时间服务器，例如ben01
# 2.保证这台服务器安装了ntp.x86_64
# 3.保证ntpd 服务运行
[root@ben01 ~]# sudo service ntpd start
[root@ben01 ~]# chkcofig ntpd on

# 4.配置相应文件
[root@ben01 ~]# vi /etc/ntp.conf
# Hosts on local network are less restricted.
# restrict 192.168.1.0 mask 255.255.255.0 nomodify notrap 
# 添加集群中的网络段位
restrict 192.168.10.0 mask 255.255.255.0 nomodify notrap

# Use public servers from the pool.ntp.org project.
# Please consider joining the pool (http://www.pool.ntp.org/join.html).
# server 0.centos.pool.ntp.org iburst 注释掉
# server 1.centos.pool.ntp.org iburst 注释掉
# server 2.centos.pool.ntp.org iburst 注释掉
# server 3.centos.pool.ntp.org iburst 注释掉
servcer 127.127.1.0    -master作为服务器
# 5.其它机器要保证安装ntpdate.x86_64
# 6.其它机器要使用root定义定时器
*/1 * * * * /usr/sbin/ntpdate -u ben01
~~~

2.3.6 Hadoop安装与环境变量配置

~~~shell
# 1.上传和解压软件包
[root@ben01 softwares]# tar zxvf hadoop-3.3.1.tar.gz -C /usr/local/

# 2.进入local里，给软件改名
[root@ben01 softwares]# cd /usr/local/
[root@ben01 local]# mv hadoop-3.3.1/ hadoop

# 3.配置环境变量，再最下面追加
[root@ben01 local]# vi /etc/profile

......
#hadoop environment
export HADOOP_HOME=/usr/local/hadoop
export PATH=$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH

# 4.刷新环境并验证
[root@ben01 local]# source /etc/profile
[root@ben01 local]# hadoop version
~~~

![1657865386616](assets/1657865386616.png)

> 注意，是三台机器都完成了如上的操作



#### 2.4 Hadoop的配置文件

2.4.1 概述

我们需要通过配置若干配置文件，来实现Hadoop集群的配置信息。需要配置的文件有：

~~~
hadoop-env.sh
yarn-env.sh
core-site.xml
hdfs-site.xml
mapred-site.xml
yarn-site.xml

在Hadoop安装完成后，会在$HADOOP_HOME/share路径下，有若干个*-default.xml文件，这些文件中记录了默认的配置信息，同时，在代码中，我们也可以配置Hadoop的配置信息。
这些位置配置的Hadoop，优先级为：代码设置 > *-site.xml > *-default.xml
~~~

集群规划：

| Node  | Applications                                                 |
| ----- | ------------------------------------------------------------ |
| ben01 | NameNode<br />DataNode<br />ResourceManager<br />NodeManager |
| ben02 | SecondaryNameNode<br />DataNode<br />NodeManager             |
| ben03 | DataNode<br />NodeManager                                    |

2.4.2 core-site.xml

~~~xml
[root@ben01 ~]# cd $HADOOP_HOME/etc/hadoop/
[root@ben01 hadoop]# vi core-site.xml
<configuration>
	<!-- hdfs地址名称：schame,ip,port-->
 	<!-- 在Hadoop1.x版本中，默认使用9000。Hadoop2.x默认使用端口为8020 -->
 	<property>
 		<name>fs.defaultFS</name>
		<value>hdfs://ben01:8020</value>
 	</property>
 	<!-- hdfs的基础路径，被其它属性依赖的一个基础路径 -->
 	<property>
 		<name>hadoop.tmp.dir</name>
 		<value>/usr/local/hadoop/tmp</value>
 	</property>
</configuration>
~~~

2.4.3 hdfs-site.xml

~~~xml
[root@ben01 hadoop]# vi hdfs-site.xml
<configuration>
 	<!-- namenode守护进程管理的元数据文件fsimag存储的位置-->
 	<property>
 		<name>dfs.namenode.name.dir</name>
 		<value>file://${hadoop.tmp.dir}/dfs/name</value>
 	</property>
	<--确定DFS数据节点应该将其块存储在本地文件系统的何处--!> 
 	<property>
 		<name>dfs.datanode.data.dir</name>
 		<value>file://${hadoop.tmp.dir}/dfs/data</value>
 	</property>
	<--块的副本数--!> 
 	<property>
 		<name>dfs.replication</name>
 		<value>3</value>
 	</property>
 	<!--块的大小（128M），下面的单位是字节-->
 	<property>
 		<name>dfs.blocksize</name>
		<value>134217728</value>
 	</property>
 	<!-- secondarynamenode守护进程的http地址：主机名和端口号。参考守护进程布局-->
 	<property>
 		<name>dfs.namenode.secondary.http-address</name>
 		<value>ben02:50090</value>
 	</property>
 	<!-- namenode守护进程的http地址：主机名和端口号。参考守护进程布局-->
 	<property>
 		<name>dfs.namenode.http-address</name>
 		<value>ben01:50070</value>
 	</property> 
</configuration>
~~~

2.4.4 mapred-site.xml

~~~xml
[root@ben01 hadoop]# vi mapred-site.xml
<configuration>
	<!--指定mapreduce使用yarn资源管理器-->
 	<property>
		 <name>mapreduce.framework.name</name>
		 <value>yarn</value>
 	</property>
 	<!--配置作业历史服务器的地址-->
 	<property>
 		<name>mapreduce.jobhistory.address</name>
 		<value>qianfeng01:10020</value>
 	</property>
 	<!-- 配置作业历史服务器的http地址 -->
	<property>
		<name>mapreduce.jobhistory.webapp.address</name>
 		<value>ben01:19888</value>
 	</property>
</configuration>
~~~

