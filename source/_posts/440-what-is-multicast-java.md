---
title: Multicasting for Java developers
date: 2018-08-08 16:26:40
tags:
---

At my office recently, I had been trying to understand how multicasting works. All I could find were various Java tutorials on how to "join groups", like [this one](http://www.baeldung.com/java-broadcast-multicast) on Baeldung. I didn't find this very helpful as it assumed multicasting just worked magically without explaining what it is or when it wouldn't work (for instance over the Internet without a VLAN). So I sifted through the Internet and will summarize as far as I understand it.

# What is multicasting?

Say you need to send some data to multiple users. Unicast is one method of doing it: just sending the same packets to all users. This is costly, as traffic goes linearly. Multicast allows to send the traffic just once, and let the routers take care of splitting the traffic as needed. Illustration:

<figure class="left-align quarter-width">![Basic illustration](/images/440-what-is-multicast-java/base.png)<figcaption>
  from [Consortium GARR's EUMED 2004](https://www.garr.it/eventiGARR/emc_training/tutorials/mcast_tutorial.pdf)
</figcaption>
</figure>

# And how does that work?

## Sending a packet

First, a packet must be sent to some address in the Multicast Address Group Range. In IPv4, that's Class D 224.0.0.0/4 (224.0.0.0 to 239.255.255.255). In IPv6, that's ff00::/8, although many scopes are available (see [here](https://en.wikipedia.org/wiki/Multicast_address#IPv6)). From here on, however, we will focus on IPv4. In practice, the most common use of multicasting for a developer is to route packets efficiently within an organization. IANA has allocated addresses for this use which it calls "administratively scoped", in range 239.0.0.0-239.255.255.255.

At the link layer, the IP address is then converted to a MAC address, with its lower 23 bits making it to the lower 24 bits of the MAC address 01-00-5e-XX-XX-XX. Note that some overlap may result with the middle 5 bits between the 4-bit prefix and the 23 lower bits.

## Navigation

The packet must now navigate the network. This requires the use of multicast routing protocols that will propagate knowledge of routes to multicast addresses. The most popular of these protocols is [PIM](https://en.wikipedia.org/wiki/Protocol_Independent_Multicast) (Protocol Independent Multicast). It may operate in three modes:

1. Dense mode: Routers first flood the network, then downstream routers that have no host with a subscription send prune messages to unsubscribe.
2. Sparse mode: Uses a rendezvous point (RP) router. The RP matches receivers with senders and all routers know a path to it. Routers will forward multicast packets to the RP, and routers that want to subscribe to some group (or to leave it) will tell the RP directly periodically. Detailed explanation [here](https://networklessons.com/multicast/multicast-pim-sparse-mode/).
3. Bidirectional PIM: Builds shared bi-directional trees. No sender-specific state or shortest-path tree.

*Note that this means routing overhead. This would be impossible on the worldwide Internet. ISP routers therefore do not forward PIM messages.*

More info on PIM [here](https://www.netcraftsmen.com/wp-content/uploads/2010/12/20101117_cmug_Multicast_Deepdive.pdf).

## Last hop

PIM requires receivers to make themselves known. But before last-hop routers (just next to the receiving hosts) can ask to receive or prune a group, it must first know which hosts are subscribed to which address. This is usually accomplished with a protocol called [IGMP](https://en.wikipedia.org/wiki/Internet_Group_Management_Protocol), between hosts and the last-hop router. There are three types of IGMP messages:

1. Membership report: hosts inform the router that they (still) need some group.
2. Membership query: the router asks if anyone still needs some group previously subscribed, with some timeout after which the router removes itself from that group.
3. Leave group: hosts inform the router that they no longer need this group.

More information on IGMP with images [here](https://mrncciew.com/2012/12/25/igmp-basics/).

Further, switches between that last-hop router and the receiving host must flood the network. That is, unless they support IGMP with a technique called IGMP Snooping: switches learn which hosts requested group membership and then only forward packets to them instead of flooding the LAN.

Here is an overview of the whole process from [Wikipedia's article](https://en.wikipedia.org/wiki/Internet_Group_Management_Protocol):

![Overview](/images/440-what-is-multicast-java/e2e.png)

# Multicasting in Java

Now that we have a good end-to-end understanding of how multicast works, let's go over what interests us as Java developers: sending and receiving packets.

## Sending

As you probably noticed, there is nothing special about a multicast packet except for its destination address being an multicast group IP. Therefore you can use the usual methods to send packets. However, note that multicasting only works in one direction. This means that TCP cannot be used with broadcast or multicast, and instead you have to send UDP, for example with `DatagramPacket`. Here is an example from [Baeldung](http://www.baeldung.com/java-broadcast-multicast):

```java 
public class MulticastPublisher {
    private DatagramSocket socket;
    private InetAddress group;
    private byte[] buf;
 
    public void multicast(
      String multicastMessage) throws IOException {
        socket = new DatagramSocket();
        group = InetAddress.getByName("230.0.0.0");
        buf = multicastMessage.getBytes();
 
        DatagramPacket packet 
          = new DatagramPacket(buf, buf.length, group, 4446);
        socket.send(packet);
        socket.close();
    }
}
```

## Receiving

Assuming that the networking equipment had been setup correctly, the only thing left we need done at the host is to send the IGMP membership report. This is accomplished using `MulticastSocket::joinGroup`. Again with an example from Baeldung:

```java
public class MulticastReceiver extends Thread {
    protected MulticastSocket socket = null;
    protected byte[] buf = new byte[256];
 
    public void run() {
        socket = new MulticastSocket(4446);
        InetAddress group = InetAddress.getByName("230.0.0.0");
        socket.joinGroup(group);
        while (true) {
            DatagramPacket packet = new DatagramPacket(buf, buf.length);
            socket.receive(packet);
            String received = new String(
              packet.getData(), 0, packet.getLength());
            if ("end".equals(received)) {
                break;
            }
        }
        socket.leaveGroup(group);
        socket.close();
    }
}
```

# Conclusion

To be honest, multicasting is on the decline. I don't think IPv4 will last very long, and IPv6 has some better mechanisms. Besides, since multicast cannot be used on the Internet, CDNs have largely replaced its use-case for traffic, as well as cloud localization making it easy to allocate clusters in each major area. But it still has its use for the next decade at least in large private organizations.

I hope this article gave a good understanding of multicasting, both in understanding the networking implications, as well as how it affects us programmers.