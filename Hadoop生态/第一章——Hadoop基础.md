# 第一章——Hadoop基础

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

2.x版本

![1657767090189](assets/1657767090189.png)

> 在3.x版本中，除了性能方面的提升。在生态，分布式计算框架除了Spark外，Flink也尤为亮眼，且支持更多NameNode，具体参考：https://hadoop.apache.org/docs/r3.0.0/，**本次我们部署的也是22年最新的3.3.1版本**



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

> 上述软件包可通过百度网盘下载：
>
> 链接：https://pan.baidu.com/s/1UZABuNRtoPzjrrc73r26Yw?pwd=d0ni 
> 提取码：d0ni 



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
# 1.使用rsa加密技术，生成公钥和密钥，一路回车即可，三台机器都需要做
[root@ben01 ~]# cd ~
[root@ben01 ~]# ssh-keygen -t rsa

# 2.01免密登录自己和02和03，进入~/.ssh目录下，使用ssh-copy-id命令，需要输入密码
[root@ben01 ~]# cd ~/.ssh
[root@ben01 .ssh]# ssh-copy-id  ben01  # 输入密码
[root@ben01 .ssh]# ssh-copy-id  ben02  # 输入密码
[root@ben01 .ssh]# ssh-copy-id  ben03  # 输入密码

# 3.进行验证，下面第一次执行时输入yes后，不提示输入密码就对了
[root@ben01 .ssh]# ssh ben02
[root@ben01 ~]# ssh localhost
[root@ben01 ~]# ssh 0.0.0.0

# 4.同样的，ben02和ben03也需要做对其它两台的免密
~~~

> 如下图
>
> ![1658122127131](assets/1658122127131.png)

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
[root@ben01 hadoop]# vim core-site.xml
<configuration>
        <property>
                <name>fs.defaultFS</name>
                <value>hdfs://ben01:8020</value>
        </property>
        <property>
                <name>hadoop.tmp.dir</name>
                <value>/usr/local/hadoop/tmp</value>
        </property>
</configuration>
~~~

2.4.3 hdfs-site.xml

~~~xml
[root@ben01 hadoop]# vim hdfs-site.xml
<configuration>
    <!-- namenode守护进程管理的元数据文件fsimag存储的位置-->
    <property>
        <name>dfs.namenode.name.dir</name>
        <value>file://${hadoop.tmp.dir}/dfs/name</value>
    </property>
    <!--确定DFS数据节点应该将其块存储在本地文件系统的何处-->
    <property>
        <name>dfs.datanode.data.dir</name>
        <value>file://${hadoop.tmp.dir}/dfs/data</value>
    </property>
    <!--块的副本数-->
    <property>
        <name>dfs.replication</name>
        <value>3</value>
        </property>
        <!--块的大小（128M），下面的单位是字节-->
        <property>
                <name>dfs.blocksize</name>
                <value>134217728</value>
        </property>
        <!-- secondarynamenode守护进程的http地址：主机名和端口号。参考守护进程
布局-->
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
[root@ben01 hadoop]# vim mapred-site.xml
<configuration>
        <!--用于执行MapReduce作业运行时的框架-->
        <property>
                 <name>mapreduce.framework.name</name>
                 <value>yarn</value>
        </property>
        <!--历史任务的内部通讯地址-->
        <property>
                <name>mapreduce.jobhistory.address</name>
                <value>ben01:10020</value>
        </property>
        <!--历史任务的外部监听页面-->
        <property>
                <name>mapreduce.jobhistory.webapp.address</name>
                <value>ben01:19888</value>
        </property>
        <!-- 下面的需要配置,否则报错:找不到AppMaster -->
        <property>
                <name>yarn.app.mapreduce.am.env</name>
                <value>HADOOP_MAPRED_HOME=/usr/local/hadoop</value>
        </property>
        <property>
                <name>mapreduce.map.env</name>
                <value>HADOOP_MAPRED_HOME=/usr/local/hadoop</value>
        </property>
        <property>
                <name>mapreduce.reduce.env</name>
                <value>HADOOP_MAPRED_HOME=/usr/local/hadoop</value>
        </property>
</configuration>
~~~

2.4.5 yarn-site.xml

~~~xml
[root@ben01 hadoop]# vim yarn-site.xml
<configuration>

<!-- Site specific YARN configuration properties -->
        <!-- 指定yarn的shuffle技术 -->
        <property>
                <name>yarn.nodemanager.aux-services</name>
                <value>mapreduce_shuffle</value>
        </property>
        <!-- 指定resourcemanager的主机名 -->
        <property>
                <name>yarn.resourcemanager.hostname</name>
                <value>ben01</value>
        </property>
        <!--下面的可选-->
        <!--指定shuffle对应的类-->
        <property>
                <name>yarn.nodemanager.aux-services.mapreduce_shuffle.class</name>
                <value>org.apache.hadoop.mapred.ShuffleHandler</value>
        </property>

        <!--配置resourcemanager的内部通讯地址 -->
        <property>
                <name>yarn.resourcemanager.address</name>
                <value>ben01:8032</value>
        </property>
        <!--配置resourcemanager的scheduler内部通讯地址 -->
        <property>
                <name>yarn.resourcemanager.scheduler.address</name>
                <value>ben01:8030</value>
        </property>
    <!--配置resoucemanager的资源调度的内部通讯地址-->
        <property>
                <name>yarn.resourcemanager.resource-tracker.address</name>
                <value>ben01:8031</value>
        </property>
        <!--配置resourcemanager的管理员的内部通讯地址-->
        <property>
                <name>yarn.resourcemanager.admin.address</name>
                <value>ben01:8033</value>
        </property>
        <!--配置resourcemanager的web ui 的监控页面-->
        <property>
                <name>yarn.resourcemanager.webapp.address</name>
                <value>ben01:8088</value>
        </property>
</configuration>

~~~

2.4.6 hadoop-env.sh

~~~shell
[root@ben01 hadoop]# vi hadoop-env.sh
......
# The java implementation to use. By default, this environment
export JAVA_HOME=/usr/local/jdk
......
~~~

2.4.7 yarn-env.sh

~~~sh
[root@ben01 hadoop]# vi yarn-env.sh
.........
# some Java parameters
export JAVA_HOME=/usr/local/jdk
if [ "$JAVA_HOME" != "" ]; then
  #echo "run java in $JAVA_HOME"
  JAVA_HOME=$JAVA_HOME
fi
.........
~~~

2.4.8 workers

此文件用于指定datanode守护进程所在的机器点主机名

~~~sh
[root@ben01 hadoop]# cat workers
ben01
ben02
ben03
~~~

> **注意**：2.x版本，是添加一个slaves文件，内容与上面一致

2.4.9 分发到另外两台节点

~~~sh
[root@ben01 etc]# cd /usr/local/
[root@ben01 local]# scp -r hadoop ben02:$PWD
[root@ben01 local]# scp -r hadoop ben03:$PWD

# 同步profile到另两台
[root@ben01 local]# scp /etc/profile ben02:/etc
[root@ben01 local]# scp /etc/profile ben03:/etc
~~~



#### 2.5 格式化与启动

2.5.1 格式化集群

1）在ben01机器上运行命令

~~~sh
[root@ben01 local]# hdfs namenode -format
~~~

![1657871956423](assets/1657871956423.png)

> **用户切换，或权限不足的报错**
>
> ![1657876970998](assets/1657876970998.png)
>
> **原因**
>
> 用户切换，或权限不足
>
> **解决办法**
>
> 将上面的hadoop-env.sh文件，添加如下内容
>
> ~~~sh
> export HDFS_NAMENODE_USER=root
> export HDFS_DATANODE_USER=root
> export HDFS_SECONDARYNAMENODE_USER=root
> export YARN_RESOURCEMANAGER_USER=root
> export YARN_NODEMANAGER_USER=root
> ~~~

