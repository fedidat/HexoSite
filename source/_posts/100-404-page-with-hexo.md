---
title: 404 Page on Hexo with Netlify
date: 2018-04-29 11:48:22
tags:
---

I made a 404 page on my Hexo site hosted on Netlify and wanted to share the steps.
I also made it a little more interesting with a game on it.

## Defining a 404 page

The handling of 404 by Netlify's server is pretty simple: it just goes to the 404.html on your root folder.

On Hexo, you can accomplish that in two ways: 

1. Make your own 404.html in your `source` folder,
which will automatically get copied to your public folder as an asset.
Keep in mind, this means it won't feature your Hexo theme.
But if you're going for a custom look anyway, that's your option.

2. If you want to keep your theme, make it a post!
Create a 404.md in your `source` folder with some minimal scaffold (just the title, or use `new hexo page`). Hexo will create a 404.html out of it.

## Integrating 2048

If we're already on the subject, I'll tell you how I used hexo's cactus
theme to embed 2048 into the page.

Disclaimer: I got this great idea from System76's 404 page. By the way, you can use the it-just-works iframe approach like [they](https://system76.com/404) did. Here's how I did it:

- First, we have to bring 2048, we can use [this package](https://www.npmjs.com/package/game-2048). So `npm i game-2048`.

- Now to bring the package to cactus: `sudo npm i -g gulp` if that isn't already the case. Then in `gulpfile.js` add a task to bring 2048 into the libs and add it to the `lib` task's dependencies:

```js
gulp.task('lib:2048',function(){
  return gulp.src([
    'node_modules/game-2048/**'
  ], {base: 'node_modules/game-2048'})
    .pipe(gulp.dest('./source/lib/game-2048'))
})
```

- Run `gulp lib` under `themes/cactus`, and now the package is in cactus's lib. Now `hexo generate` to bring it to the `public` folder.

- The default `/lib/game-2048/style/main.css` will probably wreck 
the Hexo theme because it has properties onto the body.
Tweak it.

- Finally, write your 404.md and include this HTML into it:

```html
<div id="container" class="container"></div>
<script src="/lib/game-2048/lib/index.js"></script>
<script>
document.write('<link rel="stylesheet" type="text/css" href="/lib/game-2048/style/main.css">');
window.game = new Game({
    gameContainer: document.getElementById('game-container')
});
</script>
```

- One last `hexo generate` to bring it all together and that's it.

**Update**: In the end I scrapped most of this and kept just the JS and CSS files directly in my source.
It's not like 2048 will be updated anytime soon anyway. Simplicity > all.