---
title: "Cannot access Google Analytics on Wi-Fi"
date: 2018-08-13 20:22:35
tags:
---

**TL;DR Check your hosts file for a filter list e.g EasyList, AdAway.**

Shamefully, at time of writing this article, this site uses Google Analytics, but as its only tracker. This is temporary, however it gives me the opportunity to talk about a problem that has been bugging me for a while. I had trouble accessing https://analytics.google.com/ from my Wi-FI hotspot, but could access it at home without a problem. I finally got around to checking what's up.

First things first, `nslookup`: 

``` bash
$ nslookup analytics.google.com
Server:		127.0.1.1
Address:	127.0.1.1#53

Name:	analytics.google.com
Address: 127.0.0.1
```

First of all, what kind of DNS server is that? It turns out that I had been using dnsmasq, I believe for some cybersecurity class experiment involving DNS spoofing (the VM setup turned out to cause trouble so I was running things on bare metal). Quick fix from [here](https://askubuntu.com/a/627900):

``` bash
$ sudo nano /etc/NetworkManager/NetworkManager.conf

[main]
plugins=ifupdown,keyfile,ofono
#dns=dnsmasq

$ sudo service network-manager restart
```

And `nslookup` again:

``` bash
$ nslookup analytics.google.com
Server:		192.168.43.1
Address:	192.168.43.1#53

Name:	analytics.google.com
Address: 127.0.0.1
```

Well, I know that address, that's the Android hotspot DNS server address that it delivers by DHCP. Let's check the default gateway:

``` bash
$ route -nee
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface    MSS   Window irtt
0.0.0.0         192.168.43.1    0.0.0.0         UG    600    0        0 wlp2s0   0     0      0
192.168.43.0    0.0.0.0         255.255.255.0   U     600    0        0 wlp2s0   0     0      0
```

Right. And sure enough, on my home connection, it looks better:

``` bash
$ nslookup analytics.google.com
Server:		10.0.0.138
Address:	10.0.0.138#53

Non-authoritative answer:
Name:	analytics.google.com
Address: 216.58.207.78
```

That DNS server is my router's inside NAT address. So let's try a different DNS server on the hotspot then, e.g Google's 8.8.8.8:

``` bash
$ nslookup analytics.google.com 8.8.8.8
Server:		8.8.8.8
Address:	8.8.8.8#53

Non-authoritative answer:
analytics.google.com	canonical name = www3.l.google.com.
Name:	www3.l.google.com
Address: 172.217.17.142
```

Aha. So the DNS server is to blame. I opened my terminal emulator on my phone and sure enough, the nslookup looks just like on my laptop, returns localhost. 

So what can it be? `/etc/hosts` is the first thing to check, and it is indeed littered with stuff blocked by AdAway based on EasyList. (Yes. I am a hypocrite and I block ads yet I use Google Analytics for my own site... I promise it's temporary!)

So I headed to AdAway, used the DNS logging feature, saw the `analytics.google.com` entry and added it to the whitelist from there. Rebooted my phone and it works!

**Side note**: Did you know that systemd falls back to DNS servers in the order of resolv.conf without recovery after the first server comes back? That's one thing to check when debugging DNS. On that note, systemd breaks many very old UNIX practices, and that's just one example. But it has its merits. It's just that it aims to fit 99% of people, and the others are out of luck.