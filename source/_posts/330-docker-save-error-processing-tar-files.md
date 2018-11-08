---
title: 'Docker load: "error processing tar file... unexpected EOF"'
date: 2018-06-25 08:13:51
tags:
---

While passing around some images with save and load (not to be confused with export and import for containers), I have encountered the following error:

    Error response from daemon: Error processing tar file(exit status 1): unexpected EOF

It turns out that the image was malformed, which I discovered using `md5sum [file]` and comparing between the source and destination archive.

**Update Nov. 2018**: Despite my efforts on other posts on this blog, my shortest and simplest article is the one that gets the most hits by far. That's probably because it's currently the only search result with this cryptic error string.

If file malformation is or isn't the cause for this error on your end, please consider leaving a comment so I can write about it at length here. For now however, I can't think of another reason for this issue (other than maybe `docker save` being interrupted in the middle), and `md5` seems like a reasonably accurate way to check for that symptom, as far as I can tell.