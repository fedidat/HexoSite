---
title: XFCE4 fix panel across the middle of the screen
date: 2019-05-13 07:59:03
tags:
---

Sometimes when I disconnect my Xubuntu laptop from my second display, if I don't give it time to redetect displays before closing it, the XFCE4 panel (menu bar) will appear across the middle of the screen, like so:

![xfce-panel-middle-screen](/images/710-xfce-panel-middle-screen/xfce.png)

XFCE appears to see a ghost display for some reason. In fact, as I took the above screenshot, I saw that it was 1748 pixels high instead of 1080, so the wallpaper actually starts normally at the top of the ghost display, it's just cropped from that screenshot.

I have checked various possible reasons for that number but nothing made much sense, so I don't know what the window system is thinking here.

For a quick fix, just make X redetect your displays using:

    xrandr --auto

The panel should now be back at its intended place. For a more permanent solution check [my article](/420-xfce-display-auto/) on making a script that runs at login.