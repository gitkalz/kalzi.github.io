---
layout: post
title: "Get vCenter IP from ESXi Host"
date: "2016-10-11"
comments: true
categories: vSphere
---

This is a Quick note to identify the vCenter IP from ESXi interaction. 

While the use cases are quite low, the other day, I got a ESXi host alerting some failure, however, I failed to find the host on my Production vCenter.

Curious to know the vCenter IP the host is reporting, I found the following methods for the record

1) The most easiest way is to connect a vmware client directly to ESXi Host with local root account and the client pops an alert stating "This Host is being managed from `<vcenter>` and blah blah"

Cool!, what if you dont have a VmWare Client that is handy on your machine? Well then you can retrieve vCenter Config from WebBrowser as follows

2) Connect to `ESXiHost` from your webbrowser to `https://EsxiHost/host/vpxa.cfg`, input your ESXi Credentials, and CTRL+F for `ServerIP` section

![alt text](http://i.imgur.com/2oLBbom.png "ESXi Host Browser")

3) The other way is if you have PowerCLI handy, you can use the following 

{% highlight powershell %}
#Connect to ESX Host
Connect-VIServer <ESXIHOST> -user root 

#Get ESXCLI Variable
PowerCLI > $esx = Get-ESXCLI

#Iterate ESXCLI Variable and Query
PowerCLI > $esx.network.ip.connection.list() | Where-Object {$_.State -match "Established"} | Select LocalAddress, ForeignAddress, State | Select-String -Pattern '(:443)'

@{LocalAddress=10.87.167.14:443; ForeignAddress=167.215.232.43:54592; State=ESTABLISHED}
@{LocalAddress=10.87.167.14:443; ForeignAddress=10.87.129.230:44846; State=ESTABLISHED}
@{LocalAddress=10.87.167.14:443; ForeignAddress=10.87.129.230:39415; State=ESTABLISHED}
{% endhighlight %}