2）格式化的相关信息解读

~~~sh
--1.生成一个集群唯一标识符：clusterID
--2.生成一个块池唯一标识符：blockPoolID
--3.生成namenode进程管理内容（fsimage）的存储路径：
默认配置文件属性hadoop.tmp.dir指定的路径下生成dfs/name目录
--4.生成就想fsimage，记录分布式文件系统根路径的元数据
--5.其它信息都可以查看下，比如块的副本数，集群的fsOwner等
~~~

3）目录里的内容查看

![1657871956423](assets/1657873094885.png)

2.5.2 启动集群

1）启动脚本和关闭脚本介绍

~~~sh
1.启动脚本
  -- start-dfs.sh  	:用于启动hdfs集群的脚本
  -- start-yarn.sh	:用于启动yarn守护进程
  -- start-all.sh	:用于启动hdfs和yarn
2.关闭脚本
  -- stop-dfs.sh	:用于关闭hdfs集群的脚本
  -- stop-yarn.sh	:用于关闭yarn守护进程
  -- stop-all.sh	:用于关闭hdfs和yarn
3.单个守护进程脚本
  -- hadoop-daemons.sh	:用于单独启动或关闭的某一个守护进程的脚本
  -- hadoop-daemon.sh	:用于单独启动或关闭hdfs的某一个守护进程的脚本
  reg:
    hadoop-daemon.sh [start|stop] [namenode|datanode|secondarynamenode]
  
  -- yarn-daemons.sh	:用于单独启动或关闭hdfs的某一个守护进程的脚本
  -- yarn-daemon.sh		:用于单独启动或关闭hdfs的某一个守护进程的脚本
  reg:
    yarn-daemon.sh [start|stop] [resourcemanager|nodemanager]
~~~

2）启动HDFS

~~~sh
[root@ben01 hadoop]# start-dfs.sh
~~~

![1658131281677](assets/1658131281677.png)

> **主机间没做免密的报错**
>
> ![1658122834445](assets/1658122834445.png)

3）验证，jps查看进程，具体如下

~~~sh
[root@ben01 hadoop]# jps
29411 NameNode
8233 Jps
29609 DataNode

[root@ben02 hadoop]# jps
19457 Jps
18914 DataNode
19127 SecondaryNameNode

[root@ben03 hadoop]# jps
19252 Jps
17960 DataNode
~~~

4）启动yarn

~~~sh
[root@ben01 hadoop]# start-yarn.sh
~~~

> 成功图如下：
>
> ![1658131502394](assets/1658131502394.png)

jps查看如下

~~~sh
[root@ben01 hadoop]# jps
10352 Jps
29411 NameNode
29609 DataNode
9646 ResourceManager
9838 NodeManager

[root@ben02 hadoop]# jps
18914 DataNode
31219 NodeManager
19127 SecondaryNameNode
31561 Jps

[root@ben03 ~]# jps
30023 NodeManager
17960 DataNode
31913 Jps
~~~

5）WebUI查看

~~~sh
HDFS: http://1.13.169.142:50070
YARN: http://1.13.169.142:8088
~~~

> 如果是云厂买的注意开放对应端口，效果图如下
>
> ![1658132018864](assets/1658132018864.png)
>
> ![1658132110932](assets/1658132110932.png)

致此，恭喜你，hadoop集群已安装完成。

> 需要关机的话，先stop-all.sh停止服务，开机后再start-all.sh启动



### 三. HDFS的Shell命令

简介

~~~sh
HDFS是一个分布式文件系统，我们可以使用一些命令来操作这个分布式文件系统上的文件。
-- 访问HDFS的命令：
    hdfs dfs

-- 小技巧
	1.在命令行中输入hdfs，回车后，就会提示hdfs可以使用哪些明了，其中一个是dfs
	2.在命令行中输入hdfs dfs，回车后，就会提示dfs可以添加哪些常用shell命令

-- 注意事项
	分布式文件系统的路径在命令行中，要从/开始写，即绝对路径。
~~~

#### 3.1 创建目录

~~~sh
[-mkdir [-p] <path> ...] #分布式文件系统上创建目录 -p，多层级创建

调用格式：hdfs dfs -mkdir (-p) /目录
如：
[root@ben01 ~]# hdfs dfs -mkdir /data
[root@ben01 ~]# hdfs dfs -mkdir -p /data/a/b/c
~~~

> 如下图
>
> ![1658136325417](assets/1658136325417.png)
>
> 可以在命令行查看
>
> [root@ben01 ~]# hdfs dfs -ls /
>
> 也可以在UI页面查看
>
> ![1658136476325](assets/1658136476325.png)



#### 3.2 上传命令

~~~sh
[-put [-f] [-p] [-l] <localsrc> ... <dst>]  # 将本地文件系统的文件上传到分布式文件系统

调用格式: hdfs dfs -put /本地文件 /分布式文件系统路径
注意：直接写/是省略了文件系统的名称hdfs://ip:port
如：
# 先创建并往文件写内容
[root@ben01 ~]# echo "Hello, ben" > test.txt
[root@ben01 ~]# hdfs dfs -put test.txt /data/
# 查看上传后的内容
[root@ben01 ~]# hdfs dfs -cat /data/test.txt
~~~

> 效果图如下
>
> ![1658137105576](assets/1658137105576.png)
>
> 同样能在UI页面看到
>
> ![1658137154450](assets/1658137154450.png)



#### 3.3 创建空文件

~~~sh
[root@ben01 ~]# hdfs dfs -mkdir /empty
[root@ben01 ~]# hdfs dfs -touchz /empty/emp
~~~

> 效果图如下，大小为0B
>
> ![1658200851074](assets/1658200851074.png)
>
> 注：由于我重启机器了，外网IP有所变化



#### 3.4 向分布式文件系统中的文件里追加内容

~~~sh
[root@ben01 ~]# echo "Hello ben" >> e1
[root@ben01 ~]# hdfs dfs -appendToFile e1 /empty/emp
~~~

> 效果图如下，大小为10B
>
> ![1658200889862](assets/1658200889862.png)



#### 3.5 查看指令

~~~sh
# 查看分布式文件系统的目录里的内容
[root@ben01 ~]# hdfs dfs -ls /
# 查看分布式文件系统的文件内容
[root@ben01 ~]# hdfs dfs -cat /xxx.txt
# 查看分布式系统的文件内容
[root@ben01 ~]# hdfs dfs -tail /xxx.txt
~~~



#### 3.6 下载指令

~~~sh
[root@ben01 ~]# hdfs dfs -copyToLocal /empty ./
~~~

> 效果图![1658207092361](assets/1658207092361.png)



#### 3.7 合并下载

~~~sh
# 先创建几个文件
[root@ben01 ~]# echo "Hello ben01" >> file1
[root@ben01 ~]# echo "Hello ben02" >> file2
[root@ben01 ~]# echo "Hello ben03" >> file3
#上传file*文件
[root@ben01 ~]# hdfs dfs -put file* /

# 合并下载file下的文件到本地file下，
[root@ben01 ~]# hdfs dfs -getmerge /file* ./file
~~~

> 效果图
>
> ![1658207398851](assets/1658207398851.png)



#### 3.8  移动（更名）hdfs中的文件

~~~sh
[root@ben01 ~]# hdfs dfs -mv /file1 /empty
~~~



#### 3.9 复制hdfs的文件到hdfs的另一个目录

~~~sh
[root@ben01 ~]# hdfs dfs -cp /file2 /empty
~~~



#### 3.10 删除命令

~~~sh
[root@ben01 ~]# hdfs dfs -rm /file*
Deleted /file2
Deleted /file3

