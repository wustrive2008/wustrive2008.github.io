title: centos搭建本地yum服务器
date: 2015-7-26 22:02:51
categories: Linux
tags: [Linux,yum,Centos,软件]
---


YUM是Yellow dog Updater, Modified的缩写，是由Duke University所发起的计划，目的就是为了解决RPM的依赖关系的问题，方便使用者进行软件的安装、升级等等工作。在此在特别说明的是，YUM只是为了解决RPM的依赖关系的问题，而不是一种其它的软件安装模式。

虽然在centos中可以直接使用yum源，但是也存在着与外网隔绝的现状，因此需要搭建本地yum源  
  
系统：centos 6.5  
  
1、将光盘挂载到/mnt下面  
```
# mount /dev/cdrom /mnt
```
  
  
2、删除系统自带的repo文件  
```
# cd /etc/yum.repos.d/  
# mkdir bak  
# mv *.repo bak  
```
  
3、安装createrepo（其实只要安装createrepo即可以，但是需要其他依赖包，所以也得一起装上）  
```
# cd /mnt/Packages/  
# rpm -ivh libxml2-python-2.7.6-14.el6.x86_64.rpm   
# rpm -ivh deltarpm-3.5-0.5.20090913git.el6.x86_64.rpm   
# rpm -ivh python-deltarpm-3.5-0.5.20090913git.el6.x86_64.rpm   
# rpm -ivh createrepo-0.9.9-18.el6.noarch.rpm  
```
  
4、使用createrepo命令创建yum仓库，创建索引信息  
注：这里我们把yum仓库建立在/doiido/local  
```
# mkdir /doiido/local  
# cp -rv /mnt/Packages/* /doiido/local  
# cd /doiido/local  
# createrepo -v /doiido/local
```

5、如若需要分组信息  
```
# cp /mnt/repodata/b4e0b9342ef85d3059ff095fa7f140f654c2cb492837de689a58c581207d9632-c6-x86_64-comps.xml /doiido/local/comps.xml  
# createrepo -g /doiido/local/comps.xml /doiido/local
```
  
6、编辑自己的repo文件  
```
# vi /etc/yum.repos.d/local.repo  
#库名称  
[local_server]  
  
#名称描述  
name=Thisis a local repo  
  
#yum源目录  
baseurl=file:///doiido/local  
  
#是否启用该yum源，0为禁用  
enabled=1  
  
#检查GPG-KEY（0为不检查，1为检查）  
gpgcheck=0  
```


7、加载yum缓存  
```
# yum clean all  
# yum makecache  
```
  
  
8、测试下载  
``` 
# yum -y install php 
```
  
这里就可以看到可以直接安装php，而不需要通过网上去下载。
