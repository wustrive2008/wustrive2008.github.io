title: CentOS6.5下samba服务器搭建与配置
date: 2015-7-26 21:52:19
categories: Linux
tags: [Linux,samba,Centos,文件共享]
---


###  一、samba服务器的安装与配置

1. samba服务器所需的软件包：

    [root@localhost ~]# yum -y install samba samba-common samba-client 

2. samba服务器的主配置文件

    [root@localhost ~]# vim /etc/samba/smb.conf 
    ```
    [global]

    # ----------------------- Network Related Options -------------------------

    workgroup = MYGROUP 工作组名称，可随便写

    server string = MY Samba Server Version %v 服务器描述，可随便写

    ; netbios name = MYSERVER

    ; interfaces = lo eth0 192.168.12.2/24 192.168.13.2/24 这两条相当于samba服务器特有 的防火墙机制

    ; hosts allow = 127. 192.168.12. 192.168.13.

    # --------------------------- Logging Options -----------------------------

    log file = /var/log/samba/log.%m samba的日志文件及命名格式

    # max 50KB per log file, then rotate

    max log size = 50 最大日志存储量

    # ----------------------- Standalone Server Options ------------------------

    security = user 访问samba的用户级别（share/user/domain）

    passdb backend = tdbsam

    # --------------------------- Printing Options -----------------------------

    load printers = no 是否启用打印机功能，默认为yes

    cups options = raw

    ; printcap name = /etc/printcap

    #obtain list of printers automatically on SystemV

    ; printcap name = lpstat

    ; printing = cups

    # --------------------------- Filesystem Options ---------------------------

    [homes] 在客户端显示的共享目录名称

    comment = homes directory 此共享目录描述

    browseable = no 不在客户端显示共享目录（即不显示[homes]）

    writable = no 不可写

    read only=yes 可读取

    create mode = 0664 samba用户在自己家目录（默认/home/sambauser）下新建 文件的默认权限为0644（0为acl权限）

    directory mode = 0775 samba用户在自己家目录（默认/home/sambauser）下新建 目录的默认权限为0775（0为acl权限）

    [printers] 打印功能共享目录

    comment = All Printers

    path = /var/spool/samba

    browseable = no

    guest ok = no

    writable = no

    printable = yes

    # ---------------------------- user public options -------------------------

    [wxwsmb] 服务器共享目录名称

    comment = user public directory

    path=/tmp/public 实际的共享目录绝对路径

    browseable = yes

    writable = yes

    read only= yes

    public= yes 可以共享

    write list=@wxwsmb 可以共享此目录的samba用户组
    ```

3. 启动/重启服务

    [root@localhost ~]# service smb restart

    关闭 SMB 服务： [确定]

    启动 SMB 服务： [确定]

    [root@localhost ~]# service nmb restart

    关闭 NMB 服务： [确定]

    启动 NMB 服务： [确定]

4. 查看状态

    [root@localhost ~]# netstat -ltunp | grep smb 查看网络端口的监控状态

    tcp 0 0 0.0.0.0:139 0.0.0.0:* LISTEN 1507/smbd

    tcp 0 0 0.0.0.0:445 0.0.0.0:* LISTEN 1507/smbd

    tcp 0 0 :::139 :::* LISTEN 1507/smbd

    tcp 0 0 :::445 :::* LISTEN 1507/smbd

###  二、创建samba用户

[root@localhost ~]# groupadd wxwsmb

[root@localhost ~]# useradd -g wxwsmb smb2

[root@localhost ~]# pdbedit -a -u smb2 创建samba用户

new password:

retype new password:

Unix username: smb2

NT username:

Account Flags: [U ]

User SID: S-1-5-21-1442997883-3833565404-3457035396-1002

Primary Group SID: S-1-5-21-1442997883-3833565404-3457035396-513

###  三、测试（服务器ip地址为192.168.1.24）

```

[root@localhost ~]# smbclient -L //192.168.1.24 -U smb2

Enter smb2's password:

Domain=[MYGROUP] OS=[Unix] Server=[Samba 3.6.9-164.el6]

Sharename Type Comment


wxwsmb Disk user public directory

IPC$ IPC IPC Service (MY Samba Server Version 3.6.9-164.el6)

smb2 Disk homes directory



[root@localhost ~]# mount -t cifs //192.168.1.24/smb2 /mnt -o username=smb2

Password: 输入设定的samba用户密码

[root@localhost ~]# df -h

Filesystem Size Used Avail Use% Mounted on

/dev/sda2 49G 5.8G 40G 13% /

tmpfs 398M 0 398M 0% /dev/shm

/dev/sda1 194M 28M 157M 15% /boot

//192.168.1.24/smb2 49G 5.8G 40G 13% /mnt

```

###  四、注意事项：

1、记得将防火墙（也可以将防火墙开启通过制定规则来使服务器与客户端通信）和selinux关闭；

2、共享目录开放的权限要跟目录本身的权限一致才能达到想要的你想要的效果。

####  常见问题：

windows中输入用户账号密码提示账号密码错误，使用

smbclient -L //192.168.32.220 -U lbt

在命令行登录，错误信息：

session setup failed: NT_STATUS_LOGON_FAILURE

解决办法：

这是因为此用户没有成为samba服务的用户，解决办法是将此系统用户加入到samba中：

执行命令： smbpasswd -a username

设置密码即可

### 捐赠
如何觉得本文章对你有帮助，欢迎捐赠

![捐赠](http://7xifb5.com1.z0.glb.clouddn.com/wustrive-hexo%E6%94%AF%E4%BB%98%E5%AE%9D%E4%BB%98%E6%AC%BE%E4%BA%8C%E7%BB%B4%E7%A0%81%E6%9C%80%E5%B0%8F.png)