[root@ben01 ~]# hdfs dfs -rmdir /empty/
# 注：必须是空文件夹
~~~



#### 3.11 查看磁盘利用率和文件大小

~~~sh
[root@ben01 ~]# hdfs dfs -df -h
Filesystem            Size     Used  Available  Use%
hdfs://ben01:8020  147.3 G  255.8 K    127.1 G    0%

[root@ben01 ~]# hdfs dfs -du -h /data
0   0   /data/a
11  33  /data/test.txt
~~~



#### 3.12 修改权限

~~~sh
[root@ben01 ~]# hdfs dfs -chmod 777 /data
# 页面Permission的drwxr-xr-x刷新后会变为drwxrwxrwx
# 也可以修改Owner和Group
[root@ben01 ~]# hdfs dfs -chown ben:ben /data
# 页面的Owner和Group会从root:supergroup 变为ben:ben
# 再修改回来
[root@ben01 ~]# hdfs dfs -chown root:supergroup /data
~~~



#### 3.13 修改文件副本数

~~~sh
[root@ben01 ~]# hdfs dfs -setrep 5 /data
Replication 5 set: /data/test.txt

# 之前data下的test.txt的Replication会从3刷新后变成5
# 再改回来
[root@ben01 ~]# hdfs dfs -setrep 3 /data
Replication 3 set: /data/test.txt
~~~



#### 3.14 查看文件的状态

~~~sh
[root@ben01 ~]# hdfs dfs -stat %b /data/test.txt
11

# 调用格式：hdfs dfs -stat [format] 文件路径
%b: 打印文件大小（目录大小为0）
%n: 打印文件名
%o: 打印block的size
%r: 打印副本数
%y: utc时间 yyyy-MM-dd HH:mm:ss
%Y: 打印自1970年1月1日至今的utc微秒数
%P: 目录打印directory，文件打印regular file

# 注意：
1）当使用-stat命令但不指定format时，只打印创建时间，相当于%y
2）-stat后面只跟目录，%r，%o等打印的都是0，只有文件才有副本和大小
~~~



#### 3.15 测试

~~~sh
hdfs dfs [generic options] -test -[defsz] <path> 
# 参数说明：
-e:文件是否存在，存在返回0
-z:文件是否为空，为空返回0
-d:是否是路径（目录），是返回0
# 如下，存在则返回OK
[root@ben01 ~]# hdfs dfs -test -e /data/test.txt && echo "OK" || echo "NO"
OK
# 已知道没有test111.txt这个文件
[root@ben01 ~]# hdfs dfs -test -e /data/test111.txt && echo "OK" || echo "NO"
NO
~~~



### 四. HDFS块的概念

#### 4.1 传统型分布式文件系统的缺点

现在想象一下这种情况：有四个文件0.5TB的file1，1.2TB的file2，50GB的file3，100GB的file4；有7个服务器，每个服务器上有10个1TB的硬盘。

![1658210426284](assets/1658210426284.png)

在存储方式上，我们可以将四个文件存储在同一服务器上（当然大于1TB的文件需要切分），那么缺点也暴露了出来：

1. 负载不均衡

   ~~~
   因为文件大小不一致，势必会导致有的节点磁盘的利用率高，有的利用率低
   ~~~

2. 网络瓶颈问题

   ~~~
   一个过大的文件存储在一个节点磁盘上，当并行处理时，每个线程都需要从这个节点磁盘上读取这个文件内容，那么就会出现网络瓶颈问题，不利于分布式的数据处理。
   ~~~

#### 4.2 HDFS块

HDFS与其它普通文件系统一样，同样引入了块（Block）的概念，并且块的大小是固定的。块是HDFS系统当中的最小存储单位，在Hadoop2.x中默认大小为128MB。在HDFS上的文件会被拆分成多个块，每个块作为独立的单元进行存储。多个块存放在不同的DataNode上，整个过程中HDFS系统会保证一个块存储在一个数据节点上。另外，如果某个文件大小或最后一个块没有达到128MB，则不会占用整个块空间。

以下图为例：

![1658211441669](assets/1658211441669.png)

> 可以看到50G的file3文件会以128MB一个块切分到7台DataNode节点上，每个节点均衡1~2个块



#### 4.3 HDFS块的大小

HDFS块大小为什么为128MB

1. 目的是为了最小化寻址开销时间。
   1. 在I/O开销中，机械硬盘的寻址时间是最耗时的部分，一旦找到第一条记录，剩下的顺序读取效率是非常高的，因此，合适的block大小有助于减少硬盘寻道时间（平衡了硬盘寻道时间、IO时间），提高系统吞吐量。
   2. HDFS寻址开销不仅包括磁盘寻道开销，还包括数据块的定位开销，客户端访问一个文件的流程：名称节点获取组成这个文件的数据块位置列表——>根据位置列表获取到每个数据块的数据节点位置——>数据节点根据数据块信息在本地Linux文件系统中找到对应文件，并返回客户端。
   3. 磁盘的寻址时间为大约5~15ms之间，平均值为10ms，而最小化寻址开销时间普遍认为占1秒的百分之一为最优，那么块的大小就参考1秒的传输速度。
2. 为了节省内存的使用率
   1. 一个块的元数据大约为150个字节。1亿个块，无论大小，都会占用20G左右的内存。因此块越大，集群相对存储数据就越多。这也暴露了HDFS的一个缺点，不适合存储小文件。
      - 不适合小文件的解释，从存储出发：虽然文件不到128M时不能占用整个空间，但是这个块的元数据依然会在内存中占150个字节
      - [不适合小文件的解释]()，从内存占用出发：假设存储一个块都是占用1M和都是128M，同样存储1PB数据，如果是以1M的小文件存储，占用的内存空间为1PB/1MB×150Byte=150G的内存。如果是以128MB的文件存储，占用空间为1PB/128M×150Byte=1.17G的内存占用。可以看到小文件存储比大文件存储占用更多内存。



#### 4.4 块的相关参数设置

~~~xml
# 块大小在默认配置文件hdfs-default.xml中，我们可以在hdfs-default.xml中进行重置
[root@ben01 hadoop]# ll /usr/local/hadoop/etc/hadoop/hdfs-site.xml
<property>
	<name>dfs.blocksize</name>
    <value>134217728</value>
	<description>默认块大小，以字节为单位。可以使用以下后缀（不区分大小写）：k,m,g,t,p,e以重新指定大小(例如128k, 512m, 1g等)</description>
</property>

<property>
    <name>dfs.namenode.fs-limits.min-block-size</name>
    <value>1048576</value>
    <description>以字节为单位的最小块大小，由Namenode在创建时强制执行时间。可以防止意外创建带有小块文件降低性能</description>
</property>

<property>
    <name>dfs.namenode.fs-limits.max-blocks-per-file</name>
    <value>1048576</value>
    <description>每个文件的最大块数，由写入时的Namenode执行。这可以防止创建降低性能的最大文件</description>
</property>
~~~



#### 4.5 块的存储位置

在hdfs-site.xml中配置过下面这个属性，这个属性的值就是块在Linux系统上的存储位置

~~~xml
    <!--确定DFS数据节点应该将其块存储在本地文件系统的何处-->
    <property>
        <name>dfs.datanode.data.dir</name>
        <value>file://${hadoop.tmp.dir}/dfs/data</value>
    </property>
~~~

![1658216074225](assets/1658216074225.png)



#### 4.6 HDFS的优点

