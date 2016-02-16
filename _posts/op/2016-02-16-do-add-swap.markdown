---
layout: post
title: Digitalocean添加swap
date: 2016-02-16 13:51:12  +0800
categories: 运维
---

简单几步给digitalocean添加swap：

{% highlight shell %}

#1.进入目录
cd /var/
#2.获取要增加的SWAP文件块(这里以1GB为例)
dd if=/dev/zero of=swapfile bs=1024 count=1038336
chmod 600 swapfile 
#3.创建SWAP文件
/sbin/mkswap swapfile
#4.激活SWAP文件
/sbin/swapon swapfile
#5.查看SWAP信息是否正确
/sbin/swapon –s
#6.添加到fstab文件中让系统引导时自动启动
echo “/var/swapfile swap swap defaults 0 0″ >>/etc/fstab

{% endhighlight %}
