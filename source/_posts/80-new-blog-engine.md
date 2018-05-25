---
title: Migrating From Pelican to Hexo	
date: 2018-04-23 10:31
updated: 2018-04-24 23:55
comments: true
tags:
- blog
- meta
- programming
categories:
- blog
permalink: new-blog-engine
---

In the end, I have realized a few things that have made Pelican the wrong platform for me:

* It's not quite popular so the themes and plugins are few, clunky and outdated. For instance, on the theme called "nice", bulleted lists don't work, and on "pelican-bootstrap3" code is not syntax-highlighted, 
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
    * Con: Not flexible (not a server, no SSL). UPDATE: Github [added easy SSL](https://blog.github.com/2018-05-01-github-pages-custom-domains-https/) a week after I wrote this article.
    * Con: Meaningless commits.

For each of these, there are many possibilities. You can look at updated platforms [here](https://github.com/b-long/awesome-static-hosting-and-cms).

## Settling with a framework

At the same time, I mostly needed a new platform, although these two concerns may be intertwined. I started looking at [this comprehensive list](https://github.com/myles/awesome-static-generators) of static site generators, although you can find many other resources like the famous but less helpful [StaticGen](https://www.staticgen.com/). 

Long story short, I took these two choices into account and started looking closer and closer at [Hexo](hexo.io).
Hexo is written in Javascript, which is modern and ubiquitous, and pretty well-understood with little effort.
More specifically, Hexo's project structure makes a lot of sense: 
* the usual `node_modules/`
* `source/`, containing:
    * `_posts/` with the Markdown posts created by Hexo, or imported by yourself in the proper format.
    * anything else, including pages, assets, documents,  etc... which will be copied to the site's root.
* `scaffold/` with very simple templates, by default draft.md, page.md and post.md, with about 3 lines each for the title, date and tags.
* `themes/` where each subdirectory contains a Hexo theme. If you clone themes directly from Git, be careful to remove the .git folder, otherwise the theme will be treated by Git as a submodule and will not be committed.
* a generated `public/` folder containing the static result of your site.
* `_config.yml`, the Hexo config file.

And the workflow is amazing: `hexo new [scaffold type] [name]` to create a page, `hexo serve` while developing with auto-reload and `hexo generate` to generate the public folder.

Those are really features I can appreciate coming from Pelican.

More on themes: I chose Cactus at the time of writing, a simple but pretty dark theme, which exists in other colors. It is compatible with many Hexo plugins like syntax highlighters or RSS feeds, and is easily customizable and extensible. But there are really many themes.

Now it's not all sunshine and roses. Hexo's main community is Chinese, which may be a problem later and may overall hinder its adoption. Their themes are also not that great overall. And Hexo is relatively behind in terms of technology, next to sites based on Gatsby, Next or Nuxt for example. But these are really insignificant issues for me. And I can switch anytime, thanks to the omnipresence of Markdown and the number of static site generators.

## Choosing the platform

I wanted to get going very fast. [Netlify](https://www.netlify.com) imposed itself as the best platform by far, being specialized, comprehensive, and most of all free.

In no specific order, here are some awesome features of Netlify: a CMS platform with provided authentication APIs, a DNS server, automatic LetsEncrypt provisioning, and most of all a process that way is too simple: define your deployment as a Github/Gitlab URL, specify a command (`hexo generate` in our case), an output folder (`public` with Hexo) and... that's it! Netlify with listen to pushes and deploy your website. The concept is genius and just as simple as they claim!

Now nothing is really free, so let's look at how much lock-in I got myself into. Overall, I can go back to Github pages easily anytime, and I could switch to AWS S3 or my VPS with little work. As for Hexo,Netlify is transparent and works with anything that generates static content. So I'm in a really comfortable position.

Now excuse me as I just commit and push and let the zero-effort CI/CD take care of the rest. Thanks for reading!