~~~sh
1. 高容错性（硬件故障是常态）：数据自动保存多个副本，副本丢失后，会自动恢复。
2. 适合大数据集：GB、TB、甚至PB级数据、千万规模以上的文件数量，1000以上节点规模。
3. 数据访问：一次性写入，多次读取；保证数据一致性，安全性。
4. 构建成本低：可以构建在廉价机器上。
5. 多种软硬件平台中的可移植性。
6. 高效性：Hadoop能够在节点之间动态的移动数据，并保证各个节点的动态平衡，因此处理速度非常快。
7. 高可靠性：Hadoop的存储和处理数据的能力值得人们信赖。
~~~



#### 4.7 HDFS的缺点

~~~
1. 不适合做低延迟数据访问：
	HDFS的设计目标是处理大型数据集，高吞吐率。这一点势必以高延迟为代价，因此HDFS不适合处理毫秒级低延迟应用的请求。
2. 不适合小文件存取：
	一个是大量小文件寻址时间浪费以及元数据浪费。
3. 不适合并发写入，文件随机修改：
	HDFS上的文件只能拥有一个写着，仅支持append操作，不支持多用户对同一文件的写操作，以及在文件任意位置进行修改。
~~~



### 五 HDFS的体系结构

#### 5.1 体系结构解析

~~~
HDFS采用的是master/slaves这种主从的结构模型来管理数据，主要由四个部分组成，分别是Client（客户端）、NameNode（名称节点）、DataNode（数据节点）和SecondaryNameNode。

一般HDFS集群包括一个NameNode和若干个DataNode。

NameNode是一个中心服务器，负责管理文件系统的命令空间（Namespace），它在内存中维护着命名空间的最新状态，同时对持久性文件（fsimage和edit）进行备份，防止宕机后数据丢失。NameNode还负责管理客户端对文件的访问，如权限验证等。

集群中的DataNode一般是一个节点运行一个DataNode进程，真正负责管理客户端的读写请求，在NameNode的统一调度下进行数据块的创建、删除和复制等操作。数据块实际上都是保存在DataNode本地的Linux文件系统中。每个DataNode会定期的向NameNode发送数据，报告自己的状态（我们称之为心跳机制）。没有按时发送心跳信息的DataNode会被NameNode标记为“宕机”，不会在给它分配任何I/O请求。

用户在使用Client进行I/O操作时，依然可以像使用普通文件系统那样，使用文件名去存储和访问文件，只不过在HDFS内部，一个文件会被切分成若干个数据块，并分布存储到若干个DataNode上。

通过Client访问文件流程如下：
客户端把文件名发送给NameNode——>NameNode根据文件名找到对应数据块信息及每个数据块所在DataNode位置——>把这些信息发送给客户端——>客户端直接与DataNode进行通信，获取数据。
这种设计的好处在于实现并发访问，大大提高了数据的访问速度。

HDFS集群中只有唯一的NameNode负责所有元数据的管理工作，这种方式保证了DataNode不会脱离NameNode的控制，同时用户数据也永远不会经过NameNode，大大减轻了NameNode的工作负担，使之更方便管理工作。通常在集群中，要选择一套性能较好的机器作为NameNode。当然一台机器可以运行多个DataNode或NameNode和DataNode放在同一机器，只不过实际部署中通常不会这么做。
~~~

![1658295144547](assets/1658295144547.png)



#### 5.2 开机启动HDFS的过程

5.2.1 非第一次启动集群

我们应该知道，在启动NameNode之前，内存里没有任何有关于元数据的信息的。那么启动集群的过程是怎样的呢？如下：

~~~
第一步：
NameNode启动时，会先加载name目录下最近的fsimage文件。
将fsimage里保存的元数据加载到内存当中，这样内存里就有之前检查点存储的所有元数据。但还少了从最近一次检查时间点到关闭系统时的部分数据，也就是edit日志文件里存储的数据。

第二步：
加载剩下的edit日志文件
将从最近一次检查点到目前为止的所有日志文件加载到内存里，重演一次客户端操作，这样内存里就是最新的文件系统的所有元数据了。

第三步：
进行检查点设置（满足条件会进行）
namenode会终止之前正在使用的edit文件，创建一个空的edit日志文件。将所有的未合并过的edit日志文件和fsimage文件进行合并，产生一个新的fsimage。

第四步：
处于安全模式下，等待datanode节点的心跳反馈，当收到99.9%的块至少一个副本后，退出安全模式，开始转为正常状态。
~~~



5.2.2 非首次启动集群

![1658298385494](assets/1658298385494.png)

> 作者重启过机器，公网IP有所变化。
>
> 首次启动的Loading edits和Saving checkpoint都是空的



#### 5.3 Secondary NameNode 工作机制

Secondary NameNode 是HDFS集群中的重要组成部分，它可以辅助namenode进行fsimage和editlog的合并工作，减少editlog文件大小，以便缩短下次namenode的重启时间，能尽快退出安全模式。

两个文件的合并周期，称之为检查点机制（checkpoint），是可以通过hdfs-default.xml配置文件进行修改的：

~~~xml
<property>
	<name>dfs.namenode.checkpoint.period</name>
	<value>3600</value>
	<description> 两次检查点间隔的秒数，默认是1个小时 </description>
</property>
<property>
 	<name>dfs.namenode.checkpoint.txns</name>
 	<value>1000000</value>
 	<description>txid执行的次数达到100w次，也执行checkpoint</description>
</property> 
<property>
 	<name>dfs.namenode.checkpoint.check.period</name>
 	<value>60</value>
 	<description>60秒一次检查txid的执行次数</description>
</property>
~~~

![1658299068573](assets/1658299068573.png)

通过上图，可以总结如下：

~~~
1. SecondaryNameNode请求namenode停止使用正在编辑的editlog文件，namenode会创建新的editlog文件，同时更新seed_tixd文件。
2. SecondaryNameNode通过HTTP协议获得namenode上的fsimage和editlog文件。
3. SecondaryNameNode将fsimage读进内存当中，并逐步分析editlog文件里的数据，进行合并操作，然后写入新文件fsimage_x.ckpt文件中。
4. SecondaryNameNode将新文件fsimage_x.ckpt通过HTTP协议发送回namenode。
5. namenode再进行更名操作。
~~~



### 六 HDFS的读写流程

#### 6.1 读流程详解

~~~
读操作：
	- hdfs dfs -get /file2 ./file2
	- hdfs dfs -copyToLocal /file2 ./file2
 	- FSDataInputStream fsis = fs.open("/input/a.txt");
 	- fsis.read(byte[] a)
 	- fs.copyToLocal(path1,path2)
~~~

![1658299681037](assets/1658299681037.png)

~~~
1. 客户端通过调用FileSystem对象的open()方法来打开希望读取的文件，对于HDFS来说，这个对象是DistributedFileSystem，它通过使用远程过程调用（RPC）来调用namenode，以确定文件起始块的位置。

2. 对于每个块，namenode返回有该块副本的datanode地址，并根据距离客户端的远近来排序。

3. DistributedFileSystem实例会返回一个FSDataInputStream对象（支持文件定位功能）给客户端以便读取数据，接着客户端对这个输入流调用read() 方法。

4. FSDataInputStream随即连接距离最近的文件中第一个块所在的datanode，通过对数据流反复调用read()方法，将数据从datanode传输到客户端。

5. 当读取到块的末端时，FSInputStream关闭与该datanode的连接，然后寻找下一块最佳datanode

6. 客户端从流中读取数据时，块是按照打开FSInputStream与datanode的新建连接的顺序读取。它也会根据需要询问namenode来检索下一批数据块的datanode位置。一旦客户端完成读取，就对FSInputStream调用close方法。

注意：在读取数据的时候，如果FSInputStream与datanode通信时遇到错误，会尝试从这个块的最近的datanode读取数据，并且记住那个故障的datanode，保证后续不会反复读取该节点上后续的块。FSInputStream也会通过校验和确认从datanode发来的数据是否完整。如果发现有损坏的块，FSInputStream会从其它的块读取副本，并且将损坏的块通知给namenode。
~~~



