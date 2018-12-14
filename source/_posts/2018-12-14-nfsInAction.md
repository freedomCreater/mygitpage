---
title: nfs 共享存储配置
date: 2018-12-14 16:16:57
tags: 
-实施
---



# NFS 共享存储配置过程

## 机器准备

- 主机192.168.129.159

- 客户端1 192.168.129.162
- 客户端2 192.168.129.163

## 安装

```shell
yum install -y nfs-utils  rpcbind
cat /etc/exports
```

## 建立主机共享目录

```shell
mkdir  -p /home/jiaoqx/app/share
chown -R nfsnobody:nfsnobody /home/jiaoqx/app/share
```

## 配置主机的挂载目录

```shell
/home/jiaoqx/app/share  192.168.129.162(rw)  192.168.129.163(rw，sync)
```

配置参数：

rw： 读写

ro ：只读

sync：将数据同步写入内存缓冲区与磁盘中，效率低，但可以保证数据的一致性；

async：是大数据时使用，是先写到缓存区，必要时再写到磁盘里。

all_squash：所有访问用户都映射为匿名用户或用户组；
no_all_squash（默认）：访问用户先与本机用户匹配，匹配失败后再映射为匿名用户或用户组；

root_squash（默认）：将来访的root用户映射为匿名用户或用户组；

no_root_squash：来访的root用户保持root帐号权限；

 

wdelay（默认）：检查是否有相关的写操作，如果有则将这些写操作一起执行，这样可以提高效率；

no_wdelay：若有写操作则立即执行，应与sync配合使用；

 

subtree_check ：若输出目录是一个子目录，则nfs服务器将检查其父目录的权限；

no_subtree_check（默认）：即使输出目录是一个子目录，nfs服务器也不检查其父目录的权限，这样可以提高效率；



## 启动服务

先启动 rpcbind 后启动 nfs

```shell
service rpcbind start
service nfs start
```



让nfs开机启动

```shell
chkconfig rpcbind on
chkconfig nfs on
```



## 查看共享文件服务配置

```shell
showmount -e 192.168.129.159
```

## 客户端配置

1. 安装nfs服务

2. 建立挂在共享的目录，然后执行挂载命令

   ```shell
   mkdir /home/jiaoqx/app/tmp/files
   mount -t nfs 192.168.129.159:/home/jiaoqx/app/share /home/jiaoqx/app/tmp/files
   ```


## 修改主机挂载目录后客户端重新挂载

```shell
umount -f /home/jiaoqx/app/tmp/files
mount -t nfs 192.168.129.159:/home/jiaoqx/app/share /home/jiaoqx/app/tmp/files
service rpcbind restart
service nfs restart
```

