# GitHub教程

## 注意

教程使用的是windows系统

## 介绍

### 简介

> GitHub 一个代码托管平台

### 工作流程介绍

```mermaid
graph LR
    工作区 -.add.-> 暂存区 -.commit.-> 版本库
```

## 简单使用

### 1. 下载安装git

> github使用git进行代码的仓库的管理，http://git-scm.com/downloads

> 国内git官网镜像 https://registry.npmmirror.com/binary.html?path=git-for-windows/

### 2. 初始化一个本地的仓库

安装好后创建一个文件夹名为test

然后在文件夹里面书籍右键选择Git bash here在此打开git命令窗口

> 注意: Git GUI Here是图形操作模式，但是对比命令窗口并没提高多少效率，而且linux下大部分也是使用命令模式
>
> 所以本教程全程都是使用命令模式

> 在命令窗口中使用git init 就可以把当前文件夹初始化成一个本地的git仓库

``` 
Setruth@setruth MINGW64 ~/Desktop/test
$ git init
Initialized empty Git repository in C:/Users/Setruth/Desktop/test/.git/
```

> 这是文件夹中会出现一个.git目录，是默认隐藏的，这是用于管理和标识仓库的一个文件夹

### 3. 配置仓库用户信息

> 这是用于配置仓库的持有者的信息
>
> 使用--global 是配置全局的信息，也就是默认你所有的本地仓库持有者都是这个信息
>
> 配置单独的持有者信息只用去掉--global即可

> 全局的配置存在 C:\Users\用户名\.gitconfig中
>
> 局部的在当前仓库的.git/config文件中

``` 
$ git config --global user.name "xxxxx"
$ git config --global user.email xxxxx.com
```

>  查看配置的git信息
>
> ```
> git config --list
> ```

### 4. 添加文件到暂存区

> git add 文件
>
> > git add .  //添加当前目录下的所有文件到暂存区
> >
> > git add *.c //添加当前目录下的所有.c文件到暂存区
> >
> > git add text.c //添加当前目录下的text.c文件到暂存区

### 5. 查看git当前状态

> git status
>
> > 用于查看当前是否有修改了未添加到暂存区，获取在暂存区未提交的文件

### 6. 提交暂存区的内容

> git commit -m "xxxx"
>
> > 双引号中的是这次提交的说明，windows是双引号，linux下是单引号

### 结尾

> 至此，你已经会简单的创建提交管理你的git仓库了

## 常用操作

### 克隆

> git clone 项目地址
>
> > 把项目地址的git仓库复制到当前执行指令的文件夹中

