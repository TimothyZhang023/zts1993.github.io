---  
layout: post  
title: 重置MySQL root密码
date: 2016-02-18 13:15:02  +0800   
categories: 记录  
---  
  
悲剧啊。那天安装mysql时手速太快可能root密码输入错了。。  
1、停止MySQL服务  
执行：/etc/init.d/mysql stop，你的机器上也不一定/etc/init.d/mysql也可能是/etc/init.d/mysqld  
2、跳过验证启动MySQL  
/usr/local/mysql/bin/mysqld_safe –skip-grant-tables >/dev/null 2>&1 &  
注：如果mysqld_safe的位置如果和上面不一样需要修改成你的，如果不清楚可以用find命令查找。  
3、重置密码  
等一会儿，然后执行：/usr/local/mysql/bin/mysql -u root mysql  
出现mysql提示符后输入：update user set password = Password(‘要设置的密码’) where User = ‘root’;  
回车后执行：flush privileges; 刷新MySQL系统权限相关的表。再执行：exit; 退出。  
4、重启MySQL  
杀死MySQL进程：killall mysqld  
重启MySQL：/etc/init.d/mysql start