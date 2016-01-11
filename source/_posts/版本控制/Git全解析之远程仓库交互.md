title: Git全解析之远程仓库交互
date: 2016-1-11 22:02:51
categories: 版本控制
tags: [git,版本控制,项目管理]
photos:
- http://7xifb5.com1.z0.glb.clouddn.com/wustrive-hexogit%E7%9F%A5%E8%AF%86%E7%BB%93%E6%9E%84.png
---

## Git全解析之远程仓库交互

### 中央仓库的概念
虽然说git是分布式版本控制工具，但同样有远程仓库的概念。出于各种目的，我们有时需要有一个共享的远程仓库，如GitHub上的仓库，我们公司项目中用来测试和部署的仓库等。
一般的操作步骤是先在公共服务器上创建一个仓库，然后每个开发人员都clone这个仓库进行各自的开发，开发完成后再push到远程仓库进行测试部署。

从远程仓库clone项目：
```bash
[centos@bogon gitwork]$ 
[centos@bogon gitwork]$ git clone git@github.com:wustrive2008/gittest.git
Initialized empty Git repository in /home/centos/gitwork/gittest/.git/
remote: Counting objects: 4, done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 4 (delta 0), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (4/4), 4.15 KiB, done.
```

上面示例的远程仓库在github上，当然如果我们做的是公司的项目，出于安全性和访问效率的考虑很少会将公司的私有项目放到github上。一般的做法是搭建自己的git服务器，这里推荐两种git服务器工具：
1. [Gitolite](http://git-scm.com/book/zh/v1/%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git-Gitolite)
2. [GitLab](https://about.gitlab.com/)

这两个工具网上都有很多教程。

说明：github也有付费的企业版，试用过，也很好用

### 本地分支与远程分支
平时我们使用 git branch 查看的都是本地分支,如：
```bash
[centos@bogon gittest]$ git branch
* master
```

但是当需要经常与中央仓库同步代码时，特别是分支很多时，需要查看本地当前有哪些远程分支的引用，比便于在不同的分支之间进行切换开发。关于git分支的较好实践可以参考：[git flow](https://github.com/nvie/gitflow)
查看远程分支的引用
```bash
[centos@bogon gittest]$ 
[centos@bogon gittest]$ git branch -a
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/master
```
上面的结果表示：
当前本地有一个master分支，并且有一个remotes/origin/master(远程master)分支的引用，至于remotes/origin/HEAD分支可以理解为origin/master分支的一个引用。
其中origin为远程仓库引用的别名，此名称可以修改，也可以有多个，可以参考git remote命令了解更多

接下来在本地创建一个develop分支，用于正常的开发流程：
```bash
[centos@bogon gittest]$ git checkout -b develop
Switched to a new branch 'develop'
[centos@bogon gittest]$ git br
* develop
  master
[centos@bogon gittest]$ git br -a
* develop
  master
  remotes/origin/HEAD -> origin/master
  remotes/origin/master
```

在develop分支上进行开发，并提交：
```bash
[centos@bogon gittest]$ touch file1.txt
[centos@bogon gittest]$ git add .
[centos@bogon gittest]$ git commit -am "create file file1.txt"
[develop 21053d7] create file file1.txt
 0 files changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 file1.txt
```

开发完成后将develop分支推送到服务器：
```bash
[centos@bogon gittest]$ git push origin 
Counting objects: 4, done.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 311 bytes, done.
Total 3 (delta 0), reused 0 (delta 0)
To git@github.com:wustrive2008/gittest.git
   8a9a114..21053d7  develop -> develop
```

接下来正常的流程是测试人员拉取到远程的develop分支，然后对develop分支上提交的内容进行测试，测试通过后，合并到master分支，最后推送到部署服务器进行上线部署。

```bash
[centos@bogon gittest]$ git checkout master
Switched to branch 'master'
[centos@bogon gittest]$ git merge develop
Updating 8a9a114..21053d7
Fast-forward
 0 files changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 file1.txt
[centos@bogon gittest]$ git push origin master
Total 0 (delta 0), reused 0 (delta 0)
To git@github.com:wustrive2008/gittest.git
   8a9a114..21053d7  master -> master
```

### pull与fetch
如果要拉取远程仓库的代码，需要用到pull与fetch命令
这两个命令的区别是pull=fetch+merge

先来演示一下git pull，有其他开发者已经在develop分支上提交了新的内容，现在需要同步到本地
```bash
[centos@bogon gittest]$ git br
* develop
  master
[centos@bogon gittest]$ git log
commit 21053d768d7af0c5cf90f63dc105891726094b43
Author: wubaoguo <wustrive_2008@126.com>
Date:   Mon Jan 11 22:35:50 2016 +0800

    create file file1.txt

commit 8a9a114ecbacfd5555ee417ab1dbe02a20db9a03
Author: wubaoguo <wustrive2008@users.noreply.github.com>
Date:   Mon Jan 11 22:08:39 2016 +0800

    Initial commit
[centos@bogon gittest]$ git pull origin develop
remote: Counting objects: 2, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 2 (delta 1), reused 1 (delta 0), pack-reused 0
Unpacking objects: 100% (2/2), done.
From github.com:wustrive2008/gittest
 * branch            develop    -> FETCH_HEAD
Updating 21053d7..2296978
Fast-forward
 0 files changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 file2.txt
[centos@bogon gittest]$ git log
commit 22969782f467cd04410c9ed3cf5c80e3987d212b
Author: wubaoguo <wustrive_2008@126.com>
Date:   Mon Jan 11 22:52:18 2016 +0800

    create file file2.txt

commit 21053d768d7af0c5cf90f63dc105891726094b43
Author: wubaoguo <wustrive_2008@126.com>
Date:   Mon Jan 11 22:35:50 2016 +0800

    create file file1.txt

commit 8a9a114ecbacfd5555ee417ab1dbe02a20db9a03
Author: wubaoguo <wustrive2008@users.noreply.github.com>
Date:   Mon Jan 11 22:08:39 2016 +0800

    Initial commit
```
很清楚的看到，新的代码已经拉取并合并到本地了，可以基于最新的代码进行开发了。

如果开发者B说他向远程仓库推送了新的分支fixbug，需要你在此基础上继续修改bug，可以这样做
```bash
[centos@bogon gittest]$ git br -a     #可以看到这时本地并没有新的分支与引用
* develop
  master
  remotes/origin/HEAD -> origin/master
  remotes/origin/develop
  remotes/origin/master
[centos@bogon gittest]$ git fetch    #拉取远程所有的变动
remote: Counting objects: 3, done.
remote: Compressing objects: 100% (1/1), done.
remote: Total 3 (delta 1), reused 3 (delta 1), pack-reused 0
Unpacking objects: 100% (3/3), done.
From github.com:wustrive2008/gittest
   21053d7..2296978  develop    -> origin/develop
 * [new branch]      fixbug     -> origin/fixbug   #注意这里拉取到一个新分支
[centos@bogon gittest]$ git br  #这时本地并没有fixbug分支
* develop
  master
[centos@bogon gittest]$ git checkout fixbug  #创建并切换到fixbug分支,引用origin/fixbug分支
Branch fixbug set up to track remote branch fixbug from origin.
Switched to a new branch 'fixbug'
```

与远程仓库的交互操作基本就这些，还有一点要注意，推送之前最好先拉取一下，因为如果远程分支版本比本地新，直接推送会失败。

### 关于捐赠
如何觉得本文章对你有帮助，欢迎通过支付宝捐赠来支持博主

![捐赠](http://7xifb5.com1.z0.glb.clouddn.com/wustrive-hexo%E6%94%AF%E4%BB%98%E5%AE%9D%E4%BB%98%E6%AC%BE%E4%BA%8C%E7%BB%B4%E7%A0%81%E6%9C%80%E5%B0%8F.png)






