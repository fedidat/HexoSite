---
title: Setting the Java locale from Linux
date: 2018-07-01 19:28:43
tags:
---

We got new servers at work, and since none of the original team is left,we were puzzled as to why some non-ASCII characters in our Java logs showed up as gibberish, and why Java had trouble opening files with non-ASCII names dynamically.

To clear up the situation:

1. Check the locale on the server with the current `locale` (this is the same as `echo $LANG`). Make sure that there is no conflict between that and the encoding of your shell or file names. You can check the current encoding on the server with `locale charmap`, and the one of your shell is probably defined in your terminal, for example in PuTTY under "Configuration > Window > Translation > Character set".

2. If the locale is incorrect, check the available locales and charsets using `locale -a`, you can even [define your own](https://www.cyberciti.biz/faq/how-to-set-locales-i18n-on-a-linux-unix/).

3. Most importantly, if you have made any change, **reboot**. In case of doubt, reboot! Out IT team had made the change but had not performed a reboot, so it took us hours to understand why Java was not picking up the locale. It did, it's just Linux that was still using the old one.