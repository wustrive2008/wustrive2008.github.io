title: Docker安装JavaWeb(Tomcat)运行环境
date: 2015-7-26 21:43:05
categories: Linux
tags: [Docker,Tomcat,Java,Linux,虚拟机]
---

docker 安装条件：
1. 开通cpu虚拟化技术 BIOS设置
2. 宿主主机要求64位操作系统

测试环境：ubuntu 14.04 64bit

### 安装docker

1. 安装最新版本的docker

    `wget -qO- https://get.docker.com/ | sh`

2. 查看版本

    `doceker vresion`

3. 运行测试

    `docker run hello-world`


### 去除掉sudo 
> 在Ubuntu下，在执行Docker时，每次都要输入sudo，同时输入密码，很累人的，这里微调一下，把当前用户执行权限添加到相应的docker用户组里面。

**步骤**

```
# 添加一个新的docker用户组
sudo groupadd docker
# 添加当前用户到docker用户组里，注意这里的yongboy为ubuntu server登录用户名
sudo gpasswd -a yongboy docker
# 重启Docker后台监护进程
sudo service docker restart
# 重启之后，尝试一下，是否生效
docker version
#若还未生效，则系统重启，则生效
sudo reboot
```

### 安装docker镜像

1. 安装镜像

    `docker pull learn/tutorial`

2. 测试效果

    `docker run learn/tutorial /bin/echo hello world`

3. 进入虚拟机

    `docker run -i -t learn/tutorial /bin/bash`

### 虚拟机环境部署

1. 安装ssh服务

    ```
    apt-get update
    apt-get install openssh-server
    which sshd
    /usr/sbin/sshd
    mkdir /var/run/sshd
    passwd #输入用户密码，我这里设置为123456，便于SSH客户端登陆使用
    exit #退出

    ```

2. 获取到刚才操作的实例容器ID

    ```
    #docker ps -l
    CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES
    b5b8dd5e603e learn/tutorial:latest /bin/bash 3 minutes ago Exit 0 thirsty_pasteur
    ```

3. 状态保存

    `docker commit b5b8dd5e603e learn/tutorial`

4. 以后台进程方式长期运行此镜像实例
    
    `docker run -d -p 22 -p 800:8080 learn/tutorial /usr/sbin/sshd -D`

5. 查看实例运行状态

    ```
    wustrive@ubuntu:~$ sudo docker ps
    CONTAINER ID        IMAGE               COMMAND               CREATED             STATUS              PORTS                                          NAMES
    b5b8dd5e603e        learn/tutorial      "/usr/sbin/sshd -D"   31 minutes ago      Up 31 minutes       0.0.0.0:32768->22/tcp, 0.0.0.0:800->8080/tcp   elegant_davinci

    ```
6. ssh登录镜像

    `ssh root@127.0.0.1 -p 32768`

7. 安装/运行javaweb部署运行环境

    ```
    # 在ubuntu 12.04上安装oracle jdk 7
    apt-get install python-software-properties
    add-apt-repository ppa:webupd8team/java
    apt-get update
    apt-get install -y wget
    apt-get install oracle-java7-installer
    java -version
    # 下载tomcat 7.0.47
    wget http://mirrors.hust.edu.cn/apache/tomcat/tomcat-7/v7.0.63/bin/apache-tomcat-7.0.63.tar.gz
    # 解压，运行
    tar xvf apache-tomcat-7.0.47.tar.gz
    cd apache-tomcat-7.0.47
    bin/startup.sh
    ```

8. 访问服务

    访问宿主主机800端口

    如：`curl http://192.168.32.151:800`

9. 保存镜像

    `docker commit b5b8dd5e603e java/tomcat`

### docker常用命令

- docker version
 
    显示 Docker 版本信息。
- docker info  

    检查docker安装是否正确
- docker search

    从 Docker Hub 中搜索符合条件的镜像。

    --automated 只列出 automated build类型的镜像；

    --no-trunc 可显示完整的镜像描述；

    -s 40 列出收藏数不小于40的镜像。
- sudo docker pull centos:7  

    从 Docker Hub 中拉取或者更新指定镜像。

    -a 拉取所有 tagged 镜像 。
-  docker images

    列出本地所有镜像。其中 [name] 对镜像名称进行关键词查询。

    -a 列出所有镜像（含过程镜像）；

    -f 过滤镜像，如： -f ['dangling=true'] 只列出满足dangling=true 条件的镜像；

    --no-trunc 可显示完整的镜像ID；

    -q 仅列出镜像ID。

    --tree 以树状结构列出镜像的所有提交历史。
