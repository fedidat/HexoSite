---
title: XPS boot 	
date: 2018-03-27 07:52
updated: 2018-03-27 07:52
comments: true
tags:
- linux
categories:
- linux
permalink: xps-boot
---

On Friday I updated my BIOS because I had been having some audio issues accross my operating systems.

This turned out to be a slight mistake because I had not made any backup of my BIOS settings. 
It took me a while to realize that this was the problem.

Eventually I realized that in order to install Linux I had to activate **AHCI** in the BIOS settings menu,
in order for the system to even mount the hard drive.

After that, the laptop kept booting to Windows, since the BIOS did not remember to boot to GRUB.
In order to fix that, I booted an Xubuntu 16.04.3 USB drive which I always have lying around for
such occasions. There, I quickly installed the **amazing** 
[Ubuntu Boot-Repair utility](https://help.ubuntu.com/community/Boot-Repair), launched it
and voila! Fully functioning dual boot is back. Still with the
same grub.cfg and everything.

Now, being a high-level programmer, I still sadly don't know what kind of black magic goes on under the hood of Boot-Repair aside from resinstalling GRUB, 
or whatever EFI, MBR or GRUB do exactly. But Boot-Repair works every time.

If you have trouble booting from thumb drive, I recommend using the FOSS [Rufus](https://rufus.akeo.ie/) from Windows. 
Never had better luck than with this. For some reason using `dd` or graphical options under Linux don't work as well as Rufus. 
Use the "MBT partition scheme for BIOS or UEFI", with everything else default and the recommended options elsewhere, 
and finally set your BIOS to boot using EFI, otherwise Boot-Repair won't be able to work its magic.

By the way, I have not had audio issues since the BIOS upgrade.
Fingers crossed...