#### 6.2 写流程详解

~~~
写操作：
	- hdfs dfs -put /file2 ./file2
	- hdfs dfs -copyToLocal /file2 ./file2
 	- FSDataOutputStream fsout = fs.create(path); fsout.write(byte[])
	- fs.copyFromLocal(path1,path2)
~~~

![1658382022692](assets/1658382022692.png)

~~~
1. 客户端通过对DistributedFileSystem对象调用create()方法来新建文件

2. DistributedFileSystem对namenode创建一个RPC调用，在文件系统的命名空间中新建一个文件，此时该文件中还没有相应的数据块。

3. namenode执行各种不同的检查，以确保这个文件不存在以及客户端有新建该文件的权限。如果检查通过，namenode就会为创建新文件记录一条事务记录（否则，文件创建失败并向客户端跑出一个IOException异常）。DistributedFileSystem向客户端返回一个FSDataOuputStream对象，由此客户端可以写入数据。

4. 在客户端写入数据时，FSDataOuputStream将它分成一个个的数据包（packet），并写入一个内部队列，这个队列称为“数据队列”（data queue）。DataStreamer线程负责处理数据队列，它的责任是挑选合适存储数据复本的一组datanode，并以此来要去namenode分配新的数据块。这一组datanode将构成一个管道，以默认复本3个为例，所以该管道中有3个节点。DataStreamer将数据包流式传输到管道中第一个datanode，该datanode存储数据包，并将它发送到管道中的第2个datanode。同样，第2个datanode存储该数据包，并发送给管道中的第三个datanode。DataStreamer再将一个个packet流式传到第一个datanode节点后，还会将此packet从数据队列移动到另一个队列 确认队列（ask queue）中。

5. datanode写入数据成功之后，会为ResponseProcessor线程发送一个写入成功的信息回执，当收到管道中所有的datanode确认信息后，ResponseProcessor线程会将该数据包从确认队列中删除。
~~~

如果任何datanode在写入期间发生故障，则执行以下操作：

~~~~
1. 首先关闭管道，把确认队列中的所有数据包添加回数据队列的最前端，以确保故障节点下游的datanode不会漏掉任何一个数据包。

2. 为存储在另一个正常datanode的当前数据块制定一个新标识，并将该标识传送给namenode，以便故障datanode在恢复后可以删除存储的部分数据块。

3. 从管道中删除故障datanode，基于两个正常datanode构建一条新管道，余下数据块写入管道中正常的datanode。

4. namenode注意到块复本不足时，会在一个新的datanode节点上创建一个新的复本。

注意：一个块被写入期间可能会有多个datanode同时发生故障，但概率非常低。只要写入了。
dfs.namenode.replication.min的复本数（默认1），写操作就会成功，并且这个块可以在集群中异步复制，直到达到其目标复本数dfs.replication的数量（默认3）
~~~~



### 七. Zookeeper的概述

#### 7.1 Zookeeper是什么

1. Zookeeper是一个分布式应用程序提供的一个分布式开源协调服务框架。是Hadoop和Hbase的重要组件，主要用于解决分布式集群中应用系统的一致性问题。
2. 提供了基于Unix系统的目录节点树方式的数据存储。
3. 可用于维护和监控存储的数据的变化，通过监控这些数据状态的变化，从而达到基于数据的集群管理
4. 提供了一组原语（机器指令），提供Java和C语言的接口。



#### 7.2 Zookeeper的特点

1. 一个分布式集群，一个领导者（leader），多个跟随着（follower）。
2. 集群中只要有半数以上的节点存活，Zookeeper集群就能正常服务。
3. 全局数据一致性：每个server保存一份相同的数据副本，client无论连接到哪个server，数据都是一致的。
4. 更新请求按顺序进行：来自同一个client的更新请求按其发送顺序依次执行。
5. 数据更新的原子性：一次数据的更新要么成功，要么失败。
6. 数据的实用性：在一定时间范围内，client能读到最新数据。

![1658384907909](assets/1658384907909.png)



#### 7.3 Zookeeper的数据模型

Zookeeper的数据模型采用的与Unix文件系统类似的层次化的树形结构。我们可以将其理解为一个具有高可用特征的文件系统。这个文件系统中没有文件和目录，而是统一使用“节点”（node）的概念，称之为znode。znode既可以作为保存数据的容器（如同文件），也可以作为保存其它znode的容器（如同目录）。所有的znode构成了一个层次化的命名空间。

![1658385384460](assets/1658385384460.png)

> - Zookeeper 被设计用来实现协调服务（这类服务通常使用小数据文件），而不是用于大容量数据存储，因此一个znode能存储的数据被限制在1MB以内。
> - 每个znode都可以通过其路径唯一标识。



#### 7.4 Zookeeper的应用场景

1. 统一配置管理
2. 统一集群管理
3. 服务器节点动态上下线感知
4. 软负载均衡
5. 分布式锁
6. 分布式队列



### 八. Zookeeper的安装

#### 8.1 安装与环境变量的配置

~~~sh
# 1. 将Zookeeper-3.4.10.tar.gz上传到/root中
# 2. 解压
[root@ben01 softwares]# tar -zxvf zookeeper-3.4.10.tar.gz -C /usr/local
# 3. 更名
[root@ben01 ~]# cd /usr/local
[root@ben01 local]# mv zookeeper-3.4.10 zookeeper
# 4. 配置环境变量
[root@ben01 local]# vi /etc/profile
......
#zk environment
export ZOOKEEPER_HOME=/usr/local/zookeeper
export PATH=$ZOOKEEPER_HOME/bin:$PATH
# 5. 使得当前会话生效
[root@ben01 local]# source /etc/profile
# 6. 检测是否生效
# 配置成功后，tab键可补全zk，有zk相关脚本提示即可。
~~~



#### 8.2 集群模式的配置

8.2.1 Zookeeper的服务进程布局

~~~
ben01	QuorumPeerMain
ben02	QuorumPeerMain
ben03	QuorumPeerMain
~~~

8.2.2 修改zoo.cfg文件

~~~sh
[root@ben01 local]# cd ./zookeeper/conf/
[root@ben01 conf]# cp zoo_sample.cfg zoo.cfg
# 修改存储路径（需要创建）并添加三个服务节点
[root@ben01 conf]# vim zoo.cfg
tickTime=2000	# 定义时间单元（单位毫秒），下面的两个值都是tickTime的倍数
initLimit=10	# follower连接并同步leader的初始化事件
syncLimit=5		# 心跳机制的时间（正常情况下的请求和应答的时间）
dataDir=/usr/local/zookeeper/zkData	# 修改zk的存储路径，需要创建zkData目录
clientPort=2181		# 客户端连接服务器的port
server.1=ben01:2888:3888	# 添加三个服务器节点
server.2=ben02:2888:3888
server.3=ben03:2888:3888

# 保存并退出
[root@ben01 conf]# mkdir ../zkData
[root@ben01 conf]# ll /usr/local/zookeeper/zkData
总用量 0

# 解析Server.id=ip:port1:port2
id：服务器的id号，对应zkData/myid文件内的数字
ip：服务器的ip地址
port1：follower与leader交互的port
port2：选举期间使用的port
~~~

> ~~~sh
> [root@ben01 conf]# cat zoo.cfg 
> tickTime=2000
> initLimit=10
> syncLimit=5
> dataDir=/usr/local/zookeeper/zkData
> clientPort=2181
> server.1=ben01:2888:3888        
> server.2=ben02:2888:3888
> server.3=ben03:2888:3888
> 
> ~~~

8.2.3 添加myid

