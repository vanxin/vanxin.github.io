---
layout: post
title: ubuntu14.04(64位)下编译hadoop2.2.0
category: 技术
tags: Hadoop
description: ubuntu14.04(64位)下编译hadoop2.2.0
---

>最近做的一个项目需要用到分布式计算，故搭建hadoop集群，因为用的是64位的ubuntu系统，所以需要对hadoop2.2.0进行重新编译，此篇日志也记录下编译的过程和心得。由于编译过程中没有截图，也没有设置shell导出日志文件，比较可惜。

## 编译平台

ubuntu 14.04(或者其他linux系统均可)

## 配置环境

1、安装JDK

   hadoop使用java语言编写，编译hadoop源码必定需要安装java开发环境JDK
   推荐java 1.7版本，java 1.8版本编译时会有意想不到的错误！
   Java SE 7u55[下载](http://www.oracle.com/technetwork/java/javase/downloads/index.html?ssSourceSiteId=otnjp)
   通常复制文件至/usr/lib/jvm/文件夹下，并解压。

    cd /usr/lib 
    mkdir jvm                                  //新建jvm文件夹
    cd jvm                                     //切换目录进入jvm文件夹
    cp /下载目录/jdk-7u55-linux-x64.tar.gz ./  //复制文件到当前目录
    tar -xzf jdk-7u55-linux-x64.tar.gz         //解压文件
    mv jdk1.7.0_55 java-7-sun                  //重命名文件夹方便记忆
    vim ～/.bashrc                             //编辑环境变量配置文件
    export JAVA_HOME=/usr/lib/jvm/java-7-sun   //添加java路径
    export JRE_HOME=${JAVA_HOME}/jre           //添加jre路径
    export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib  //添加classpath路径
    export PATH=${JAVA_HOME}/bin:$PATH         //添加系统路径并保存退出
    source ~/.bashrc                           //是环境变量的改变生效
    java -version                              //检测是否安装成功：成功打印版本号即为安装成功

2、安装maven

   hadoop源码是使用maven组织管理的，编译hadoop必须下载maven
   maven3.2.1[下载](http://maven.apache.org/download.cgi)
   复制该文件至java-7-sun目录下并解压，配置环境变量

    cd /usr/lib/jvm/java-7-sun                  //进入java安装目录
    cp /下载目录/apache-maven-3.2.1-bin.tar.gz ./   //复制文件到当前文件夹
    tar -xzf apache-maven-3.2.1-bin.tar.gz      //解压文件
    mv apache-maven-3.2.1 maven3                //重命名文件夹方便记忆
    vim ～/.bashrc                              //编辑环境变量配置文件
    export M3_HOME=/usr/lib/jvm/java-7-sun/maven3   //添加maven环境变量
    export PATH=${JAVA_HOME}/bin:${M3_HOME}/bin:$PATH         //添加系统路径并保存退出
    source ~/.bashrc                           //是环境变量的改变生效
    mvn -version                               //检测是否安装成功：成功打印版本号

3、安装findbugs

   findbugs用于生成文档
   findbugs3.0.0[下载](http://sourceforge.jp/projects/sfnet_findbugs/releases/)
   复制该文件至java-7-sun目录下并解压，配置环境变量

    cd /usr/lib/jvm/java-7-sun                  //进入java安装目录
    cp /下载目录/findbugs-3.0.0-dev... ./       //复制文件到当前文件夹
    tar -xzf findbugs-3.0.0-dev...              //解压文件
    mv findbugs-3.0.0-dev... findbugs3          //重命名文件夹方便记忆
    vim ～/.bashrc                              //编辑环境变量配置文件
    export FINDBUGS_HOME=/usr/lib/jvm/java-7-sun/findbugs3  //添加findbugs路径
    export PATH=${JAVA_HOME}/bin:${M3_HOME}/bin:${FINDBUGS_HOME}/bin:$PATH         //添加系统路径并保存退出
    source ~/.bashrc                           //是环境变量的改变生效
    findbugs -version                               //检测是否安装成功：成功打印版本号

4、安装protoc

   hadoop使用protocol buffer进行通信，所以必须安装protoc
   protoc[下载](https://code.google.com/p/protobuf/downloads/list)
   为了编译protoc需下载如下工具

    sudo apt-get install g++
    sudo apt-get install cmake

   安装好后将protocbuf-2.5.0复制至java-7-sun目录下并解压

    cd /usr/lib/jvm/java-7-sun                  //进入java安装目录
    cp /下载目录/protocbuf-2.5.0.tar.gz ./      //复制文件到当前文件夹
    tar -xzf protocbuf-2.5.0.tar.gz             //解压文件
    cd protocbuf-2.5.0                          //进入文件夹进行编译安装protoc
    ./configure
    make && make check && make install          //编译安装protoc
    vim ～/.bashrc                              //编辑环境变量配置文件
    export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib   //添加环境变量
    source ~/.bashrc                            //是环境变量的改变生效
    protoc --version                            //检测是否安装成功：成功打印版本号

5、安装其他依赖项

    sudo apt-get install maven build-essential autoconf automake libtool cmake zlib1g-dev pkg-config libssl-dev libglib2.0-dev

6、编译hadoop

   hadoop源码[下载](http://apache.fayea.com/apache-mirror/hadoop/common/stable2/)
   解压源码进入该文件夹执行如下指令进行编译

    mvn package -DskipTests -Pdist,native,docs

   如果没有出现问题，则编译完成后的代码在/hadoop-2.2.0-src/hadoop-dist/target/文件夹下

7、常见问题

   0、路径问题

    [ERROR]中包括{FINDBUGS_HOME}{M3_HOME}等路径问题的首先确定以上环境均已经配置好并且能够打印出版本号。

   1、Java版本问题

    [ERROR] Failed to execute goal org.apache.maven.plugins:maven-javadoc-plugin:2.8.1:jar (module-javadocs) on project hadoop-maven-plugins: MavenReportException: Error while creating archive:
    [ERROR] Exit code: 1 - /usr/local/hadoop-2.2.0-src/hadoop-maven-plugins/src/main/java/org/apache/hadoop/maven/plugin/util/Exec.java:47: error: unknown tag: String

    解决办法：使用jdk1.7.0代替jdk1.8.0 

   2、hadoop-auth由于依赖缺失问题，具体描述见[bug](https://issues.apache.org/jira/browse/HADOOP-10110)

    [ERROR] Failed to execute goal org.apache.maven.plugins:maven-compiler-plugin:2.5.1:testCompile (default-testCompile) on project hadoop-auth: Compilation failure: Compilation failure:
    [ERROR] /home/chuan/trunk/hadoop-common-project/hadoop-auth/src/test/java/org/apache/hadoop/security/authentication/client/AuthenticatorTestCase.java:[84,13] cannot access org.mortbay.component.AbstractLifeCycle
    [ERROR] class file for org.mortbay.component.AbstractLifeCycle not found

    解决办法：下载patch并打上补丁
    patch -p0 < HADOOP-10100.patch

   3、错误的maven-site-plugin版本问题，具体描述见[bug](https://issues.apache.org/jira/browse/HADOOP-10092)

    [ERROR] Failed to execute goal org.apache.maven.plugins:maven-site-plugin:3.0:site (default-site) on project hadoop-main: Execution default-site of goal org.apache.maven.plugins:maven-site-plugin:3.0:site failed: A required class was missing while executing org.apache.maven.plugins:maven-site-plugin:3.0:site: org/sonatype/aether/graph/DependencyFilter
    [ERROR] -----------------------------------------------------
    [ERROR] realm =    plugin>org.apache.maven.plugins:maven-site-plugin:3.0
    [ERROR] strategy = org.codehaus.plexus.classworlds.strategy.SelfFirstStrategy
    [ERROR] urls[0] = file:/home/dc/.m2/repository/org/apache/maven/plugins/maven-site-plugin/3.0/maven-site-plugin-3.0.jar
    [ERROR] urls[1] = file:/home/dc/.m2/repository/org/apache/maven/wagon/wagon-ssh/1.0/wagon-ssh-1.0.jar
    [ERROR]...

    解决办法1：
    <plugin>
    <artifactId>maven-site-plugin</artifactId>
    <version>3.0</version>
    ...
    修改为:
    <version>3.3</version>
	
    <plugin>
    <groupId>org.apache.maven.plugin</groupId>
    <artifactId>maven-site-plugin</artifactId>
    <version>3.0</version>
    ...
    修改为
    <version>3.3</version>

    解决办法2：在bug报告中下载补丁并patch即可
    patch -p0 < HADOOP-10092

