---
title: "Git Submodule使用完整教程"
date: 2017-12-30T03:11:24+08:00
draft: false
---

自从看了蒋鑫的《Git权威指南》之后就开始使用Git Submodule功能，团队也都熟悉了怎么使用，多个子系统（模块）都能及时更新到最新的公共资源，把使用的过程以及经验和容易遇到的问题分享给大家。

`Git Submodule`功能刚刚开始学习可能觉得有点怪异，所以本教程把每一步的操作的命令和结果都用代码的形式展现给大家，以便更好的理解。

<!--more-->

## 1.对于公共资源各种程序员的处理方式
每个公司的系统都会有一套统一的系统风格，或者针对某一个大客户的多个系统风格保持统一，而且如果风格改动后要同步到多个系统中；这样的需求几乎每个开发人员都遇到，下面看看各个层次的程序员怎么处理：

假如对于系统的风格需要几个目录：css、images、js。

* 普通程序员，把最新版本的代码逐个复制到每个项目中，如果有N个项目，那就是要复制N x 3次；如果漏掉了某个文件夹没有复制…。

* 文艺程序员，使用Git Submodule功能，执行：`git submodule update`，然后冲一杯咖啡悠哉的享受着。

引用一段《Git权威指南》的话： 项目的版本库在某些情况虾需要引用其他版本库中的文件，例如公司积累了一套常用的函数库，被多个项目调用，显然这个函数库的代码不能直接放到某个项目的代码中，而是要独立为一个代码库，那么其他项目要调用公共函数库该如何处理呢？分别把公共函数库的文件拷贝到各自的项目中会造成冗余，丢弃了公共函数库的维护历史，这显然不是好的方法。

## 2.开始学习Git Submodule
“工欲善其事，必先利其器”！

既然文艺程序员那么轻松就搞定了，那我们就把过程一一道来。

说明：本例采用两个项目以及两个公共类库演示对submodule的操作。因为在一写资料或者书上的例子都是一个项目对应1～N个lib，但是实际应用往往并不是这么简单。

### 2.1 创建Git Submodule测试项目

#### 2.1.1 准备环境

```bash
henryyan@hy-hp  ~  pwd
/home/henryyan
mkdir -p submd/repos
```
创建需要的本地仓库：

```bash
cd ~/submd/repos
git --git-dir=lib1.git init --bare
git --git-dir=lib2.git init --bare
git --git-dir=project1.git init --bare
git --git-dir=project2.git init --bare
```
初始化工作区：


```bash
mkdir ~/submd/ws
cd ~/submd/ws
```

#### 2.1.2 初始化项目

初始化project1：
```bash
➜ henryyan@hy-hp  ~/submd/ws  git clone ../repos/project1.git 
Cloning into project1...
done.
warning: You appear to have cloned an empty repository.
 
➜ henryyan@hy-hp  ~/submd/ws  cd project1
➜ henryyan@hy-hp  ~/submd/ws/project1 git:(master) echo "project1" > project-infos.txt
➜ henryyan@hy-hp  ~/submd/ws/project1 git:(master) ✗ ls
project-infos.txt
 
➜ henryyan@hy-hp  ~/submd/ws/project1 git:(master) ✗ git add project-infos.txt 
➜ henryyan@hy-hp  ~/submd/ws/project1 git:(master) ✗ git status
# On branch master
#
# Initial commit
#
# Changes to be committed:
#   (use "git rm --cached <file>..." to unstage)
#
#   new file:   project-infos.txt
#
➜ henryyan@hy-hp  ~/submd/ws/project1 git:(master) ✗ git commit -m "init project1"
[master (root-commit) 473a2e2] init project1
 1 files changed, 1 insertions(+), 0 deletions(-)
 create mode 100644 project-infos.txt
➜ henryyan@hy-hp  ~/submd/ws/project1 git:(master) git push origin master
Counting objects: 3, done.
Writing objects: 100% (3/3), 232 bytes, done.
Total 3 (delta 0), reused 0 (delta 0)
Unpacking objects: 100% (3/3), done.
To /home/henryyan/submd/ws/../repos/project1.git
 * [new branch]      master -> master
```
 
初始化project2：
```bash
➜ henryyan@hy-hp  ~/submd/ws/project1 cd ..
➜ henryyan@hy-hp  ~/submd/ws  git clone ../repos/project2.git 
Cloning into project2...
done.
warning: You appear to have cloned an empty repository.
 
➜ henryyan@hy-hp  ~/submd/ws  cd project2
➜ henryyan@hy-hp  ~/submd/ws/project2 git:(master) echo "project2" > project-infos.txt
➜ henryyan@hy-hp  ~/submd/ws/project2 git:(master) ✗ ls
project-infos.txt
 
➜ henryyan@hy-hp  ~/submd/ws/project2 git:(master) ✗ git add project-infos.txt 
➜ henryyan@hy-hp  ~/submd/ws/project2 git:(master) ✗ git status
# On branch master
#
# Initial commit
#
# Changes to be committed:
#   (use "git rm --cached <file>..." to unstage)
#
#   new file:   project-infos.txt
#
➜ henryyan@hy-hp  ~/submd/ws/project2 git:(master) ✗ git commit -m "init project2"
[master (root-commit) 473a2e2] init project2
 1 files changed, 1 insertions(+), 0 deletions(-)
 create mode 100644 project-infos.txt
➜ henryyan@hy-hp  ~/submd/ws/project2 git:(master) git push origin master
Counting objects: 3, done.
Writing objects: 100% (3/3), 232 bytes, done.
Total 3 (delta 0), reused 0 (delta 0)
Unpacking objects: 100% (3/3), done.
To /home/henryyan/submd/ws/../repos/project2.git
 * [new branch]      master -> master
```

