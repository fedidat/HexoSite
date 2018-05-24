---
title: 'Remote debugging: connection refused'
date: 2018-05-24 19:02:19
tags:
---

*I just came back from a frantic week of linguistic competencies exams 
(the British IELTS and the French TEF), so I haven't been writing for over a week.
I thought about sharing my experiences with these exams but I want to keep this blog
focused on programming. Now I have to prepare for some technical interviews in two weeks so I'll be busy but I
still hope to be more active than I've recently been.*

Sometimes in Eclipse or IntelliJ IDEA you'll launch a remote debugging instance and you'll get a "connection refused", which is aptly named because the remote computer refused your TCP SYN.

Of course you first look at `pgrep java` and see the proper `-agentlib:jdwp=transport=dt_socket,server=y,suspend=y,address=1044` JVM argument. So what's wrong?

The logical thing to do is to debug why the socket is not open to your packets. Suppose your remote debugging configuration connects to port **7777**. Head to the remote instance and:

* If Linux, type `netstat -tnap | grep 7777` to see all (`-a`) TCP connections (`-t`) with numeric IP addresses (`-n`), along with the process name (`-p`) filtered by port 7777.
* On Windows, use `netstat -tnap | findstr "7777"`.

If you notice a relevant line that says:

```
tcp        0      0 0.0.0.0:7777           0.0.0.0:*               LISTEN      [pid]/java
```

then this has to be a firewall problem.

But you probably don't, instead you'll see something like:

```
tcp        0      0 [host IP]:7777     [some other IP]:443     ESTABLISHED 14965/java
```

... which means that someone else is already debugging this process. Indeed, when Java receives a remote debugging connection, it stops listening to incoming connections of the debug port. This triggers connection refused for the next clients.

Simple stuff but it can take a few minutes to realize. Particularly when your colleagues take over each other's servers and come to you to find out why remote debugging doesn't work. Hope this helps!