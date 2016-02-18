---
layout: post
title: OPENWRT使用命令行设置无线和有线网络
date: 2016-02-18 11:53:31  +0800
categories: 记录
description: OPENWRT使用命令行设置无线和有线网络
---

在我们将路由器固件刷成开源的基于Linux内核的openwrt系统后，由于openwrt默认未安装WEB管理界面，所以我们需要先通过SSH或者telnet对路由器进行网络设置，设置完成后可通过openwrt的软件包管理opkg安装web设置界面Luci。  
设置lan ip(即访问路由的ip)
{% highlight shell %}
uci set network.lan.ipaddr=[lan ip]
{% endhighlight %}

使用pppoe设置
{% highlight shell %}
uci set network.wan.proto=pppoe //设置wan口类型为pppoe
uci set network.wan.username=[上网帐户]
uci set network.wan.password=[上网密码] //这两行设置pppoe用户名和密码
{% endhighlight %}

如果要挂在上级路由下面,就需要进行下面的设置
{% highlight shell %}
uci set network.wan.proto=none //关掉wan
uci set network.lan.gateway=[上级路由ip] //网关指向上级路由
uci set network.lan.dns=[上级路由ip] //dns指向上级路由
uci set dhcp.lan.ignore=1 //关掉lan的dhcp
{% endhighlight %}

最后对无线网络进行配置
{% highlight shell %}
uci set wireless.@wifi-device[0].disabled=0 //打开无线
uci set wireless.@wifi-device[0].txpower=17 //设置功率为17dbm 太高会烧无线模块
uci set wireless.@wifi-device[0].channel=6 //设置无线信道为6
uci set wireless.@wifi-iface[0].mode=ap //设置无线模式为ap
uci set wireless.@wifi-iface[0].ssid=[自己设置SSID] //设置无线SSID
uci set wireless.@wifi-iface[0].network=lan //无线链接到lan上
uci set wireless.@wifi-iface[0].encryption=psk2 //设置加密为WPA2-PSK
uci set wireless.@wifi-iface[0].key=[密码] //设置无线密码
{% endhighlight %}

提交应用配置
{% highlight shell %}
uci commit //应用
/etc/init.d/network restart //重启网络服务
{% endhighlight %}

安装luci管理界面
{% highlight shell %}
opkg update // 更新软件列表
opkg list-installed // 查看已安装软件
opkg install luci // 安装LUCI
opkg install luci-i18n-chinese // 支持中文
{% endhighlight %}

即可完成LUCI的安装。

输入以下命令开启支持web服务的uhttpd，并设置其为自启动：
{% highlight shell %}
/etc/init.d/uhttpd enable # 开机自启动
/etc/init.d/uhttpd start # 启动uhttpd
{% endhighlight %}
