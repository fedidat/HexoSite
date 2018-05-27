---
title: 'WTF is: Docker ENTRYPOINT'
date: 2018-05-27 07:11:14
tags:
---

*Partly in homage to John Bain AKA Totalbiscuit, a videogame journalist who passed away 2 days ago, I'll be starting a new series of articles, "WTF is". 
Like its namesake did with video games, this series will focus on explaining
somewhat unknown technical concepts shortly and clearly.*

I feel that the difference between Dockerfile's CMD and ENTRYPOINT is not well understood among my coworkers.

In as few words as possible: **a CMD may specify arguments that are fed to an ENTRYPOINT, if both are defined in a copliant way**.

## The ENTRYPOINT

ENTRYPOINT provides a way to tell how the container's command will run, (either from e.g `docker run image echo hello` or from the Dockerfile e.g `CMD echo hello`), possibly with some default arguments. That way, overriding the CMD will not override the entrypoint. However, even this is still doable with the `--entrypoint` argument. There should only be one ENTRYPOINT, otherwise only the last one takes effect.

From the documentation:

>ENTRYPOINT has two forms:
>* ENTRYPOINT ["executable", "param1", "param2"] (exec form, preferred)
>* ENTRYPOINT command param1 param2 (shell form)

The exec form runs an executable, and the shell form runs in `sh -c`. 

## The CMD

CMD is merely the "rest" of the actual command that will start your container, supposed to be overridden more routinely. There should only be one CMD, otherwise only the last is considered.

However, CMD never takes precedence over ENTRYPOINT. ENTRYPOINT's exec form uses CMD as additional arguments, and its shell form completely omits the CMD.

From the documentation:

>The CMD instruction has three forms:
>* CMD ["executable","param1","param2"] (exec form, this is the preferred form)
>* CMD ["param1","param2"] (as default parameters to ENTRYPOINT)
>* CMD command param1 param2 (shell form)
 
These forms work in the same way as ENTRYPOINT, with the exec and default parameter forms being added to the ENTRYPOINT (if one exists, otherwise they're run as an executable), and the shell form being added in `sh -c` (concatenated with ENTRYPOINT's exec form, and dismissed under its shell form).

If this is still confusing, look at the table [here](https://docs.docker.com/engine/reference/builder/#understand-how-cmd-and-entrypoint-interact).

## So what?

In the end, use what's best for you. Unless you have specific architectural goals in mind, I advise you just use one CMD, in either exec or shell form, and be done with it.

And here is my personal advice from experience. for more flexibility, forget ENTRYPOINT and use the shell form of CMD this way:

```bash
CMD \
if [ -n "$COMMAND" ]; sh -c "$COMMAND"; fi \
[the default command]
```

This will allow you to provide an alternative command from an environment variable, even when the `docker run [command]` is not available (e.g Openshift).

It's not the "recommended" way but it is the most readable and the most flexible and that's what counts in my opinion. And later on, when a specific need for a default executable is defined, and a final form of the command is decided, exec forms of both options can be settled on.