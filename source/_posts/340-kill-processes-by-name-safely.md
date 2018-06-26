---
title: Killing Linux processes while uninstalling RPMs
date: 2018-06-25 08:15:09
tags:
---

I have been packaging some RPMs at work, and I would like to stop running instances safely before uninstalling.

The clean way to do this is by creating a file containing the PID of the process, but this is less reliable. Alternatively, making the process a service or daemon can allow us to stop it cleanly but is not always possible.

So I gave a dummy argument to the process, e.g `java MyClass -dummy` and thought `kill -9 $(ps -ef | grep dummy) || true` would be enough. There are some variants using equivalent or less common tools like `xargs`, `pgrep` or `pkill` but they lead to the same concerns I will present here.

Notice that this also kills the `grep` process itself. So I used this trick: instead of `ps -ef | grep dummy`, write `ps -ef | grep [d]ummy`. This makes the grep interpret the brackets as a regex: "look for (the groups of characters containing "d") followed by dummy". But the name "[d]ummy" is not a match so the grep won't be affected.

In addition, this may also kill the RPM process itself if dummy is in the name of the package. In that case, the RPM process will end by showing "Killed". For this problem, there is little choice but to switch to a more special dummy flag.