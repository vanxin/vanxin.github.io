---
layout: post
title: Goagent + Chrome
category: 资源
tags: [Goagent,Chrome]
keywords: Goagent,Chrome
description:
---

## Goagent+Chrome 教程

### Goagent+Chrome 部署
    1. [申请谷歌帐号](https://accounts.google.com/SignUp?continue=https%3A%2F%2Fwww.google.com.hk%2F&hl=en)  
    2. [申请Google Appengine并创建appid](https://developers.google.com/appengine/)  
    3. 修改local目录下proxy.ini中的age下的appid=你的appid（多个用|隔开）  
    4. window用户双击server目录下的uploader.bat进行上传配置。linux用户则在该文件顶层目录下执行以下命令：`cd server && python uploader.zip`进行配置  
    5. Chrome用户[请安装SwitchySharp](https://chrome.google.com/webstore/detail/dpplabbmogkhghncfbfdeeokoefdjegm)并将local目录下的SwitchyOptions.bak文件导入，同时导入证书CA.crt(位于local文件夹下)  

### 使用Goagent 
    1. 进入goagent中的local目录下运行proxy.py:`python proxy.py`  
    2. 设置SwitchyOptions模式为goagent即可

### 图文教程
- [图文教程](https://code.google.com/p/goagent/wiki/InstallGuide)

### 参考材料
- [goagent官网](https://code.google.com/p/goagent/)其他浏览器也能在其中找到合适的设置方法此处不再累赘。
