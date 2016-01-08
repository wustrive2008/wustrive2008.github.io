title: Git全解析之用起来先
date: 2016-1-6 22:23:16
categories: 版本控制
tags: [git,版本控制,项目管理]
photos:
- http://7xifb5.com1.z0.glb.clouddn.com/wustrive-hexogit%E7%9F%A5%E8%AF%86%E7%BB%93%E6%9E%84.png
---
## Git全解析之用起来先

### 先安装Git环境
下载安装：http://git-scm.com/download/

### 配置

配置user与email,用来标识用户
```bash
$ git config --global user.name "wustrive"
$ git config --global user.email "wustrive2008@gmail.com"
```
也可以直接编辑配置文件，配置文件默认路径在用户目录下的.gitconfig文件中，结构是：
```bash
[user]
    name = wustrive
    email = wustrive2008@gmail.com
```

### 简单了解Git
Git是一个快速的分布式版本控制系统
#### Git对象模型 SHA
所有用来表示项目历史信息的文件是通过一个40个字符“对象名”来索引的。每一个“对象名”都是对“对象”内容做SHAI哈希计算来的。这个对象名是全局唯一的，不同的对象生成的“对象名”不同。

Git中四种类型的对象："blob","tree","commit"和"tag"。每个对象包括三个部分：类型，大小和内容。
- blob用来存储文件数据
- tree有点像一个目录，用来管理一些blob与tree
- commit，一个commit只指向一个tree，用来标记项目某一个特定时间点的状态，即一次提交
- tag，一个tag用来标记一个commit

#### Git目录与工作目录
git目录是为你的项目存储所有历史和元信息的目录，包括所有对象，这些对象指向不同的分支，每个项目只能有一个Git目录,这个叫'.git'的目录一般在项目的根目录下，
这个目录下的重要文件有：
```bash
.
|-- FETCH_HEAD #指向着目前已经从远程仓库取下来的分支的末端版本。
|-- HEAD #这个git项目当前处在哪个分支里
|-- ORIG_HEAD #HEAD指针的前一个状态
|-- branches/ #项目的所有分支
|-- config/ #项目的配置信息，git config命令会改动它
|-- description #项目的描述信息
|-- hooks/ #系统默认钩子脚本目录
|-- index #索引文件
|-- info/ #包含仓库的一些信息
|-- logs/ #各个refs的历史信息
|-- objects/ #Git本地仓库的所有对象 (commits, trees, blobs, tags)
|-- packed-refs #运行 git gc, refs 下的所有文件都会消失。Git 会将这些文件挪到 .git/packed-refs 文件中去以提高效率
|-- refs/ #标识你项目里的每个分支指向了哪个提交(commit)
```

工作目录就是你的项目源代码目录，即是你签出(checkout)用来编辑的文件，当在不同的分支间切换时，工作目录里的内容会随之替换或删除，所有的操作历史都保存在Git目录中，工作目录是用来临时保存checkout文件的地方。

### 可以开始使用了

#### 获取仓库
- 创建仓库，在工作目录下执行
```bash
git init .
```

- clone一个仓库
```bash
#通过http(s)协议
git clone https://github.com/git/git.git
#通过ssh协议
git clone git@github.com:git/git.git
```

**提示：创建和clone后默认的分支是master，默认的repository引用名称origin**


#### 正常的工作流程
1. 修改文件，将它们更新的内容添加到索引中
```bash
git add file1 file2 file3
#也可以通过git add . 来添加所有变动到暂存区
git add .
```

2. 查看当状态
```bash
$ git status
# On branch master
# Changes to be committed:
# (use "git reset HEAD <file>..." to unstage)
#
# modified: file1
# modified: file2
# modified: file3
#
```

3. 提交commit
```bash
#执行后会进入编辑器进行注释编辑
$ git commit

#如果注释很短也可以使用
$ git commit -m "注释内容"

#如果只是修改了文件，没有添加新的文件，可以省略git add
$ git commit -am "注释内容"
```


#### 分支与合并

##### 基本操作命令

