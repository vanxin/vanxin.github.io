---
layout: post
title: ubuntu 14.04(64位)下尝试安装hadoop2.2.0
category: 技术
tags: Hadoop
description: ubuntu 14.04(64位)下尝试安装hadoop2.2.0
---

>简单尝试单节点安装hadoop2.2.0并运行自带的wordcount测试样例

## 编译hadoop2.2.0

   具体编译方法[参见](http://ivanxin.com/2014/05/19/Compile-Hadoop2.2.0-on-ubuntu14.04-x64.html)
   或者直接拷贝已经编译好的文件[hadoop2.2.0-x64-下载](http://pan.baidu.com/s/16bk0u)
   下载后解压至/home/目录下，个人兴趣爱好而已，目录随意放

## 安装ssh和rsync

   为什么要安装ssh和rsync呢？rsync是类unix系统下的数据镜像同步工具，全程为remote sync，具备镜像保存整个目录树和文件系统、容易做到保持原来文件爱女的权限、时间爱女、软硬链接等，无须特殊权限即可安装，优化的流程，文件传输效率高（传输的数据可以经过压缩），可以使用rcp、ssh等方式来传输文件，支持匿名传输等特性。而ssh就是用于rsync远程同步，用户验证及文件传输控制的，它可以对所有传输数据进行加密。
   具体可以参见[Rsync](http://zh.wikipedia.org/wiki/Rsync)及[SSH](http://zh.wikipedia.org/wiki/Secure_Shell)
   安装ssh及rsync

    $ sudo apt-get install ssh
    $ sudo apt-get install rsync

## 修改环境变量

   添加hadoop环境变量,编辑~/.bashrc文件

    vim ~/.bashrc                          #打开配置文件，将以下内容添加
    # add for hadoop 2.2
    export HADOOP_HOME=/home/hadoop-2.2.0  #路劲为hadoop文件夹路径
    export HADOOP_MAPRED_HOME=$HADOOP_HOME #添加mapred路径
    export HADOOP_COMMON_HOME=$HADOOP_HOME #添加common路径
    export HADOOP_HDFS_HOME=$HADOOP_HOME   #添加分布式文件系统路径
    export YARN_HOME=$HADOOP_HOME          #添加yarn路径
    export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop #添加配置文件路径

## 添加namenode目录及datanode目录

    mkdir -p /home/hadoop-2.2.0/yarn/yarn_data/hdfs/namenode 
    mkdir -p /home/hadoop-2.2.0/yarn/yarn_data/hdfs/datanode
    #如果HADOOP_HOME路径设置正确，可以直接在shell输入$HADOOP_HOME如果返回路径则证明设置正确，此时可以修改以上两条命令如下（防止文件夹路径手抖设置错误）
    mkdir -p $HADOOP_HOME/yarn/yarn_data/hdfs/namenode 
    mkdir -p $HADOOP_HOME/yarn/yarn_data/hdfs/datanode

## 修改配置文件

   修改的文件都在$HADOOP_HOME/etc/hadoop下：core-site.xml、hdfs-site.xml、yarn-site.xml 、mapred-site.xml. 四个文件，配置文件的添加和修改都在每个文件的标签<configuration></configuration> 中
   core-site.xml文件用于定义系统级别的参数,如hdfs url，临时目录等配置，此处配置修改为：

    <configuration>
     <!-- fs.defaultFS这是一个描述集群中NameNode结点的URI(包括协议、主机名称、端口号)，集群里面的每一台机器都需要知道NameNode的地址。DataNode结点会先在NameNode上注册，这样它们的数据才可以被使用。独立的客户端程序通过这个URI跟DataNode交互，以取得文件的块列表 -->
     <property>
      <name>fs.defaultFS</name>
      <value>hdfs://localhost:9000</value>
     </property>
     <!—hadoop.tmp.dir 是hadoop文件系统依赖的基础配置，很多路径都依赖它。如果hdfs-site.xml中不配置namenode和datanode的存放位置，默认就放在这个路径中-->
     <property>
      <name>hadoop.tmp.dir</name>
      <value>/home/hdfs/tmp</value>
     </property>
    </configuration>

   hdfs-site.xml文件用于设置HDFS的相关设定，如文件副本个数，块大小，是否强制权限等，此处配置修改为：

     <configuration>
       <!-- dfs.replication它决定着系统里面的文件块的数据备份个数。对于一个实际的应用，它 应该被设为3（这个数字并没有上限，但更多的备份可能并没有作用，而且会占用更多的空间）。少于三个的备份，可能会影响到数据的可靠性(系统故障时，也许会造成数据丢失)-->
      <property>
       <name>dfs.replication</name>
       <value>1</value>
      </property>
      <!--dfs.name.dir这是NameNode结点存储hadoop文件系统信息的本地系统路径。这个值只对NameNode有效，DataNode并不需要使用到它。上面对于/temp类型的警告，同样也适用于这里。在实际应用中，它最好被覆盖掉-->
      <property>
       <name>dfs.namenode.name.dir</name>
       <value>/home/hadoop-2.2.0/yarn/yarn_data/hdfs/namenode</value>
      </property>
      <!-- dfs.data.dir这是DataNode结点被指定要存储数据的本地文件系统路径。DataNode结点上的这个路径没有必要完全相同，因为每台机器的环境很可能是不一样的。但如果每台机器上的这个路径都是统一配置的话，会使工作变得简单一些。默认的情况下，它的值hadoop.tmp.dir, 这个路径只能用于测试的目的，因为，它很可能会丢失掉一些数据。所以，这个值最好还是被覆盖。-->
      <property>
       <name>dfs.datanode.data.dir</name>
       <value>/home/hadoop-2.2.0/yarn/yarn_data/hdfs/datanode</value>
      </property>
    </configuration>

   mapred-site.xml:HDFS的相关设定，如reduce任务的默认个数、任务所能够使用内存的默认上下限等,此处可能文件不存在，直接复制mapred-site.xml.template即可，

    cp mapred-site.xml.template mapred-site.xml

   修改配置如下：

    <configuration>
     <property>
      <name>mapreduce.cluster.temp.dir</name>
      <value></value>
      <description>No description</description>
      <final>true</final>
     </property>
     <property>
      <name>mapreduce.cluster.local.dir</name>
      <value></value>
      <description>No description</description>
      <final>true</final>
     </property>
    </configuration>

   hadoop-env.sh:用于定义hadoop运行环境相关的配置信息，比如配置JAVA_HOME环境变量、为hadoop的JVM指定特定的选项、指定日志文件所在的目录路径以及master和slave文件的位置等,如果$JAVA_HOME变量已经正确设置，则可以不用修改，否则将文件中的以下命令进行修改：

    export JAVA_HOME = /usr/lib/jvm/java-7-sun   #设置为自己的java路径即可

   yarn-site.xml:暂时还未理解，按照他人教程修改如下：

    <property>
     <name>yarn.nodemanager.aux-services</name>
     <value>mapreduce_shuffle</value>
    </property>
    <property>
     <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
     <value>org.apache.hadoop.mapred.ShuffleHandler</value>
    </property>

## 初始化namenode

    bin/hadoop namenode -format

### 启动hadoop守护进程

    sbin/hadoop-daemon.sh start namenode
    sbin/hadoop-daemon.sh start datanode
    sbin/hadoop-daemon.sh start secondarynamenode
    sbin/yarn-daemon.sh start resourcemanager
    sbin/yarn-daemon.sh start nodemanager
    sbin/mr-jobhistory-daemon.sh start historyserver

## 查看服务状态

    http://localhost:50070     #查看hadoop状态
    http://localhost:8088      #查看all application
    http://localhost:19888     #查看jodhistory

## 测试，创建文件并复制到dfs中

    mkdir in 
    vi in/file
    Hadoop is fast
    Hadoop is cool 
    bin/hadoop fs -copyFromLocal in/ /in
    //用Hadoop自带的wordcount进行测试：
    bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.2.0.jar wordcount /in /out
    //如果安装正确，您将可以在屏幕上看到任务调用后的各项处理信息。
    //如果你再次调用该任务，可能会出现/out目录已存在的错误信息，此时您可以调用以下命令清除对应的目录，比如：
    bin/hadoop fs -rm -r /out

## 停止服务

    sbin/hadoop-daemon.sh stop namenode
    sbin/hadoop-daemon.sh stop datanode
    sbin/hadoop-daemon.sh stop secondarynamenode
    sbin/yarn-daemon.sh stop resourcemanager
    sbin/yarn-daemon.sh stop nodemanager
    sbin/mr-jobhistory-daemon.sh stop historyserver

## 参考资料

   [hadoop配置文件详解、安装及相关操作](http://blog.csdn.net/lin_fs/article/details/7349497)
   [Hadoop2.2.0单节点安装](http://f.dataguru.cn/thread-251624-1-1.html)
   [Hadoop配置文件及常用配置参数详解](http://mageedu.blog.51cto.com/4265610/1112047)
   [Hadoop MapReduce Next Generation - Setting up a Single Node Cluster.](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-common/SingleCluster.html)
   [Hadoop新MapReduce框架Yarn详解](http://www.ibm.com/developerworks/cn/opensource/os-cn-hadoop-yarn/index.html)
   [Getting started with NextGen MapReduce (single node) in easy steps](http://www.thecloudavenue.com/2012/01/getting-started-with-nextgen-mapreduce.html)

