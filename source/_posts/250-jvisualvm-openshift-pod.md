---
title: JVisualVM to an Openshift pod
date: 2018-06-03 19:08:36
tags:
---

Recently at work, I have struggled to find a way to connect through JVisualVM (an excellent JMX performance visualizer for the JVM) to
an Openshift pod. What I will say is however also valid for Kubernetes pods in general (using `kubectl port-forward`), or for running jconsole on Linux. 

Sadly, the handy method [I have previously used with nodeports](/2018/05/11/170-opening-ports-in-openshift/) will not work here because of hostname problems. 

# Set up JMX

So first of all, I of course started with [the usual JVM flags to enable JMX](http://www.adam-bien.com/roller/abien/entry/how_to_establish_jmx_connection):

```sh
-Dcom.sun.management.jmxremote 
-Dcom.sun.management.jmxremote.port=[RMI_PORT] 
-Dcom.sun.management.jmxremote.rmi.port=[RMI_PORT]
-Dcom.sun.management.jmxremote.ssl=false 
-Dcom.sun.management.jmxremote.authenticate=false 
-Djava.rmi.server.hostname=[HOSTNAME]
```

As usual, you get to pick which port to use for JMX and RMI, [as long as you set both to be the same](http://www.perftactique.com/2018/03/29/remote-jmx-connection-over-the-firewall/). Now for the hostname. You should set up 127.0.0.1. This is because the next step is to set up port forwarding, so the JVM will see the connection as coming locally.

## Port forwarding

Go to some machine (ideally the one you'll run JVisualVM on) and execute:

```sh
oc login #login to Openshift
oc project #switch project if needed
oc get pod #list pods and note the hostname of the one you wish to monitor
oc port-forward [POD_HOSTNAME] [RMI_PORT] #or use kubectl with the same syntax
```

If you have executed this on the machine where you run JVisualVM, you can now connect locally to RMI_PORT. Otherwise, you'll have an additional step of opening a SOCKS proxy to that machine. 

You can [do that with PuTTy](http://realprogrammers.com/how_to/set_up_an_ssh_tunnel_with_putty.html) on Windows, or with `ssh -D` on Linux. Then you can connect, again locally, to RMI_PORT.