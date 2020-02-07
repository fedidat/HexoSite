---
title: Missing files with non-UTF8 filenames on volume mount
date: 2020-02-07 11:08:36
tags:
---

I've had trouble on a server with handling non-UTF8 filenames. No matter what I used, `ls`, `vi`, `touch`, I could not find non-UTF8 files created on another server.

This was a problem with the volume mount. On the server with working filenames, the mount was NFSv3, which does support other encodings. NFSv4, as used by the mount on the other server, [only supports UTF-8 filenames](https://news.ycombinator.com/item?id=16991263).

One solution is to fix the mount to use NFSv3:

```bash
sudo mount -t nfs -o vers=3 network-drive:/path/to/folder /mnt/nfsv4
```

This is not always available or transparent, unfortunately. A more correct but potentially problematic solution is to convert filenames to UTF-8 using the `convmv` util, for example:

```bash
convmv -f latin8 -t utf-8 -r /mnt/nfsv3
```
