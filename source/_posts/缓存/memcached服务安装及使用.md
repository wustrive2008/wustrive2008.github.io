title: memcached高速缓存服务安装及使用
date: 2015-7-26 21:30:41
categories: 缓存
tags: [memcached,java,linux,缓存]
---

### 安装Memcached

#### request

  1. gcc

yum install gcc

  2. libevent

yum install libevent*

#### install
    
    
    ./configure --with-libevent=/usr
    make && sudo make install
    

#### start
    
    
    sudo /usr/local/bin/memcached -d -m 10 -u root -l 192.168.32.193 -p 12000 -c 256 -P /tmp/memcached.pid
    
-d选项是启动一个守护进程，
-m是分配给Memcache使用的内存数量，单位是MB，我这里是10MB，
-u是运行Memcache的用户，我这里是root，
-l是监听的服务器IP地址，如果有多个地址的话，我这里指定了服务器的IP地址192.168.0.200，
-p是设置Memcache监听的端口，我这里设置了12000，最好是1024以上的端口，
-c选项是最大运行的并发连接数，默认是1024，我这里设置了256，按照你服务器的负载量来设定，
-P是设置保存Memcache的pid文件，我这里是保存在 /tmp/memcached.pid
    

#### shutdown
    
    
    kill 'cat /tmp/memcached.pid'
    

#### test
    
    
    telnet 192.168.32.193 12000
    #基本协议
    set key1 0 180 3
    abc
    STORED
    add key1 0 180 3
    xyz
    NOT_STORED
    get key1
    VALUE key1 0 3
    abc
    END
    replace key1 0 180 3
    xyz
    STORED
    get key1
    VALUE key1 0 3
    xyz
    END
    delete key1
    DELETED
    

#### 自动启动

1. 启动脚本 存入/etc/init.d/memcached
```
#!/bin/sh   
#   
# memcached:    MemCached Daemon   
#   
# chkconfig:    - 90 25  
# description:  MemCached Daemon   
#   
# Source function library.   
. /etc/rc.d/init.d/functions   
. /etc/sysconfig/network   
#[ ${NETWORKING} = "no" ] && exit 0  
#[ -r /etc/sysconfig/dund ] || exit 0  
#. /etc/sysconfig/dund   
#[ -z "$DUNDARGS" ] && exit 0  
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

2. 执行以下命令   
```   
    chkconfig  --add memcached 
    chkconfig  --level 235  memcached  on

```


3. 测试使用

    ```     
        chkconfig  --list | grep mem

        /etc/rc.d/init.d/memcached  start  
        /etc/rc.d/init.d/memcached  stop
        /etc/rc.d/init.d/memcached  restart
        或
        service memcached start
        service memcached stop
        service memcached restart
    ``` 
