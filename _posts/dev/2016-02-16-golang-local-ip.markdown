---
layout: post
title: "Golang 获取本机IP"
date: 2016-02-16 12:55:53  +0800
categories: Dev
---

==

{% highlight golang %}

func GetLocalAddressByName(name string) (string, error) {

	interfaces, err := net.Interfaces()
	if err != nil {
		return "", err
	}

	for _, inter := range interfaces {

		if strings.HasPrefix(inter.Name, name) {

 			addrs, _ := inter.Addrs()

			for _, value := range addrs {
				ips := strings.Split(value.String(), "/")

				for _, item := range ips {

					ip := net.IP{}

					ip.UnmarshalText([]byte(item))

					res := ip.To4()

					if res != nil {
						return item, nil
					}

				}

			}

		}


	}


	return "", errors.New("no available addr found")

}



func GetLocalAddress() string {

	var addr string
	var err error

	netCards := []string{"bond0", "bond", "eth0", "eth", "em1", "em", "Internet", "WLAN", ""}


	for _, netCard := range netCards {
		addr, err = GetLocalAddressByName(netCard)

		if err == nil {
			break
		}
	}

	return addr

}
 
{% endhighlight %}


根据网卡名称来，主要是针对Linux，Windows下可以修改网卡名称~