---
title: OpenShift ConfigMap without overwriting the entire folder 	
date: 2018-04-17 19:34
updated: 2018-04-17 19:34
comments: true
tags:
- devops
- openshift
categories:
- programming
permalink: openshift-partial-configmaps
---

Openshift is great... or rather, Kubernetes is. At least if you don't
need Openshift's building features but only the orchestration part.
Although I'm hopeful it will become a very solid and open-source
platform with Red Hat's backing and robust ecosystem.

So I'll write about one trick I've learned recently. 

I think ConfigMaps are very practical. Picture this: you get one source of configuration across your entire deployment, updated instantly across all your pods, and all this backed by a YAML format that can be updated in your CLI if you are so inclined. This is a feature [originally provided by Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/).

While it could easily do with more features, such as integrated version control, it does have one quirk: it uses good old Linux mounts. This means that whatever folder it is mounted to is obliterated from its container to make some room.

But what if your system recently ported to Openshift has some configuration that doesn't exactly fit the ConfigMap, such as binaries? Or your configuration folder happens to also host your logs, or perhaps your libraries sit in the same folder? Or you want to put some application.properties files next to your JAR to use the property that they will override the ones inside of it? Well, in principle you can't do any of these, at least not easily or not without compromise since partial mounts ("subPaths") [are not continuously updated after mounting](https://github.com/kubernetes/kubernetes/issues/50345).

But there is one solution: **mount your ConfigMap then overwrite each pre-existing file with a softlink to the file in the Map**.  One the one hand, the mount will exist fully and be constantly updated. On the other hand, your files not present in the ConfigMap, such as JARs or binaries, will still be in your original folder. Then, every time your application reads from these files, it will actually read from the softlink. While simple in retrospect, it took me time to figure.

I put this in my Dockerfile because of some problems with users in Openshift, but you could put it in your startup script and anything that gets executed at the right time in your pod:

```bash
CMDIR=/usr/user/configmap
APPDIR=/usr/user/app
if [ ! -z $USE_CONFIG_MAP ] && [ $USE_CONFIG_MAP = 'Y' ] && [ "$(ls -A $CMDIR)" ]; then
    for f in `ls $CMDIR`; do
        echo Mapping $CMDIR/$f to $APPDIR/$f
        rm -f $APPDIR/$f
        ln -s $CMDIR/$f $APPDIR/$f
    done;
fi
```

And that works! The only problem with it is that it won't automatically add new files from your ConfigMap. But you could do it from the terminal if you insist on not deploying again.