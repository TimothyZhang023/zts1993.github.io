---
layout: post
title: 南京掌上公交api分析加key破解
date: 2016-02-18 12:53:57  +0800
categories: 折腾
---

严正声明:本文仅供研究学习使用，谢绝一切盈利非法行为

最后转载请著名出处

{% highlight php %}
http://58.213.35.9 或者 http://trafficomm.jstv.com
http://trafficomm.jstv.com/smartBus/Module=BusHelper/Controller=Main/Action=GetloadingAd/uid=0（启动广告）
http://trafficomm.jstv.com/smartBus/Module=BusHelper/Controller=Main/Action=getcurrentkey（获取api key作用位置）
http://trafficomm.jstv.com/smartBus/Data/stations.xml （error，这个有问题，不知道有什么用处）
http://trafficomm.jstv.com/smartBus/Module=BusHelper/Controller=Main/Action=GetArroundStation/Range=500/Coorlat=32.106714/Coorlong=118.813844
(获取周边车站--从当前位置找站台)
http://trafficomm.jstv.com/smartBus/Module=BusHelper/Controller=Main/Action=GetStationInfo/Uid=0/StationId=1863(获取指定车站路过车辆--站台)
http://trafficomm.jstv.com/smartBus/Module=BusHelper/Controller=Main/Action=GetLineInfo/Uid=0/LineId=322/IsSearch=1(获取指定车辆路过位置--线路)
http://trafficomm.jstv.com/smartBus/Module=BusHelper/Controller=Main/Action=GetCurrentBus/StationName=%E8%BF%88%E6%8B%9B/LineName=76%E8%B7%AF/Destination=%E5%B0%A7%E5%8C%96%E9%97%A8(%E5%B0%A7%E4%BD%B3%E8%B7%AF)/Key=c13ba9a1cdf7e85ce564f643a079ec59(获取指定线路车辆位置--车辆 注意)
http://trafficomm.jstv.com/smartBus/Module=BusHelper/Controller=Main/Action=GetCurrentBus/StationName=%E6%B5%B7%E5%AD%90%E5%8F%A3/LineName=71%E8%B7%AF/Destination=%E5%8D%97%E4%BA%AC%E8%BD%A6%E7%AB%99/Key=c13ba9a1cdf7e85ce564f643a079ec59
{% endhighlight %}

这里我们注意到GetCurrentBus是有key的。如果key不正确会返回非法调用
注意：Key不能少  url大小写区分
通过分析安卓版客户端源码我们可以得到
这样一段
{% highlight java %}

for (this.currentIndex = BusLineDetailActivity.this.busStationBean.getCurrentIndex(); ; this.currentIndex = BusLineDetailActivity.this.findCurrentStation(BusLineDetailActivity.this.busStationBean))
{
if (BusLineDetailActivity.this.application.validBean == null)
BusLineDetailActivity.this.application.validBean = UserInfoUtils.getInstance().valid("http://trafficomm.jstv.com/smartBus/Module=BusHelper/Controller=Main/Action=GetCurrentKey");
if (BusLineDetailActivity.this.application.validBean != null)
BusLineDetailActivity.this.nowLinesBus = BusUtils.getInstance().obtainLinesBus(BusLineDetailActivity.this.station_level, "http://trafficomm.jstv.com/smartBus/Module=BusHelper/Controller=Main/Action=GetCurrentBus/StationName=" + Utils.encode(BusLineDetailActivity.this.pointName) + "/LineName=" + Utils.encode(BusLineDetailActivity.this.busline) + "/Destination=" + Utils.encode(paramBusLinesBean.getEnd_truename()) + "/Key=" + LibSk.encryptMD5(new StringBuilder("GetCurrentBusspinshine").append(MyBaseCode64.encode(BusLineDetailActivity.this.application.validBean.getValidCode())).toString()));
return null;
str2 = BusLineDetailActivity.this.application.loginResultBean.getId();
break;
}
{% endhighlight %}

 
所以随后的结果就是  Util/urls中的一个String叫KEY_PREFIX他的值是GetCurrentBusspinshine
因此我们用
http://trafficomm.jstv.com/smartBus/Module=BusHelper/Controller=Main/Action=getcurrentkey解析得到的Code(现在是520)将Code用Base64加密得到加密后的值(我现在是NTIw)，在结果之前加上KEY_PREFIX（即GetCurrentBusspinshine）可以得到新的字符串(GetCurrentBusspinshineNTIw)最后将这个 字符串用MD5摘要算法得到结果c13ba9a1cdf7e85ce564f643a079ec59就是我们上面看到的Key了。
 
用php表示结果
 
{% highlight php %}

$Code=http://trafficomm.jstv.com/smartBus/Module=BusHelper/Controller=Main/Action=getcurrentkey中的Code
$key= md5('GetCurrentBusspinshine'.base64_encode($Code));
{% endhighlight %}

Done