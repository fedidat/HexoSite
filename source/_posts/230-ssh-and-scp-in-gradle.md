---
title: SSH and copying files and folders in Gradle
date: 2018-05-25 08:52:38
tags:
---

Gradle is well on its way to replace Maven thanks to the superiority of Groovy over Maven's Ant-like tasks.
It does have a bit too much magic for me but I'm sure I'll get used to it.

## 1. Why SSH of SCP in Gradle

One important task that you might want to accomplish outside of your CI routine is some kind of deploy task
that allows you to test remote capabilities (like Openstack connectivity) without having to make a commit+push+build cycle for each change.

For this purpose you may setup some special script, or insert it in your natural build flow. But SCP
(secure remote copy) and SSH tasks are often involved in deploys. And Gradle has no easy way to do it.

## 2. The Gradle SSH plugin

If you can, you may apply the [Gradle SSH plugin](https://gradle-ssh-plugin.github.io/). This is not always possible or sufficient.

## 3. The Ant way

Thankfully, Gradle can still create Ant tasks (although with a different but similar syntax), which can in turn call Java classes. 

First of all, get `jsch*.jar` and `ant-jsch*.jar`. You can [download jsch](http://www.jcraft.com/jsch/), and look for ant-jsch in your filesystem with `locate` or Everything, as it comes with Ant or Java runtimes.

Starting with this [SO answer](https://stackoverflow.com/a/13205243/9296017) we need to adjust it to send folders or patterns (as opposed to single files at a time), and possibly providing a password. This is the final code in `build.gradle`:

```groovy
configurations {
    sshAntTask
}

dependencies {
    sshAntTask fileTree(dir:'[jsch jar location]', include:'jsch*.jar')
    sshAntTask fileTree(dir:'[ant-jsch jar location]', include:'ant-jsch*.jar')
}

ant.taskdef(
    name: 'scp',
    classname: 'org.apache.tools.ant.taskdefs.optional.ssh.Scp',
    classpath: configurations.sshAntTask.asPath)

task deploy() {
    doLast  {
        ant.scp(todir: '[user]:[password]@[hostname]:[destination folder path]') {
            fileset(dir: '[dir to send]') {
                include(name: '**.jar') //for example
                exclude(name: '**.xml')
            }
        }
    }
}
```

*Note: Of course, SSH keyfiles are better but it's not always that easy in a corporate environment. Just set the password as an environment variable so it doesn't get pushed to version control.*

*Also, there may be some problem including the dependency as absolute path. Make sure you use forward slashes, including on Windows, and beyond that replace the fileTree with another method.*

That's it, the deploy task should be available. It would usually depend on a build task and can ideally even replace custom deployment tasks within CI platforms for consistency.