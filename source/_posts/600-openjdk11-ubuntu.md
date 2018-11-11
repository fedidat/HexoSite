---
title: Installing Java 11 on Ubuntu
date: 2018-11-10 22:43:27
tags:
---

Up to last month, installing Java 11 was only possible by either:

- Installing Oracle's JDK (HotSpot JVM), with its business and proprietary implications.
- Downloading and manually setting up OpenJDK 11.

This still held true after Java 11's General Availability on Sep. 24 2018.

Last month, OpenJDK's repository was finally updated with an up-to-date package for Ubuntu using their PPA:

```bash
sudo add-apt-repository ppa:openjdk-r/ppa
sudo apt-get update
sudo apt install openjdk-11-jdk
```

Check with `java -version` (although `java --version` should now work, if not you are on an earlier JDK).

If Java was manually set to another version, run:

```bash
sudo update-alternatives --config java
```

And either select OpenJDK 11 manually, or choose 0 for auto mode, i.e automatically update when (re)installing JDK packages.