---
title: VPS Setup 	
date: 2018-04-22 09:14
updated: 2018-04-22 09:14
comments: true
tags:
- linux
categories:
- programming
---

It's been a while I've had a VPS, and I thought I should exercise some server skills, so this time I documented the process of getting a new VPS running smoothly.

## Picking a host

Getting a server, these are the options:

* Dedicated server ($100+): If you have a huge project or have too much money, this is going to be your option.

* Pay as you go: If you think you will want to scale up your side project instead of just having a sandbox, you can go for platforms such as AWS EC2 or Google Compute Engine.

* Virtual Private Server: If all you want is a small sandbox, you should be fine with a shared VM. There are several options here:
    * Bare metal: These are not exactly VPSes, I am referring to extremely cheap dedicated servers, such as Scaleway or OVH for as low as 3 euros per month (and perpetually out of stock).
    * KVM: True VM, allows you to run Docker or other technologies that require to run on their own kernel.
    * OpenVZ: The truly cheap option. The shared kernel means that many things will be off limits. But good enough for most uses.

I just wanted to start with a dirty cheap sandbox so I picked a $10/year OpenVZ box (2 core, 2GB RAM, 20GB SSD) from https://hiformance.com.

## Linux

I don't think any VPS provider these days doesn't offer at least the latest stable headless images of CentOS, Debian and Ubuntu. I want to get running as fast as possible so I picked Ubuntu 16.04 Server x64.

Further steps:

* Setup SSH PKI: http://www.linuxproblem.org/art_9.html

* Create a user and group:
``` bash
sudo groupadd users
sudo useradd -G users ben
```

* Add to passwordless sudoers in visudo: `ben     ALL=(ALL:ALL) NOPASSWD: ALL`

* Switch the default shell from /bin/sh to Bash: `chsh -s /bin/bash username`

* Copy root's .bashrc and .profile, then `chown -R ben /home/ben`

## X2Go and XFCE

I'm spoiled and don't like overusing the terminal. One awesome thing about X11 is that it's designed around network transparency. So you can get super-fast remote desktop connection through X2Go.

* Start by setting up a firewall and XFCE with this: https://www.digitalocean.com/community/tutorials/how-to-set-up-a-remote-desktop-with-x2go-on-debian-8. Just stop before your install X2Go server.

* Install the X2Go server with this: https://wiki.x2go.org/doku.php/doc:installation:x2goserver#installing_x2go_server

* At this point I got `Connection failed. : mesg: ttyname failed: Invalid argument`. Turned out there was a line that said `mesg n || true` in root's .profile, which I blindly copied... I removed it and X2Go worked.

* Install X2Go client locally, make a shortcut that uses your SSH key, put a shortcut on your desktop if you want. Finally, if you want you can do some xfce customization (most importantly install xfce4-goodies and xfce4-term) and install some basics: `sudo apt install firefox git`.

And voila! You've got the perfect remote desktop solution to your Linux box.

## NodeJS and NPM

Despite being a Java programmer, these days the first thing I do when I get a new box is install node. Javascript is that ubiquitous and quick to get started. However the node/npm installation isn't that obvious, so I thought I'd throw it in:

``` bash
sudo apt install nodejs npm
sudo ln -s /usr/bin/nodejs /usr/bin/node
sudo npm install -g n
sudo n update
sudo n latest
sudo npm install -g npm #twice for some reason
sudo chown -R $USER:$(id -gn $USER) /home/$USER/.config
```
