---
title: New blog 	
date: 2018-04-23 10:31
updated: 2018-04-23 10:31
comments: true
tags:
- metablog
- programming
categories:
- metablog
permalink: new-blog-engine
---

In the end, I have realized a few things that have made Pelican the wrong platform for me:

* It's not quite popular so the themes and plugins are few, clunky and outdated. On the "nice" theme, bulleted lists don't work, and on "pelican-bootstrap3" code is not syntax-highlighted, 
* I don't need reStructuredText, as Markdown is perfect and universally supported (although less consistent as I had noted).
* Although I hope to fix this, I have little experience in Python and can't customize the templates as much as I would wish.

For these reasons, and with some conclusions in mind, I started looking at the possibilities ahead of me.

## Picking a paradigm

The first thing to do is to pick a framework and platform. These are the possibilities:

* Server-hosted CMS, e.g Lektor or Tipe on a VPS.
    * Pro: Easy file manipulation.
    * Pro: Very flexible.
    * Con: Not scalable or stable.
    * Con: More or less static. 
    * Con: Needs a server, costly and not maintained.

* File hosts or object storage platform, such as S3, Netlify or surge.sh, coupled with some script using rsync, scp or such.
    * Pro: Truly static.
    * Pro: Maintained by big companies, hence infinitely scalable and very stable.
    * Pro: Easy to use (SSL setup).
    * Con: Needs some setup.
    * Con: Still not necessarily free.

* Github Pages
    * Pro: Free.
    * Pro: well maintained.
    * Con: Not flexible (not a server, no SSL).
    * Con: Meaningless commits.

For each of these, there are many possibilities. You can look at updated frameworks [here](https://github.com/myles/awesome-static-generators) and platforms [here](https://github.com/b-long/awesome-static-hosting-and-cms).

I'm still working on figuring out the option that suits me most. I'll update this post, hopefully from another platform.