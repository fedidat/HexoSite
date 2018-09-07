---
title: "XFCE: configure connected displays automatically"
date: 2018-07-16 07:56:15
tags:
---

Every day, when I come home from work, I connect my Xubuntu XPS 15 9560 laptop to my 27" secondary monitor and need to do the same ritual: 

1. wait for xfce4-display-settings to pop up, 
2. select "extend to the right" (which restricts my secondary monitor to be on the right of my laptop), 
3. inally select advanced and adjust the position of the secondary monitor to roughly where it needs to be: a little above the laptop's display.

I got tired of this very quickly, but XFCE's Display settings lacks consistency and there's no other way but to develop a custom solution for now.

So here are the steps:

## 1. Disable the automatic XFCE display event

Open `xfce4-display-settings` (or just open the Whisker menu and look for "Display"), then toggle "Configure new displays when connected". This will disable the popup every time you connect a screen, since we'll now do it automatically.

![toggle "Configure new displays when connected](/images/420-xfce-display/disable.png)

## 2. use Arandr to find out the right command

First, we have to find the right xrandr command to automatically setup your connect, e.g `xrandr --output DP-1 --mode 2560x1440 --pos 1920x0 --output eDP-1 --pos 0x700 --scale 1x1` in my case.

ARandR is a visual interface for xrandr (the X11 "[resize and rotate](https://en.wikipedia.org/wiki/RandR)" protocol), which can also save commands as shell scripts. Install it with `sudo apt-get install ARandR`.

Play with the displays until it works and then select `Layout -> Save As` to save the command as a `.sh` file. Experiment with the values until you get a minimal, reliable and pixel-perfect command.

## 3. Prepare a script

The following script will do the actual automatic connection. It is heavily inspired by this [StackOverflow answer](https://unix.stackexchange.com/a/52051): I struggled for a while to understand why my command couldn't be run automatically until that answer indicated that it [needed a DISPLAY handle and XAUTHORITY](https://bugs.launchpad.net/ubuntu/+source/xserver-xorg-video-intel/+bug/660901).

This is my script:

```bash /home/[user]/scripts/layout.sh
#!/bin/sh

date >> log.txt #log the date for debug purposes
sleep 1 #give the system time to setup the connection

#CHANGE THE 'DP-1' IN THE FOLLOWING LINE
dmode="$(cat /sys/class/drm/card0-DP-1/status)"
export DISPLAY=:0
export XAUTHORITY=/home/ben/.Xauthority

if [ "${dmode}" = disconnected ]; then
    echo disconnected >> log.txt
elif [ "${dmode}" = connected ]; then
    if xrandr -q | grep -q "2560"; then #the resolution should appear
        #THIS IS THE XRANDR COMMAND, output piped to the log
        /usr/bin/xrandr --output DP-1 --mode 2560x1440 --pos 1920x0 --output eDP-1 --pos 0x700 --scale 1x1 2>&1 | tee -a log.txt
        echo "success" >> log.txt
    else
        echo "no resolution, bad setup" >> log.txt
    fi
else
    echo "unknown event" >> log.txt
fi
```

A couple of notes here:

- On line 4, the status of the display is inserted in a variable which we later check to determine the state of the screen. You should change the "DP-1" to the name of your display as it appears in `xrandr -q`.
- On disconnected or unknown events, I let XFCE handle the automatic resizing. Otherwise, `/usr/bin/xrandr --auto` should be used.
- The xrandr command is executed if two conditions occur: the monitor is connected following the event, and the resolution appears in `xrandr -q`. This is the purpose of line 14, but you should tune it to fit the xrandr query output.

## 4. Add a udev rule

udev is a Linux device manager, which generates various events. These can be observed through `sudo udevadm monitor --environment`. What interests us here is that we can make a udev rule which can trigger a xrandr script on device change events.

So make a new rule, e.g `/etc/udev/rules.d/10-screen.rules`. `10` is intentionally a low priority number so we make sure our script has no conflicts. The content of the rule would probably be:

`ACTION=="change", SUBSYSTEM=="drm", RUN+="/bin/sh -c 'cd /home/[user]/scripts; /bin/sh layout.sh'"`

The `RUN` indicates the path of the script we made in step 3. The `ACTION` and `SUBSYSTEM` are trigger rules for udev and can be tuned if you see that the script doesn't run. Use the `sudo udevadm monitor --environment` command above, then plug your monitor and see what events play.

After inserting or editing a udev rule, run `sudo udevadm control --reload-rules` or even `sudo service udev restart`. Check that the rule can be parsed by running `udevadm test ...`, then run `udevadm trigger` to trigger all the rules so you should at least see the date in the script log.

## Further remarks

1. For more complex setups (three screens, different home/work configurations), you can use [autorandr](https://github.com/wertarbyte/autorandr) which can detect the current configuration but still needs to be triggered by udev or other methods (or even manually), e.g [here](https://unix.stackexchange.com/a/22462).
2. If you use Nvidia drivers, this won't work. Nvidia replaces the Linux device manager. Note that having an nvidia card might still work if you use X11/Nouveau drivers. But read on for other solutions.
3. The way XFCE does it (as explained [here](ps://forum.xfce.org/viewtopic.php?id=11845)) is that it reacts to xrandr RRScreenChangeNotify event. Note that this works with Nvidia drivers. The former link gives C and Python implementations [here](https://stackoverflow.com/a/21498380), and they work well with the same technique presented here, although you should remember to provide a DISPLAY handle and XAUTHORITY. I suggest using C with `gcc -o screen-watcher monitor_watcher.c -lxcb -lxcb-randr`, calling xrandr with a `system()` call, and making it a systemd service. Another approach is [randrd](https://bitbucket.org/portix/srandrd/overview) which works the same way.
