---
title: Git --changes not staged for commit
date: 2019-07-10 00:55:53
tags:
	- Git
	- Git mistake
toc: true
---

### 出现的操作

> 正常 **git add .** ，然后**git commit**

> 后仍然报 changes not staged for commit 错误，并且上传到仓库的文件夹是空的

### 解决方案

1. **先强行删除clone来的目录下的 .git 文件夹**

>  删除方式：在该目录下打开命令行工具，执行 **rd/s/q .git**命令
>
> 删除成功后执行**ls .git**命令提示：No such file or directory

2. **回到仓库根目录删除仓库中的空文件夹**

> ​    **1）git rm -r --cached "themes/materail"**
>
> ​    **2）git commit -m "remove empty folder"**
>
> ​    **3）git push origin master**

3. **在仓库根目录重新提交代码**

> ​    **1）git add .**
>
> ​    **2）git commit -m "repush"**
>
> ​    **3）git push origin master**

> 这样就能保证不会报上面的错，并且删除了空文件夹，重新把clone下来的目录

### 原因

> clone 下来的文件夹也是一个 git 仓库，因此正常的 **git add .** 是无法提交该文件夹下的文件的，所以我们要做的就是删除文件夹下的 .git 文件夹，这样就可以通过  **git add .** 命令来提交内容