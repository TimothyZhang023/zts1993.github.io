---
layout: post
title: wp-o-matic-for-SAE
date: 2016-02-18 13:03:39  +0800
categories: 发布
---

wp-o-matic SAE移植版 支持远程图片保存。  
修改wpomatic.php文件  
705行 $stroage=’http://matic-[storage的名字].stor.sinaapp.com/img/’.$cachepath;  
706行 file_put_contents(‘saestor://[storage的名字]/img/’.$cachepath . $filename, $contents);  
[storage的名字]修改为你的storage的名字  
下载地址 http://pan.baidu.com/share/link?shareid=165329&uk=2298755412  
解压密码: zts1993.com  
