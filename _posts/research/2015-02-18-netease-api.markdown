---
layout: post
title: 网易云音乐API
date: 2016-02-18 12:49:27  +0800
categories: 折腾 
---

前两天看到有个人做了一个高品质音乐的chrome插件。里面用到网易云音乐API感觉这个还不错，  
就是不太喜欢 “云” 这个字，我就不黑了。总的来说音乐的质量远高于豆瓣。顺便就把API弄下来吧。。  

($keyword)是搜索关键字  
搜索API：http://music.163.com/api/search/get/web?csrf_token=  
Method=POST  
HEAD  
Referer:http://music.163.com/search/  
BODY  
type:1  
hlpretag:  
hlposttag:   
autoplay:1  
s:($keyword)  
 
上面的用来搜索歌曲。得到的json结果中每个歌曲会有一个唯一的id，然后下面会用到。  
歌曲详细API：http://music.163.com/api/song/detail/?id=($id)&ids=%5B($id)%5D&csrf_token=  
Method=GET  
($id)=上面JSON中取得的id  
 
非常见到我们就得到了歌曲的详细信息，当然啦里面有音乐的具体下载地址。尽情使用吧~~  