```bash
#查看本地分支 *代表当前所在分支
[centos@bogon gittest]$ git branch
* master

#新建分支
[centos@bogon gittest]$ git branch br1
[centos@bogon gittest]$ git branch
  br1
* master

#切换分支
[centos@bogon gittest]$ git checkout br1
Switched to branch 'br1'
[centos@bogon gittest]$ git branch
* br1
  master

#切换并合并分支，以当前分支为基础新建分支
[centos@bogon gittest]$ ll
total 0
-rw-rw-r--. 1 centos centos 0 Jan  6 23:32 file1
-rw-rw-r--. 1 centos centos 0 Jan  6 23:32 file2
-rw-rw-r--. 1 centos centos 0 Jan  6 23:32 file3
[centos@bogon gittest]$ git branch
* br1
  master
[centos@bogon gittest]$ git checkout -b br2
Switched to a new branch 'br2'
[centos@bogon gittest]$ ll
total 0
-rw-rw-r--. 1 centos centos 0 Jan  6 23:32 file1
-rw-rw-r--. 1 centos centos 0 Jan  6 23:32 file2
-rw-rw-r--. 1 centos centos 0 Jan  6 23:32 file3
[centos@bogon gittest]$ git branch
  br1
* br2
  master

#合并分支，将其他分支合并到当前分支
[centos@bogon gittest]$ ll
total 0
-rw-rw-r--. 1 centos centos 0 Jan  6 23:32 file1
-rw-rw-r--. 1 centos centos 0 Jan  6 23:32 file2
-rw-rw-r--. 1 centos centos 0 Jan  6 23:32 file3
[centos@bogon gittest]$ git merge br2
Updating da5068b..f9da174
Fast-forward
 br2-file |    1 +
 1 files changed, 1 insertions(+), 0 deletions(-)
 create mode 100644 br2-file
[centos@bogon gittest]$ ll
total 4
-rw-rw-r--. 1 centos centos 10 Jan  6 23:44 br2-file
-rw-rw-r--. 1 centos centos  0 Jan  6 23:32 file1
-rw-rw-r--. 1 centos centos  0 Jan  6 23:32 file2
-rw-rw-r--. 1 centos centos  0 Jan  6 23:32 file3   

#删除分支，删除已经被合并过的分支，安全删除分支
[centos@bogon gittest]$ git branch
  br1
  br2
* master
[centos@bogon gittest]$ git branch -d br2
Deleted branch br2 (was f9da174).
[centos@bogon gittest]$ git branch
  br1
* master

#强制删除分支
[centos@bogon gittest]$ git branch
  br1
* master
[centos@bogon gittest]$ git branch -D br1
Deleted branch br1 (was da5068b).
[centos@bogon gittest]$ git branch
* master

```

##### 分支冲突
如果执行自动合并没有成功的话，git会在索引和工作树里设置一个特殊的状态， 提示你如何解决合并中出现的冲突。
```bash
[centos@bogon gittest]$ git merge br1
Auto-merging file1
CONFLICT (content): Merge conflict in file1
Automatic merge failed; fix conflicts and then commit the result.
[centos@bogon gittest]$ git status
# On branch master
# Unmerged paths:
#   (use "git add/rm <file>..." as appropriate to mark resolution)
#
#   both modified:      file1
#
no changes added to commit (use "git add" and/or "git commit -a")
```
有冲突(conflicts)的文件会保存在索引中，在commit之前要解决冲突，解决冲突的方式就是编辑冲突文件，重新commit。

##### 撤销合并
```bash
#如果合并分支后又后悔了，可以撤销合并
[centos@bogon gittest]$ git reset --hard HEAD
HEAD is now at 2fa716d file1 master
[centos@bogon gittest]$ git status
# On branch master
nothing to commit (working directory clean)

#如果已经把合并后的代码提交了，可以执行
[centos@bogon gittest]$ git reset --hard ORIG_HEAD
HEAD is now at 2fa716d file1 master
```

