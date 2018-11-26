---
title: Switching DNS providers
date: 2018-11-26 08:04:12
tags:
---

## Why

Up until last week, I had no idea that my ISP's DNS provider was slowing me down. Of course, in retrospect, it makes sense since my ISP is so lousy and slow. But consider this: slow DNS requests may represent hundreds of milliseconds of latency!

This is a significant addition when websites these days struggle to load under half a second because of all the scripts, requests between microservices and so on.

You may be using a DNS cache such as under Windows, or with systemd's resolved caching feature. But cache misses are common, and I would wager that most page loads are the first time you visit a domain for the given day.

On top of performance, there are also a number of additional reasons to switch DNS providers:

- Privacy: Your ISP may be snooping on you. In fact, no one has as much ability to snoop on you as your ISP (unless you use a VPN). Why give it easy metadata such as the sites you're visiting? IPs alone may not necessarily indicate a specific domain name unless browsing unencrypted websites.
- Reliability: Your ISP's DNS servers can never be as reliable and fault-tolerant as large companies like Google or Cloudflare, or even as smaller actors such as Comodo or OpenDNS.
- Protection: This is not a factor for me, but some people may want to filter content or to protect themselves from phishing and other risks. DNS providers may assist in this, with OpenDNS and others even offering custom filtering.

So I knew my DNS provider was slow, but how much of a difference is it? A huge one! See [this post](https://medium.com/@nykolas.z/dns-resolvers-performance-compared-cloudflare-x-google-x-quad9-x-opendns-149e803734e5) for some metrics from all over the world.

## Testing

To get concrete measurements, I will use this great and easy to use Linux script: https://github.com/cleanbrowsing/dnsperftest

```bash
git clone --depth=1 https://github.com/cleanbrowsing/dnsperftest/
cd dnsperftest
bash ./dnstest.sh |sort -k 22 -n
```

Results:

                    test1   test2   test3   test4   test5   test6   test7   test8   test9   test10  Average 
    cloudflare        14 ms   14 ms   28 ms   14 ms   23 ms   24 ms   17 ms   73 ms   32 ms   14 ms     25.30
    neustar           72 ms   83 ms   73 ms   70 ms   80 ms   73 ms   69 ms   68 ms   73 ms   74 ms     73.50
    norton            77 ms   77 ms   70 ms   73 ms   69 ms   74 ms   73 ms   75 ms   85 ms   77 ms     75.00
    quad9             75 ms   86 ms   71 ms   72 ms   78 ms   78 ms   75 ms   72 ms   75 ms   71 ms     75.30
    google            77 ms   76 ms   67 ms   99 ms   81 ms   71 ms   65 ms   93 ms   75 ms   63 ms     76.70
    cleanbrowsing     77 ms   77 ms   75 ms   77 ms   72 ms   74 ms   82 ms   90 ms   81 ms   78 ms     78.30
    adguard           80 ms   90 ms   78 ms   75 ms   74 ms   77 ms   72 ms   76 ms   79 ms   91 ms     79.20
    level3            86 ms   86 ms   79 ms   81 ms   78 ms   84 ms   83 ms   88 ms   78 ms   88 ms     83.10
    opendns           78 ms   71 ms   81 ms   110 ms  75 ms   318 ms  76 ms   88 ms   74 ms   71 ms     104.20
    127.0.0.53        235 ms  235 ms  250 ms  217 ms  233 ms  358 ms  197 ms  322 ms  244 ms  261 ms    255.20
    yandex            102 ms  251 ms  111 ms  107 ms  103 ms  109 ms  323 ms  116 ms  267 ms  184 ms    167.30
    comodo            87 ms   194 ms  111 ms  94 ms   145 ms  89 ms   99 ms   96 ms   1000 ms 97 ms     201.20
    freenom           96 ms   323 ms  93 ms   96 ms   563 ms  401 ms  96 ms   472 ms  101 ms  271 ms    251.20

Results may vary a lot between locations so only you can tell which one is the fastest fro you. For me, it was Cloudflare with a huge difference of 229.1ms on average from my ISP, almost a 10x ratio!

## Switching

Your router usually acts as your recursive DNS server, by giving its own address through DHCP. So, switching DNS servers is usually as easy as entering your router interface and changing a value.

For me, on my router's web interface, I have to enter `Basic Settings`, under which I found the `Domain Name Server (DNS) Address`, which I just switched from "Get Automatically From ISP" to Cloudflare's addresses `1.1.1.1` and `1.0.0.1`.

As it happens, Cloudflare's DNS also seems to have gone to greater lengths to demonstrate their concern for privacy than any other public DNS provider, and they often score the best in terms of performance. On the other hand, Cloudflare as a company has a bad track record with privacy and can now correlate DNS requests with the information gathered by their CDN infrastructure, which is still a concern. OpenVPN may be more appropriate if so.

Ultimately, the only ideal course of action here is to use a VPN. Otherwise, I believe mindfully switching DNS resolvers is a good decision.