~~~sh
# $ZOOKEEPER_HOME/zkData目录下添加myid文件，内容为server的id号
[root@ben01 zookeeper]# cd /usr/local/zookeeper/zkData
[root@ben01 zkData]# echo "1" >> myid
~~~

8.2.4 搭建其它两台server节点的环境

1）使用scp命令将zk环境复制到ben02、ben03中

~~~sh
[root@ben01 zkData]# cd /usr/local/
[root@ben01 local]# scp -r zookeeper ben02:/usr/local/
[root@ben01 local]# scp -r zookeeper ben03:/usr/local/
~~~

2）使用scp命令拷贝/etc/profile到两台机器上

~~~sh
[root@ben01 local]# scp /etc/profile ben02:/etc/
[root@ben01 local]# scp /etc/profile ben03:/etc/
~~~

3）source，并修改ben02的myid文件为2

~~~sh
[root@ben02 ~]# source /etc/profile
[root@ben02 ~]# echo "2" > /usr/local/zookeeper/zkData/myid
~~~

4）source，并修改ben02的myid文件为3

~~~sh
[root@ben03 ~]# source /etc/profile
[root@ben03 ~]# echo "3" > /usr/local/zookeeper/zkData/myid
~~~

8.2.5 启动zk

1）三台机器上都启动zk（防火墙需是关闭状态）

~~~sh
[root@ben01 local]# zkServer.sh start
ZooKeeper JMX enabled by default
Using config: /usr/local/zookeeper/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED
# 此时查看状态提示还没运行起来
[root@ben01 local]# zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /usr/local/zookeeper/bin/../conf/zoo.cfg
Error contacting service. It is probably not running.
# 查看jps，已经能看到QuorumPeerMain了，说明启动了，但由于zk是三节点，最低要求有半数以上的节点可用，所以还需启动ben02上的zk
[root@ben01 local]# jps
801 Jps
22898 ResourceManager
22281 DataNode
23100 NodeManager
22078 NameNode
719 QuorumPeerMain

[root@ben02 ~]# zkServer.sh start
ZooKeeper JMX enabled by default
Using config: /usr/local/zookeeper/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED
# 可以看到状态是已经成功的
[root@ben02 ~]# zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /usr/local/zookeeper/bin/../conf/zoo.cfg
Mode: leader
[root@ben02 ~]# jps
21762 NodeManager
21330 DataNode
2565 Jps
1958 QuorumPeerMain
21535 SecondaryNameNode


[root@ben03 ~]# zkServer.sh start
ZooKeeper JMX enabled by default
Using config: /usr/local/zookeeper/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED
[root@ben03 ~]# zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /usr/local/zookeeper/bin/../conf/zoo.cfg
Mode: follower
[root@ben03 ~]# jps
19713 Jps
21619 DataNode
21929 NodeManager
2319 QuorumPeerMain
~~~

2）启动客户端（ben01）

~~~sh
[root@ben01 local]# zkCli.sh
[zk: localhost:2181(CONNECTED) 0] ls
[zk: localhost:2181(CONNECTED) 1] quit
# 退出，连到指定的zk机器
[root@ben01 local]# zkCli.sh -server ben02:2181
[zk: ben02:2181(CONNECTED) 0] quit
~~~



### 九. Zookeeper的shell操作

| 命令   | 描述                                                         | 示例                                            |
| ------ | ------------------------------------------------------------ | ----------------------------------------------- |
| ls     | 查看某个目录包含的所有文件                                   | ls /                                            |
| ls2    | 查看某个目录包含的所有文件，与ls不同的是它查看到time、version等信息 | ls2 /                                           |
| create | 修改znode，并需要设置初始内容                                | create /test "test"<br />create -e /test "test" |
| get    | 获取znode的数据                                              | get /test                                       |
| set    | 修改znode的内容                                              | set /test "test2"                               |
| delete | 删除znode                                                    | delete /test                                    |
| quit   | 退出客户端                                                   |                                                 |
| help   | 帮助命令                                                     |                                                 |



### 十. YARN的概述

为了克服Hadoop  1.x中HDFS和MapReduce存在的各种问题而提出的，针对Hadoop1.x中的MapReduce在扩展性和多框架支持方面的不足，提出了全选的资源管理框架YARN。

Apache YARN是Hadoop集群的资源管理系统，负责为计算程序提供服务器计算资源，相当于一个分布式的操作系统平台，而MapReduce等计算程序则相当于操作系统之上的应用程序。

YARN被引入Hadoop2，最初是为了改善MapReduce的实现，但因其足够的通用性，又支持其他的分布式计算模式，如Spark、Tez等结算框架，逐渐取代原有MapReduce的位置，MapReduce进行了完全重构，发生了根本上的改变，是运行在YARN之上的分布式应用程序。

![1658820940897](assets/1658820940897.png)



### 十一. YARN的架构及组件

#### 11.1 MapReduce 1.x的简介

第一代Hadoop，由分布式存储系统hdfs和分布式计算框架MapReduce组成，其中，hdfs由一个NameNode和多个DataNode组成，MapReduce由一个JobTracker和多个TaskTracker组成，对应Hadoop版本为Hadoop 1.x和0.21.x，0.22.x。

1）MapReduce 1 的角色

~~~
-- 1.Client：作业提交发起者
-- 2.JobTracker：初始化作业，分配作业，与TackTracker通信，协调整个作业。
-- 3.TaskTracker：保持JobTracker通信，在分配的数据片段上执行MapReduce任务。
~~~

![1658821617982](assets/1658821617982.png)

2）MapReduce执行流程

![1658821646593](assets/1658821646593.png)

> ~~~
> 步骤1 提交作业
> 	编写MapReduce程序代码，创建job对象，并进行配置，比如输入和输出路径，压缩格式等，然后通过JobClient来提交作业。
>     
> 步骤2	作业的初始化
> 	客户提交完成后，JobTracker会将作业加入队列，然后进行调度，默认的调度方法是FIFO调试方法。
> 	
> 步骤3	任务的分配
> 	TaskTracker和JobTracker之间的通信与任务的分配是通过心跳机制完成的。
> 	TaskTracker会主动向JobTracker询问是否有作业要做，如果自己可以做，那么就会申请到作业任务，这个任务可以是MapTask也可以是ReduceTask。
> 	
> 步骤4 任务的执行
> 	申请到后，TaskTracker会做如下事情：
> 	- 1.拷贝代码到本地	
> 	- 2.拷贝任务的信息到本地
> 	- 3.启动JVM运行任务
> 
> 步骤5 状态与任务的更新
> 	任务在运行过程中，首先会将自己的状态汇报给TaskTracker，然后由TaskTracker汇总给JobTracker。任务进度是通过计数器来实现的。
> 	
> 步骤6 作业的完成
> 	JobTracker是在接受到最后一个任务运行完成后，才会将任务标记为成功，此时会做删除中间结果等善后工作。
> ~~~



#### 11.2 YARN的设计思想

YARN的基本思想是将资源管理和作业调度/监控功能划分为单独的守护进程。其思想是拥有一个全局ResourceManager（RM），以及每个应用程序拥有一个ApplicationMaster（AM）。应用程序可以是单个作业，也可以是一组作业

![1658823164088](assets/1658823164088.png)

一个ResourceManager和多个NodeManager构成了yarn资源管理框架。他们是yarn启动后长期运行的守护进程，来提供核心服务。

~~~
ResourceManager 是在系统中的所有应用程序之间仲裁资源的最终权威，即管理整个集群上的所有资源分配，内部含有一个Scheduler（资源调度器）

NodeManager 是每台机器的资源管理器，也就是单个节点的管理者，负责启动和监视容器（container）资源使用情况，并向ResourceManager及其Scheduler报告使用情况

Container 是集群上的可用资源，包含cpu、内存、磁盘、网络等

