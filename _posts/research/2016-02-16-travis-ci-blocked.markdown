---
layout: post
title: "travis-ci打不开解决"
date: 2016-02-16 13:06:39  +0800
categories: 折腾 
description: travis-ci打不开解决
---

虽然路由器上了SS，可是最近travis-ci打开是空白。

原因是js https://travis-ci-com.global.ssl.fastly.net/assets/vendor-203bc1bf399e89b6815a49be7adde6a7.js被墙了

显然是CDN的亚洲节点被墙，或者是asia ip太宽松导致的，

travis-ci-com.global.ssl.fastly.net解析结果地址为103.245.222.249

正好在103.0.0.0/8这条规则里面。

修改成  
103.0.0.0/9  
103.128.0.0/10  
保存重启插件，访问成功