#### log记录
```bash
$ git log v2.5.. # commits since (not reachable from) v2.5
$ git log test..master # commits reachable from master but not test
$ git log master..test # commits reachable from test but not master
$ git log master...test # commits reachable from either test or
# master, but not both
$ git log --since="2 weeks ago" # commits from the last 2 weeks
$ git log Makefile # commits that modify Makefile
$ git log fs/ # commits that modify any file under fs/
$ git log -S'foo()\' # commits that add or remove any file data
# matching the string 'foo()'
$ git log --no-merges # dont show merge commits

```
示例：
```
#查看当前分支log
[centos@bogon gittest]$ git log
commit 2fa716df1d841ac2347cd9b6d371cfdf71682dfe
Author: wubaoguo <wustrive_2008@126.com>
Date:   Wed Jan 6 23:53:20 2016 +0800

    file1 master

commit f9da1748bddb6cfcc0f492f60328abcd54f97663
Author: wubaoguo <wustrive_2008@126.com>
Date:   Wed Jan 6 23:44:08 2016 +0800

    br2

commit da5068b35246dc26b77105a6dc6c2aa6e430fcad
Author: wubaoguo <wustrive_2008@126.com>
Date:   Wed Jan 6 23:38:39 2016 +0800

    init

#查看详细变动
[centos@bogon gittest]$ git log --stat
commit 2fa716df1d841ac2347cd9b6d371cfdf71682dfe
Author: wubaoguo <wustrive_2008@126.com>
Date:   Wed Jan 6 23:53:20 2016 +0800

    file1 master

 file1 |    1 +
 1 files changed, 1 insertions(+), 0 deletions(-)

commit f9da1748bddb6cfcc0f492f60328abcd54f97663
Author: wubaoguo <wustrive_2008@126.com>
Date:   Wed Jan 6 23:44:08 2016 +0800

    br2

 br2-file |    1 +
 1 files changed, 1 insertions(+), 0 deletions(-)

commit da5068b35246dc26b77105a6dc6c2aa6e430fcad
Author: wubaoguo <wustrive_2008@126.com>
Date:   Wed Jan 6 23:38:39 2016 +0800

    init


#格式化log输出结果
[centos@bogon gittest]$ git log --pretty=oneline
2fa716df1d841ac2347cd9b6d371cfdf71682dfe file1 master
f9da1748bddb6cfcc0f492f60328abcd54f97663 br2
da5068b35246dc26b77105a6dc6c2aa6e430fcad init

#更友好的格式化，历史多了效果明显
[centos@bogon gittest]$ git log --pretty=format:'%h : %s' --graph
* 2fa716d : file1 master
* f9da174 : br2
* da5068b : init
```

#### 比较差异DIFF
```bash
#比较分支间的差异
[centos@bogon gittest]$ git diff master..br1
diff --git a/file1 b/file1
index 3325d54..76e65f5 100644
--- a/file1
+++ b/file1
@@ -1 +1 @@
-file1 master
+file1 br1

#工作目录与暂存区(staged)差异
[centos@bogon gittest]$ git diff
diff --git a/file2 b/file2
index e69de29..35d5537 100644
--- a/file2
+++ b/file2
@@ -0,0 +1,2 @@
+diff 的使用
+

#暂存区与上次提交之间的差异
[centos@bogon gittest]$ git add .
[centos@bogon gittest]$ git diff --cached
diff --git a/file2 b/file2
index e69de29..35d5537 100644
--- a/file2
+++ b/file2
@@ -0,0 +1,2 @@
+diff 的使用
+

#工作目录与上次提交之间的差异
[centos@bogon gittest]$ git diff HEAD
diff --git a/file2 b/file2
index e69de29..35d5537 100644
--- a/file2
+++ b/file2
@@ -0,0 +1,2 @@
+diff 的使用
+
diff --git a/file3 b/file3
index e69de29..1bf6afb 100644
--- a/file3
+++ b/file3
@@ -0,0 +1 @@
+还没commit  工作目录又改动了

```


### 附录 .gitconfig文件配置参考
```bash
[user]
    name = zhangsan
    email = zhangsan@gmail.com
[color]
    branch = auto
    diff = auto
    status = auto
    ui = auto

[core]
    quotepath=false
    edit = vim
    autocrlf = true
    filemode = false
[i18n]
    commitencoding = UTF-8
[gui]
    encoding = utf-8
[alias]
    stage = add
    unstage = reset HEAD
    hb = merge --no-ff
    rmv = remote -v
    ci = commit
    cia = commit --amend
    co = checkout
    br = branch
    st = status
    dc = diff --cached
    dw = diff --word-diff
    aa = add -A
    rmall = !git ls-files --deleted | xargs git rm
    ll = log --pretty=format:"%C(yellow)%h%Cred%d%Creset\\ %cn\\ %Cblue%cr%Creset\\ %Cgreen%s%Creset" --decorate --numstat
    lg = log --pretty=format:"%C(yellow)%h%Cred%d%Creset\\ %cn\\ %Cblue%cr%Creset\\ %Cgreen%s%Creset" --decorate
    lt = log --pretty=format:"%C(yellow)%h%Cred%d%Creset\\ %cn\\ %Cblue%cr%Creset\\ %Cgreen%s%Creset" --graph
[receive]
    denyCurrentBranch = ignore
```

更多配置文件参考: https://github.com/wustrive2008/conf-file

### 参考
《Git Community Book》


### 捐赠
如何觉得本文章对你有帮助，欢迎捐赠

![捐赠](http://7xifb5.com1.z0.glb.clouddn.com/wustrive-hexo%E6%94%AF%E4%BB%98%E5%AE%9D%E4%BB%98%E6%AC%BE%E4%BA%8C%E7%BB%B4%E7%A0%81%E6%9C%80%E5%B0%8F.png)

