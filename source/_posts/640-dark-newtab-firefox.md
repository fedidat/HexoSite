---
title: Dark background in Firefox 64's new tabs without the white flash
date: 2019-01-03 23:11:59
tags:
---


Since I tend to program on the bus at night, and I can't lower my laptop's brightness to a decent level,
I have made most things have a dark background. In particular, I use XFCE's X-Arc-Shadow theme and Firefox's [Dark Reader extension](https://addons.mozilla.org/en-US/firefox/addon/darkreader/) (FOSS, customizable, per-site, sepia whites).

Then something bothered me yesterday as I was blinded by the brightness of opening a new tab on Firefox.
So I've tried to get my Firefox 64.0 (Quantum) to display new tabs with a dark background.

Many superfluous extensions (e.g [New Tab Override](https://addons.mozilla.org/en-US/firefox/addon/new-tab-override/)) 
try to do it just as well as userContent.css advices that are all over the Internet. Or even better, you can get the same result
just by going to `about:config` and editing `browser.display.background_color` to an acceptable color.

But this all leaves a blinding white flash for half a second before Firefox loads the edited background color.

Eventually, I found the solution on a Reddit [thread](https://old.reddit.com/r/FirefoxCSS/comments/83grjw/white_flash_is_back_in_firefox_60_can_someone_fix/)
using [ShadowFox](https://github.com/overdodactyl/ShadowFox), which aims to be a dark retheme of Firefox, in particular 
[this](https://github.com/overdodactyl/ShadowFox/blob/master/css/userChrome-files/remove_white_flash.css) file.

To sum it all up, the complete process is this:

* In `about:config`, change `browser.display.background_color` to the color of your choice,
* head to Firefox's profile folder by finding it in `about:profiles` under "Root Directory",
* go to the `chrome` subfolder, open/create `userChrome.css` and append the following CSS
  (replacing `#1D1B19` by the color of your choice):
    ```css
    #browser vbox#appcontent tabbrowser, #content, #tabbrowser-tabpanels, 
    browser[type=content-primary],browser[type=content] > html {
        background: #1D1B19 !important
    }
    ```