ApplicationMaster（AM） 实际上是框架的特定库，每启动一个应用程序，都会启动一个AM，它的任务是与ResourceManager协商资源，并与NodeManager一起执行和监视任务。
~~~

扩展）YARN与MapReduce 1的比较

| MapReduce 1 | YARN                                                  |
| ----------- | ----------------------------------------------------- |
| Jobtracker  | Resource manager, application master, timeline server |
| Tasktracker | Node manager                                          |
| Slot        | Container                                             |



#### 11.3 YARN的配置

YARN属于Hadoop的一个组件，不需要再单独安装程序，Hadoop中已存在配置文件的设置，本身就是一个集群，有主节点和从节点。

~~~
注意<value></value>之间的值不能有空格
~~~

在mapred-site.xml中的配置如下：

> 无需再配

~~~xml
<configuration>
        <!--用于执行MapReduce作业运行时的框架-->
        <property>
                 <name>mapreduce.framework.name</name>
                 <value>yarn</value>
        </property>
        <!--历史任务的内部通讯地址-->
        <property>
                <name>mapreduce.jobhistory.address</name>
                <value>ben01:10020</value>
        </property>
        <!--历史任务的外部监听页面-->
        <property>
                <name>mapreduce.jobhistory.webapp.address</name>
                <value>ben01:19888</value>
        </property>
        <!-- 下面的需要配置,否则报错:找不到AppMaster -->
        <property>
                <name>yarn.app.mapreduce.am.env</name>
                <value>HADOOP_MAPRED_HOME=/usr/local/hadoop</value>
        </property>
        <property>
                <name>mapreduce.map.env</name>
                <value>HADOOP_MAPRED_HOME=/usr/local/hadoop</value>
        </property>
        <property>
                <name>mapreduce.reduce.env</name>
                <value>HADOOP_MAPRED_HOME=/usr/local/hadoop</value>
        </property>
</configuration>
~~~

在yarn-site.xml中的配置如下：

> 无需再配

~~~xml
<configuration>

<!-- Site specific YARN configuration properties -->
        <!-- 指定yarn的shuffle技术 -->
        <property>
                <name>yarn.nodemanager.aux-services</name>
                <value>mapreduce_shuffle</value>
        </property>
        <!-- 指定resourcemanager的主机名 -->
        <property>
                <name>yarn.resourcemanager.hostname</name>
                <value>ben01</value>
        </property>
        <!--指定shuffle对应的类-->
        <property>
                <name>yarn.nodemanager.aux-services.mapreduce_shuffle.class</name>
                <value>org.apache.hadoop.mapred.ShuffleHandler</value>
        </property>

        <!--配置resourcemanager的内部通讯地址 -->
        <property>
                <name>yarn.resourcemanager.address</name>
                <value>ben01:8032</value>
        </property>
        <!--配置resourcemanager的scheduler内部通讯地址 -->
        <property>
                <name>yarn.resourcemanager.scheduler.address</name>
                <value>ben01:8030</value>
        </property>
    <!--配置resoucemanager的资源调度的内部通讯地址-->
        <property>
                <name>yarn.resourcemanager.resource-tracker.address</name>
                <value>ben01:8031</value>
        </property>
        <!--配置resourcemanager的管理员的内部通讯地址-->
        <property>
                <name>yarn.resourcemanager.admin.address</name>
                <value>ben01:8033</value>
        </property>
        <!--配置resourcemanager的web ui 的监控页面-->
        <property>
                <name>yarn.resourcemanager.webapp.address</name>
                <value>ben01:8088</value>
        </property>
</configuration>
~~~

1）日志位置

~~~sh
jps：当启动进程时出错后的解决步骤

如果是hdfs上的问题，则查看对应的日志
less 或 tail -1000 $HADOOP_HOME/logs/hadoop-{user.name}-{jobname}-{hostname}.log
如果是yarn则查看
less 或 tail -1000 $HADOOP_HOME/logs/yarn-{user.name}-{jobname}-{hostname}.log
~~~

2）历史服务

~~~sh
如果需要查看YARN作业历史，需要打开历史服务：
# 1.先停止当前YARN进程
[root@ben01 ~]# jps
22898 ResourceManager
20324 Jps
22281 DataNode
23100 NodeManager
22078 NameNode
719 QuorumPeerMain

[root@ben01 ~]# stop-yarn.sh
[root@ben01 ~]# jps
26176 Jps
22281 DataNode
22078 NameNode
719 QuorumPeerMain
# 可以看到有关yarn的服务都停了，如Nodemanager、ResourceManager，另外两台机器也可以看到Nodemanager已关闭。

# 2.打开并添加配置
[root@ben01 ~]# cd /usr/local/hadoop/etc/hadoop/
[root@ben01 hadoop]# vim yarn-site.xml 
<configuration>
......
<!-- 开启日志聚集功能 -->
<property>
	<name>yarn.log-aggregation-enable</name>
 	<value>true</value>
</property>
<!-- 日志信息报错在文件系统上的最长时间，单位为秒 -->
<property>
 	<name>yarn.log-aggregation.retain-seconds</name>
 	<value>640800</value>
</property>
</configuration>

# 3.分发到其它节点
[root@ben01 hadoop]# scp yarn-site.xml ben02:$PWD
[root@ben01 hadoop]# scp yarn-site.xml ben03:$PWD

# 4.启动YARN进程
[root@ben01 hadoop]# start-yarn.sh
[root@ben01 hadoop]# jps
20336 ResourceManager
20547 NodeManager
22772 Jps
22281 DataNode
22078 NameNode
719 QuorumPeerMain

# 5.开启历史服务
[root@ben01 hadoop]# mapred --daemon start historyserver
[root@ben01 hadoop]# jps
20336 ResourceManager
32672 Jps
20547 NodeManager
22281 DataNode
26381 JobHistoryServer
22078 NameNode
719 QuorumPeerMain
# 可以看到JobHistoryServer已经有了
~~~



### 阶段关机需要做的操作

~~~sh
# 关机前需要关闭的服务，zk（zk需要三台机器都操作）、hadoop、作业任务
[root@ben01 ~]# zkServer.sh stop
[root@ben02 ~]# zkServer.sh stop
[root@ben03 ~]# zkServer.sh stop
[root@ben01 ~]# mapred --daemon stop historyserver
[root@ben01 ~]# stop-all.sh 
[root@ben01 ~]# jps
32672 Jps

# 开机需要开启的服务
[root@ben01 ~]# start-all.sh
# 此时查看三台机器的jps可以看到服务启动的内容
[root@ben01 ~]# zkServer.sh start
[root@ben02 ~]# zkServer.sh start
[root@ben03 ~]# zkServer.sh start
[root@ben01 ~]# mapred --daemon start historyserver
[root@ben01 ~]# jps
19507 Jps
13193 DataNode
14012 NodeManager
15724 QuorumPeerMain
12972 NameNode
13821 ResourceManager
19343 JobHistoryServer
~~~



### 十二. YARN的执行原理

在MR程序运行时，有五个独立的进程：

~~~sh
-- YarnRunner：用于提交作业的客户端程序
-- ResourceManager：yarn资源管理器，负责协调集群上计算机资源的分配
-- NodeManager：yarn节点管理器，负责启动和监视集群中机器上的计算容器（container）
-- Application Master：负责协调运行MapReduce作业的任务，它和任务都在容器中运行，这些容器由资源管理器分配并由节点管理器进行管理。
-- HDFS：用于共享作业所需文件。
~~~

整个过程如下图描述：

![1658887440421](assets/1658887440421.png)

