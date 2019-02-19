---
title: Migrating from font-awesome 5 to fork-awesome 1.0.0
date: 2018-04-30 23:42:17
tags:
---

Font-Awesome is excellent in the concept - delivering icons in the form of font files, following in the wake of Bootstrap with responsiveness.

In practice, I don't think it's such a great idea and increases page load time for no good reason. 
For example, icon fonts for the share buttons represent about half of the size of the page you're looking at... 
But at least if you have to do it - do it right.

Font-Awesome has apparently taken a turn for the worse in recent years by denying all pull requests, among other questionable decisions.
So some good folks have forked it and made fork-awesome. Here's a quote from their README.md:

>Following concerns regarding [the development of Font Awesome](https://github.com/FortAwesome/Font-Awesome/issues/12199#issuecomment-362919956), the PR Freeze since Oct 2016 and the direction [Fort Awesome](https://fortawesome.com/) is taking with the version 5.0 of their project, we are forking Font Awesome (4.7), in order to build on this incredible tool Dave Gandy has given us [...].

In addition, Fork-Awesome have accepted pull requests for many excellent icons such as Keybase, XMPP or Debian.
And unlike Font-Awesome, it's all free and open-source!

So how do you migrate?

## 1. Move the files.

In your gulpfile or whatever you use, bring the bare necessary from the node module to your page:

```js
gulp.task('lib:fork-awesome',function(){
  return gulp.src([
    'node_modules/fork-awesome/fonts/*',
    'node_modules/fork-awesome/css/fork-awesome.min.css'
  ], {base: 'node_modules/fork-awesome'})
    .pipe(gulp.dest('./source/lib/fork-awesome'))
})
```

## 2. Rename the file import

You will need to rename the CSS file import in your pages. This is what it looked like in my `cactus` theme's styles.ejs:

```diff
- <%- css('lib/font-awesome/css/fontawesome-all.min') %>
+ <%- css('lib/fork-awesome/css/fork-awesome.min') %>
```

## 3. Adapt the references

The real reason I made this article was the non-obvious stuff: the API differences. These result from the upgrade of Font-Awesome from 4.7 to 5 which will not be fully pulled to Fork.

Fork-Awesome mostly kept the same icon names this far. Categories don't exist here however. No brand, regular or solid ("pro" paid fonts) as in Font-Awesome 5. 
So you'll have to convert `fab`, `fas`, `far` and `fal` class names back to `fa`.

The only other significant point is that you might be using weights that aren't present in Fork,
such as `fa-xs`, `fa-sm`, `fa-7x` and `fa-10x`. Those have no alternatives for now other than straight upscaling.

## 4. Examples and references

Finally, I recommend you take a look at some [Fork-Awesome examples](https://forkawesome.github.io/Fork-Awesome/examples/) for a very quick review of usage.