--
layout: post
title: Git 常用资源
category: 资源
tags: Git
keywords: Git
description: 
---

## Git常用操作

### 打标签

### 初始化个人信息并设置账户信息保存时间
    $ git config --global user.name "Your Name Here"
    $ git config --global user.email "your_email@example.com"
    $ git config --global credential.helper cach
    $ git config --global credential.helper 'cache --timeout=3600'

### 添加文件
    git add <filename>

### 回滚操作
    reset --hard v0.1
    reflog
    reset --hard v0.2

### 取消某个文件的修改
    git checkout -- <filename>

### 删除文件
    git rm <filename>   直接删除文件
    git rm --cached <filename>    删除文件暂存状态

### 移动文件
    git mv <sourcefile> <destfile>

### 查看文件更新
    git diff              查看未暂存的文件更新 
    git diff --cached     查看已暂存文件的更新 

### 远程仓库相关命令
    git clone <path> 检出仓库
    git remote -v 查看远程仓库
    git pull 本地与服务器端同步
    git remote add <name> <url> 添加远程仓库
    git remote rm <name> 删除远程仓库
    git remote set-url --push <name> <newurl> 修改远程仓库
    git pull <remoteName> <localBranchName> 拉取远程仓库
    git push <remoteName> <localBranchName> 推送远程仓库
    git branch -r
    git checkout origin/android 切换分支

### 分支操作相关命令
    git branch 查看本地分支
    git branch -r 查看远程分支
    git branch <name> 创建本地分支
    git branche -d <name> 删除本地分支
    git checkout <name> 切换分支
    git checkout -b <name> 创建本地分支并立即切换到新分支
    git merge <name> 合并分支，将<name>分支与当前分支合并
    git push origin <name> 创建远程分支
    git push origin :<name> 删除远程分支

### 版本(tag)操作相关命令
    git tag 查看版本
    git tag <name> 创建版本
    git tag -d <name> 删除版本
    git tag -r 查看远程版本
    git push origin <name> 创建远程版本
    git push origin :/refs/tags/<name> 删除远程版本
    git pull origin --tags 合并远程tag到本地
    git push origin --tags 上传本地tag到远程仓库
    git tag -a <name> -m 'yourMessage' 创建带注释的tag

### 子模块(submodule)相关操作命令
    git submodule add <url> <path>
    git submodule init
    git submodule update
    删除子模块分四步走
    1.git rm --cached <path>
    2.编辑“.gitmudules”文件，将子模块的相关配置节点删掉
    3.编辑“.git/config”文件，将子模块的相关配置节点删掉
    4.手动删除子模块残留的目录

### 忽略一些文件、文件夹不提交
    在仓库根目录下创建名称为“.gitignore”文件，写入不许要得文件夹或者文件名，每个元素占据一行
