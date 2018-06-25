---
title: 'Docker load: "error processing tar file... unexpected EOF"'
date: 2018-06-25 08:13:51
tags:
---

While passing around some images with save and load (not to be confused with export and import for containers), I have encountered the following error:

```
Error response from daemon: Error processing tar file(exit status 1): unexpected EOF
```

It turns out that the image was malformed, which I discovered using `md5sum [file]` and comparing between the source and destination archive.