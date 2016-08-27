title: nginx平滑升级方案
date: 2015-8-28 20:50:31
categories: nginx
tags: [nginx,linux,升级]
---

#### 下载版本
下载新版本的nginx源码包
```
http://nginx.org/en/download.html

```

#### 备份配置 
备份旧nginx二进制文件和配置文件
```
cp /usr/local/nginx/sbin/nginx /usr/local/nginx/sbin/nginx_old
cp /usr/local/nginx/conf/nginx.conf /usr/local/nginx/conf/nginx.conf.old

```

#### 配置安装
解压下载的源码包， 执行configure选项配置
```
./configure --with-http_ssl_module --with-openssl=/path/to/openssl_src

make&make install

```
可以执行/usr/local/nginx/sbin/nginx -V命令查看以前的编译参数。

#### 新旧进程同时运行
向旧的nginx主进程发送USR2信号，这会启动新的nginx主进程而保留旧的nginx主进程。
```
kill -USR2 `cat /usr/local/nginx/logs/nginx.pid`
```

#### 杀掉旧的子进程
发送WINCH信号到旧的nginx主进程以杀掉旧的nginx子进程。
```
kill -WINCH pid

```

#### 检测访问正常，退出旧的进程
```
kill -QUIT pid

```
