---
layout: post
title:  "阿里云笔记(1)"
cover:  cover.jpg
date:   2014-05-18 16:25:00
categories: hosting, vps, aliyun
---

最近使用阿里云，发现有些需要注意的地方：

### 默认没有挂载数据盘

解决方法有两个：

* 最简单是直接运行 /root/auto_fdisk.sh，会自动帮你挂载数据盘
* 按照以下的步骤一步步完成：

```
$ su root
$ fdisk /dev/xvdb

根据提示，依次输入 "n", "p", "1"，然后按两次回车
输入 "w"，提示 Syncing disks，表示完成分区

$ mkfs.ext3 /dev/xvdb1
$ mkdir /data
$ mount /dev/xvdb1 /data
$ vi /etc/fstab

加入

/dev/xvdb1 /data ext3 defaults 0 0

保存退出，操作完成
```

### 默认没有 Swap 交换分区

如果跑的是 Rails 项目，占用内存较多，没有 Swap 分区，偶尔会出现内存不足的情况

```    
$ su root
$ SWAPFILE=/swap
$ dd if=/dev/zero of=$SWAPFILE bs=1M count=2048
$ /sbin/mkswap $SWAPFILE
$ /sbin/swapon $SWAPFILE
$ sh -c "echo '$SWAPFILE none swap sw 0 0' >> /etc/fstab"
```

### 迁移 Mysql 数据到 /data 目录后出现无法启动

主要是 AppArmor 造成的。
假设你的 Mysql 数据移到 /data/mysql，解决方法如下：
    
```
$ su root
$ ln -s /data/mysql /var/lib/mysql
$ "alias /var/lib/mysql/ -> /data/mysql/," >> /etc/apparmor.d/tunables/alias
$ /etc/init.d/apparmor reload
$ service mysql start
```

[http://hi.baidu.com/jiyunjie/item/eed3e3d1656822302a35c7e0](http://hi.baidu.com/jiyunjie/item/eed3e3d1656822302a35c7e0)
[http://blog.sina.com.cn/s/blog_6262a50e0101ildf.html](http://blog.sina.com.cn/s/blog_6262a50e0101ildf.html)
[http://askubuntu.com/questions/137424/moving-mysql-datadir](http://askubuntu.com/questions/137424/moving-mysql-datadir)