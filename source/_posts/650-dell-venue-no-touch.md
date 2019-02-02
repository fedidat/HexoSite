---
title: No touchscreen input on Dell Venue Pro
date: 2019-02-02 22:27:50
tags:
---

I'm straying from my usual programming posts to write about a random computer fix.

![Dell Venue 8 Pro](/images/650-dell-venue-no-touch/dell-venue.jpg)

I recently received a Dell Venue 8 Pro T01D as a special gift: "it doesn't work, if you can fix it, it's yours". I had no idea what I had even received, apart from being an x86 tablet. But after charging it and turning it on, I saw the Windows 10 setup screen, albeit with a small problem: the touchscreen wasn't responding.

With the help of a USB OTG cable as pictured below, along with a USB hub, I could connect a mouse and keyboard and get to the Windows desktop. That didn't do much though, since the touchscreen still wasn't working, and now I could see that neither was the WLAN connection.

![USB OTG cable](/images/650-dell-venue-no-touch/usb-otg.jpg)

I then found a Dell support [thread](https://www.dell.com/community/Tablets-Mobile-Devices/Dell-Venue-8-Pro-Touch-screen-not-working/td-p/4481981) recommending a BIOS update and firmware drivers. While the BIOS update installed fine, it didn't help and the firmware drivers were not even detecting the devices, and neither were they displaying under the Device Management utility (Win+R, devmgmt.exe, look for "HID-compliant device" under "Human Interface Devices").

Eventually, after trying other drivers, what turned out to help the most were the chipset drivers ([these](https://www.dell.com/support/home/il/en/ilbsdt1/drivers/driversdetails?driverId=JVX4C&osCode=WB32A&productCode=dell-venue-8-pro) ones on my hardware). Seems like the CPU had a bad time communicating with the other components.

After doing that, I could install the [networking ](https://www.dell.com/support/home/il/en/ilbsdt1/drivers/driversdetails?driverId=FG0HG&osCode=WB32A&productCode=dell-venue-8-pro) and [firmware](https://www.dell.com/support/home/il/en/ilbsdt1/drivers/driversdetails?driverId=8T9WD&osCode=WB32A&productCode=dell-venue-8-pro) drivers to restore WLAN and touchscreen functionalities.

In short, at the very least find an USB OTG cable and connect a mouse or keyboard to get to the desktop. Then transfer the chipset and WLAN driver, either through a thumb drive and USB hub to have the mouse transfer the files, or try using the microSD slot. Finally, once the Venue is connected to the Internet, you can download and install the rest of the drivers. Updating Window 10 then brings a lot of worthy features such as the swipe keyboard.

While I'm a fan of Linux, after playing with this tablet for a few days, I have to admit that Windows 10 is the best option right now for serious tablets. Even with its 2GB RAM, it works surprisingly well. You need to embrace the spyware and the bloatware of course, but it has its use cases.