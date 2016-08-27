title: chkconfig构建linux服务
date: 2015-7-26 21:27:41
categories: Linux
tags: [Linux,服务器,chkconfig]
---

手动构建linux服务
---

### Linux运行级别

- 0 - 停机
- 1 - 单用户模式
- 2 - 多用户，没有NFS和网络
- 3 - 完全多用户
- 4 - 暂无
- 5 - 图形界面
- 6 - 重启

** 默认的运行级别在/etc/inittab中设置 **
 
### 写服务控制脚本

** 示例：**

```
    #!/bin/sh   
    #   
    # memcached:    MemCached Daemon   
    #   
    # chkconfig:    35 90 25  
    # description:  MemCached Daemon   
    #   
    # Source function library.   
    . /etc/rc.d/init.d/functions   
    . /etc/sysconfig/network   
    start()   
    {   
            echo -n $"Starting memcached: "  
            daemon $MEMCACHED -u lbt -d -m 1024 -l 192.168.32.193 -p 12000 
            echo   
    }   
    stop()   
    {   
            echo -n $"Shutting down memcached: "  
            killproc memcached   
            echo   
    }   
    MEMCACHED="/usr/local/bin/memcached"  
    [ -f $MEMCACHED ] || exit 1  
    # See how we were called.   
    case "$1" in   
      start)   
            start   
            ;;   
      stop)   
            stop   
            ;;   
      restart)   
            stop   
            sleep 3  
            start   
            ;;   
        *)   
            echo $"Usage: $0 {start|stop|restart}"  
            exit 1  
    esac   
    exit 0 

```

** 说明 **

```
    # chkconfig:    35 90 25     
    # description:  MemCached Daemon  
```
这两行必须，chkconfig 35 90 25  ,数字分别代表，启动级别，启动运行 ，关闭顺序

### 安装服务

1. 将脚本拷贝到/etc/init.d/目录
2. chkconfig add  servername(脚本名称)
3. chkconfig --level 35 servername on 
4.  chkconfig  --list | grep servername

### 使用服务

    service  servername  start|stop|restart
