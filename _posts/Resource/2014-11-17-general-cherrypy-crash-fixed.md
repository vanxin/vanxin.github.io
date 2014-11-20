---
layout: post
title: Solution of CherryPy2.7b3 crash
category: 资源
tags: CherryPy Jython
keywords: CherryPy Jython Crash
description:
---


## CherryPy2.7b运行崩溃的解决办法


### 测试源码

    import cherrypy
    class HelloWorld(object):
    	def index(self):
            return "Hello World!"
    	index.exposed = True

    cherrypy.quickstart(HelloWorld())

### 错误描述

    PM org.python.netty.channel.AbstractChannel$AbstractUnsafe register
    WARNING: Force-closing a channel whose registration task was not accepted by an event loop

### 解决办法

    为Jython安装文件夹下的两个文件/Lib/_scoket.py及/Lib/test_scoket.py分别打上补丁即可,具体Bug描述及补丁文件见[解决方法](https://hg.python.org/jython/rev/a33353c0ac25)

### Patch

-  将changeset文件保存为scoket.patch文件  
-  存放在Jython安装目录中(Lib的父目录)  
-  执行`patch -p1 < scoket.patch`命令打上补丁  

### 参考资料

-  [CherryPy Crash](http://bugs.jython.org/issue2207)  
-  [Bugs of Jython](http://bugs.jython.org/issue2201)  
-  [Jython Changeset](https://hg.python.org/jython/rev/a33353c0ac25)  
