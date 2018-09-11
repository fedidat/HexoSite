---
title: Xubuntu freezing at LightDM and other GPU problems
date: 2018-09-11 22:03:15
tags:
---

## Trouble on the horizon

Yesterday, I updated Bumblebee on my routine update-upgrade check. An hour later, after closing and reopening the lid, LightDM stopped responding to mouse movement or keystrokes, and the input cursor stopped blinking after 5 seconds.

That was weird but crashes happen, though rarely. After rebooting, same thing, over and over again.

By booting into recovery mode, and then selecting `dpkg` to remount the filesystem in read-write, then selecting `shell` to drop into a root shell, I was able to remove packages like `bumblebee`, `powertop`, `nvidia-384`... to no avail.

## The reinstall hammer

Back home, I made a live USB of Xubuntu 18.04 using Rufus from Windows (which seems to be the only way to make a fully working live USB these days). And even that wouldn't work! Every time, after booting the live USB it would print the following message then hang:

    [   0.035095] ACPI Error:  [\_SB_.PCI0.XHC_.RHUB.HS11] Namespace lookup failure,
    AE_NOT_FOUND  (20160930/dswload-210)
    [   0.035102] ACPI Exception: AE_NOT_FOUND, During name lookup/catalog (20160930
    1/psobject-252)
    [   0.035144] ACPI Exception: AE_NOT_FOUND, (SSDT:xh:rvp11) while loading table
    (20160930/tbxf load-228)
    [   0.038190] ACPI Error: 1 table load failures, 12 successful (20160930/tbxf lo
    ad-246)

Disabling ACPI in the BIOS would not help. Later on, I finally got it working. As you drop into Grub, before selecting `Try Xubuntu`, press `e` and add `nomodeset` to the kernel parameters (the line that starts with `linux`, after `quiet splash`). This tells the init system to not load video drivers until after X is launched, and prevents some Nvidia voodoo from freezing the system. This is an [official solution by Dell](https://www.dell.com/support/article/il/en/ilbsdt1/sln306327/manual-nomodeset-kernel-boot-line-option-for-linux-booting?lang=en) in order to boot a live USB. It is not needed after installing.

So I took the occasion to upgrade from Xubuntu 16.04 to 18.04. Since I always use the root/home partitioning technique (see) this [guide](https://www.tecmint.com/install-ubuntu-16-04-alongside-with-windows-10-or-8-in-dual-boot/)), I only reformatted the root partition and everything restored automagically. I only had to reinstall some packages and solve a few XFCE quirks due to the update, mostly related to the panel.

## What now?

After I had restored a fully working environment, and arguably an even improved one, came one big question: what about the graphics? How do we use Linux graphics without wrecking the kernel? After much tinkering, I strongly suggest to not go out there blindly, and instead follow a good guide.

I have found two reliable solutions: either using the proprietary Nvidia drivers or turning off the GPU and live off Intel's IGP.

### 1. With the Nvidia drivers

Much wisdom can be found online regarding the use of Nvidia's proprietary drivers, and their weird behavior with some laptops. In the end, I found the best collection of good practices to be David Akeley's [DementedIGPU script](https://github.com/akeley98/DementedIGPU/). I don't think I saw it miss one thing, and I can attest to its simplicity and reliability. Highly recommended if you want to enable the GPU.

### 2. Goodbye GPU, hello IGP

Foregoing the dedicated GPU altogether may be an interesting solution. Not every buyer of a laptop with a dedicated GPU may be into games, and powering it off would make sense energy-wise.  

But just running `sudo prime-select intel` won't cut it here, as Nvidia's drivers will still be running in the background. And while that may not be that problematic for you, it might cause mysterious problems. For example, suspend might at times cause your laptop to overheat and drain its battery, which took me months to correctly diagnose. That is because this setup [is not powering off the GPU](https://bugs.launchpad.net/ubuntu/+source/nvidia-prime/+bug/1765363).

So if you want to go down that path, check [this](https://www.reddit.com/r/Dell/comments/9724vh/xps_9560_fedora_28_guide_for_power_saving_and_no/) guide.

### Making a choice

Unless you have an allergy to proprietary drivers, I suggest you try the Nvidia path. If glitches occur, try the second technique to use just the IGP. Graphics performance will obviously suffer, and you may lose some compatibility (as I did playing Donut County on WINE, which only worked under Nvidia). So choose carefully.

In my case, I applied the Demented-iGPU technique and am happily running the Nvidia drivers.

-------

Sidenote: Shoutout to Visual Studio Code which saved my work before the crash (as I had not saved a bunch of code before closing my laptop's lid), all the way until after reinstalling the root partition.