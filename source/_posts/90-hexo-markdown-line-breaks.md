---
title: Single line breaks in Hexo
date: 2018-04-25 11:34:18
tags: blog
---

If you're like me, you would rather input line breaks rather than
rely on your editor's line wrapping.

Usual Markdown accounts for this by skipping single line breaks,
and instead only inserting `<br>` (or equivalent) for double line breaks.

Vanilla Hexo ignores this, and single line breaks do break lines in the output. In principle, you should change the config in the hexo-renderer-marked module. Thankfully, there's another way. Insert this in your `_config.yml`:

```js
marked:
  gfm: true
  breaks: false
```