> ~~~sh
> 1. 调用waitForCompletion方法每秒轮询作业的进度，内部封装了submit()方法，用于创建JobCommiter实例，并且调用其的submitJobInternal方法。提交成功后，如果有状态改变，就会把进度报告到控制台。错误也会报告到控制台。
> 2. JobCommiter实例会向ResourceManager申请一个新应用ID，用于MapReduce作业ID。这期间JobCommiter也会进行检查输出路径的情况，以及计算输入分片。
> 3. 如果成功申请到ID，就会将运行作业所需要的资源（包括作业jar文件，配置文件和计算所得的输入分片元数据文件）上传到一个用ID命令目录下HDFS上。此时副本个数是10。
> 4. 准备工作已做好，再通知ResourceManager调用submitApplication方法提交作业。
> 5. ResourceManager调用submitApplication方法后，会通知Yarn调度器（Scheduler），调度器分配一个容器，在节点管理器的管理下在容器中启动 application master进程。
> 6. application master的主类是MRAppMaster，其主要作用是初始化任务，并接受来来自任务的进度和完成报告。
> 7. 然后从HDFS上接受资源，主要是split。然后为每一个split创建MapTask以及参数指定的ReduceTask，任务ID在此时分配。
> 8. 然后Application Master会向资源管理器请求容器，首先为MapTask申请容器，然后再为ReduceTask申请容器。（5%）
> 9. 一旦ResourceManager中的调度器（Scheduler），为Task分配了一个特定节点上的容器，Application Master就会与NodeManager进行通信来启动容器。
> 10. 运行任务是由YarnChild来执行的，运行任务前，先将资源本地化（jar文件，配置文件，缓存文件）
> 11. 然后开始运行MapTask或ReduceTask。
> 12. 当收到最后一个任务已经完成通知后，application master会把作业状态设置为success。然后Job轮询时，知道成功完成，就会通知客户端，并把统计信息输出到控制台。
> ~~~
>
> 

### 十三. YARN的案例测试

使用官方提供的

~~~sh
# 1.创建一个内容
[root@ben01 ~]# ls
e1  empty  file  file1  file2  file3  softwares  test.txt
[root@ben01 ~]# mkdir input 
[root@ben01 ~]# mv file* input/
[root@ben01 ~]# cat input/*
Hello ben01
Hello ben02
Hello ben03
Hello ben01
Hello ben02
Hello ben03

# 2.将文件夹put到hdfs上
[root@ben01 ~]# hdfs dfs -put input/ /
[root@ben01 ~]# hdfs dfs -cat /input/*

# 3.官方案例
[root@ben01 ~]# cd /usr/local/hadoop/share/hadoop/mapreduce

# 4.运行
[root@ben01 mapreduce]# hadoop jar hadoop-mapreduce-examples-3.3.1.jar wordcount /input /output
~~~

> 输入内容如下：
>
> ~~~sh
> [root@ben01 mapreduce]# hadoop jar hadoop-mapreduce-examples-3.3.1.jar wordcount /input /output
> 2022-07-27 09:38:32,446 INFO client.DefaultNoHARMFailoverProxyProvider: Connecting to ResourceManager at ben01/10.206.0.10:8032
> 2022-07-27 09:38:32,917 INFO mapreduce.JobResourceUploader: Disabling Erasure Coding for path: /tmp/hadoop-yarn/staging/root/.staging/job_1658885211305_0002
> 2022-07-27 09:38:33,181 INFO input.FileInputFormat: Total input files to process : 4
> 2022-07-27 09:38:33,267 INFO mapreduce.JobSubmitter: number of splits:4
> 2022-07-27 09:38:33,832 INFO mapreduce.JobSubmitter: Submitting tokens for job: job_1658885211305_0002
> 2022-07-27 09:38:33,832 INFO mapreduce.JobSubmitter: Executing with tokens: []
> 2022-07-27 09:38:34,002 INFO conf.Configuration: resource-types.xml not found
> 2022-07-27 09:38:34,002 INFO resource.ResourceUtils: Unable to find 'resource-types.xml'.
> 2022-07-27 09:38:34,055 INFO impl.YarnClientImpl: Submitted application application_1658885211305_0002
> 2022-07-27 09:38:34,086 INFO mapreduce.Job: The url to track the job: http://ben01:8088/proxy/application_1658885211305_0002/
> 2022-07-27 09:38:34,087 INFO mapreduce.Job: Running job: job_1658885211305_0002
> 2022-07-27 09:38:41,181 INFO mapreduce.Job: Job job_1658885211305_0002 running in uber mode : false
> 2022-07-27 09:38:41,182 INFO mapreduce.Job:  map 0% reduce 0%
> 2022-07-27 09:38:52,312 INFO mapreduce.Job:  map 25% reduce 0%
> 2022-07-27 09:38:53,322 INFO mapreduce.Job:  map 100% reduce 0%
> 2022-07-27 09:38:57,359 INFO mapreduce.Job:  map 100% reduce 100%
> 2022-07-27 09:38:57,375 INFO mapreduce.Job: Job job_1658885211305_0002 completed successfully
> 2022-07-27 09:38:57,459 INFO mapreduce.Job: Counters: 54
>         File System Counters
>                 FILE: Number of bytes read=126
>                 FILE: Number of bytes written=1363365
>                 FILE: Number of read operations=0
>                 FILE: Number of large read operations=0
>                 FILE: Number of write operations=0
>                 HDFS: Number of bytes read=447
>                 HDFS: Number of bytes written=32
>                 HDFS: Number of read operations=17
>                 HDFS: Number of large read operations=0
>                 HDFS: Number of write operations=2
>                 HDFS: Number of bytes read erasure-coded=0
>         Job Counters 
>                 Launched map tasks=4
>                 Launched reduce tasks=1
>                 Data-local map tasks=4
>                 Total time spent by all maps in occupied slots (ms)=40246
>                 Total time spent by all reduces in occupied slots (ms)=2673
>                 Total time spent by all map tasks (ms)=40246
>                 Total time spent by all reduce tasks (ms)=2673
>                 Total vcore-milliseconds taken by all map tasks=40246
>                 Total vcore-milliseconds taken by all reduce tasks=2673
>                 Total megabyte-milliseconds taken by all map tasks=41211904
>                 Total megabyte-milliseconds taken by all reduce tasks=2737152
>         Map-Reduce Framework
>                 Map input records=6
>                 Map output records=12
>                 Map output bytes=120
>                 Map output materialized bytes=144
>                 Input split bytes=375
>                 Combine input records=12
>                 Combine output records=10
>                 Reduce input groups=4
>                 Reduce shuffle bytes=144
>                 Reduce input records=10
>                 Reduce output records=4
>                 Spilled Records=20
>                 Shuffled Maps =4
>                 Failed Shuffles=0
>                 Merged Map outputs=4
>                 GC time elapsed (ms)=1627
>                 CPU time spent (ms)=2840
>                 Physical memory (bytes) snapshot=1411006464
>                 Virtual memory (bytes) snapshot=13943873536
>                 Total committed heap usage (bytes)=1187512320
>                 Peak Map Physical memory (bytes)=309903360
>                 Peak Map Virtual memory (bytes)=2796343296
>                 Peak Reduce Physical memory (bytes)=201601024
>                 Peak Reduce Virtual memory (bytes)=2792300544
>         Shuffle Errors
>                 BAD_ID=0
>                 CONNECTION=0
>                 IO_ERROR=0
>                 WRONG_LENGTH=0
>                 WRONG_MAP=0
>                 WRONG_REDUCE=0
>         File Input Format Counters 
>                 Bytes Read=72
>         File Output Format Counters 
>                 Bytes Written=32
> ~~~
>
> 



### 十五. YARN的Web UI查看

外网加上8088端口访问

![1658886205864](assets/1658886205864.png)



**Hadoop基础部分已完成🎉🎉🎉**