- docker run centos /bin/echo Hello Docker 
 
    运行镜像
- docker stop $sample_job
 
    停止名为sample_job的容器
- docker commit $sample_job job1
 
    容器状态保存为镜像
- docker ps

    列出所有运行中的容器

    -a 列出所有容器（含沉睡镜像）；

    --before="nginx" 列出在某一容器之前创建的容器，接受容器名称和ID作为参数；

    --since="nginx" 列出在某一容器之后创建的容器，接受容器名称和ID作为参数；

    -l 仅列出最新创建的一个容器；

    --no-trunc 显示完整的容器ID；

    -n=4 列出最近创建的4个容器；

    -q 仅列出容器ID；

    -s 显示容器大小。
- docker rmi

    从本地移除一个或多个指定的镜像。

    -f 强行移除该镜像，即使其正被使用

    --no-prune 不移除该镜像的过程镜像，默认移除\
- docker rm
 
    -f 强行移除该容器，即使其正在运行；

    -l 移除容器间的网络连接，而非容器本身；

    -v 移除与容器关联的空间。
- docker history

    查看指定镜像的创建历史。

    --no-trunc 显示完整的提交记录；

    -q 仅列出提交记录ID。
- docker start|stop|restart

    启动、停止和重启一个或多个指定容器。

    -a 待完成

    -i 启动一个容器并进入交互模式；

    -t 10 停止或者重启容器的超时时间（秒），超时后系统将杀死进程。
- docker kill

    杀死一个或多个指定容器进程。

    -s "KILL" 自定义发送至容器的信号。
- docker events

    从服务器拉取个人动态，可选择时间区间。
- docker save

    将指定镜像保存成 tar 归档文件， docker load 的逆操作。保存后再加载（saved-loaded）的镜像不会丢失提交历史和层，可以回滚。
- docker load

    从 tar 镜像归档中载入镜像， docker save 的逆操作。保存后再加载（saved-loaded）的镜像不会丢失提交历史和层，可以回滚。

    -i "debian.tar" 指定载入的镜像归档。
- docker export

    将指定的容器保存成 tar 归档文件， docker import 的逆操作。导出后导入（exported-imported)）的容器会丢失所有的提交历史，无法回滚。
- docker import

    从归档文件（支持远程文件）创建一个镜像， export 的逆操作，可为导入镜像打上标签。导出后导入（exported-imported)）的容器会丢失所有的提交历史，无法回滚。
- docker top

    查看一个正在运行容器进程，支持 ps 命令参数。
- docker inspect

    检查镜像或者容器的参数，默认返回 JSON 格式。

    -f 指定返回值的模板文件。
- docker pause

    暂停某一容器的所有进程。
- docker unpause

    恢复某一容器的所有进程。
- docker tag

    标记本地镜像，将其归入某一仓库。

    -f 覆盖已有标记。
- docker push

    将镜像推送至远程仓库，默认为 Docker Hub 。
- docker logs

    获取容器运行时的输出日志。

    -f 跟踪容器日志的最近更新；

    -t 显示容器日志的时间戳；

    --tail="10" 仅列出最新10条容器日志。
- docker run

    启动一个容器，在其中运行指定命令。

    -a stdin 指定标准输入输出内容类型，可选 STDIN/STDOUT/STDERR 三项；

    -d 后台运行容器，并返回容器ID；

    -i 以交互模式运行容器，通常与 -t 同时使用；

    -t 为容器重新分配一个伪输入终端，通常与 -i 同时使用；

    --name="nginx-lb" 为容器指定一个名称；

    --dns 8.8.8.8 指定容器使用的DNS服务器，默认和宿主一致；

    --dns-search example.com 指定容器DNS搜索域名，默认和宿主一致；

    -h "mars" 指定容器的hostname；

    -e username="ritchie" 设置环境变量；

    --env-file=[] 从指定文件读入环境变量；

    --cpuset="0-2" or --cpuset="0,1,2" 绑定容器到指定CPU运行；

    -c 待完成

    -m 待完成

    ---net="bridge" 指定容器的网络连接类型

    --link=[] 待完成

    --expose=[] 待完成


### 常见问题

1. 执行docker命令提示：

    `Get http:///var/run/docker.sock/v1.19/containers/json: dial unix /var/run/docker.sock: no such file or directory. Are you trying to connect to a TLS-enabled daemon without TLS?`


**解决办法:**

```
    sudo apt-get install apparmor

    service docker restart
```

2. 挂载宿主主机目录

```
     docker run -it -v /home/dock/Downloads:/usr/Downloads ubuntu64 /bin/bash
```










