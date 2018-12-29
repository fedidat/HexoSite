---
title: Top 10 commands on your terminal
date: 2018-12-29 20:40:03
tags:
---

On [Shekhar Gulati's blog](https://shekhargulati.com/2018/05/21/top-10-commands-that-you-use-on-your-command-line-terminal/), I discovered a neat command that lists the top 10 commands that you use in your terminal, based on your shell history files.

```bash
history | awk '{CMD[$2]++;count++;}END { for (a in CMD)print CMD[a] " " CMD[a]/count*100 "% " a;}' | grep -v "./" | column -c3 -s " " -t | sort -nr | nl |  head -n10
```

Here are the results on my personal machine:

     1	957  17.918%     sudo
     2	602  11.2713%    git
     3	452  8.46283%    cd
     4	252  4.71822%    hexo
     5	215  4.02546%    npm
     6	123  2.30294%    ll
     7	121  2.26549%    nano
     8	95   1.77869%    gcc
     9	83   1.55402%    make
    10	78   1.4604%     ssh

A few observations:

* Shame on me for still using `nano`, but getting used to `vim` is too much strain on me right now. It is absolutely at the top of my personal improvement to-do list to get used to a tmux+vim workflow, as well as switching from XFCE to i3wm for mouseless window management.  
  These changes are too risky and heavy right now, as I depend on my workflow daily in my disk's only Linux partition on my only PC. I think I'll train in a VM or wait for a more stable situation to make the leap.
* There's way too much `cd` and `ll`. I should get used to a command-line file manager, such as `ranger` or `nnn`.

So I'm no example. But that's a great way to put forward some of the bottlenecks in your workflow.

For the sake of sample size, I do recommend having a large history file size, e.g for `bash`, put in your `~/.bashrc`:

```bashrc
HISTSIZE=10000 #max number of commands to remember per ongoing session
HISTFILESIZE=20000 #max number of lines contained in the history file
```

Or use negative values to set the size to infinite.

This is also useful for the underrated Cmd+R command lookup, for backing up your installed programs or for other history related needs. The storage cost is often negligible and it could save your day.