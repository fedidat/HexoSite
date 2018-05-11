---
title: Installing and using fonts with Pandoc
date: 2018-05-11 11:56:30
tags:
---

I'm still working on making my Markdown resume with Pandoc.

In the process, I have struggled a lot with making Hebrew fonts work while generating LaTeX, but Pandoc seems to have trouble with non-English characters globally, because its default font appears to be missing glyphs, at least on Ubuntu 16.04.

So how do you install fonts? 

* Download some .ttf font.
* Put it in `~/.fonts`.
* Run `sudo fc-cache -fv` and confirm the font has been loaded with `fc-list | grep [font name]`

Then, add the mainfont variable to the Pandoc command: `-V mainfont="[font name]"`.
