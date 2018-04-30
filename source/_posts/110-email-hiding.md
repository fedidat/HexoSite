---
title: Email obfuscation
date: 2018-04-30 11:58:33
tags:
---

Bots have been scraping email addresses on the Internet for many years now.
And since spam is still veyr much a problem these days, it was time I
started obfuscating my address.

## The compromise

Obfuscating an address from bots is a compromise between security and user experience.

After looking for a while on [StackExchange](https://stackoverflow.com/questions/23002711/how-to-show-email-addresses-on-the-website-to-avoid-spams) 
and other places, it seems like the best way to do it is:

1. Use Javascript to make a mailto link, which is now well supported in browsers and creates the best experience
while remaining unprocessed by bots, since very few enable Javascript.

2. Keep an image backup for users without Javascript.

## Implementation

First, generate the image, preferably locally. I prefer not to rely on image generators. MS Paint (or KolourPaint under Linux) do a great job
and you don't risk providing your address to yet another database. Then place it in your link.

Then, give your link div an `id` and place something like this next to your link:

```html
<script>
    var user = 'user',
    word = "mail" + "to:",
    domain = 'example' + '.' + 'com',
    element = document.getElementById('address-div');
    element.setAttribute("href", word + user + '@' + domain);
</script>
```

That way even bots that skim over the Javascript file will have trouble making sense of this.

Next I'll be setting up my custom domain email and maybe Netlify CMS,
then I hope to get back to speaking about backend stuff.