---
title: 'Blog comment systems: Disqus alternatives'
date: 2018-10-17 22:21:22
tags:
---

Disqus is everywhere. And it does the job: insert a `<script>` tag and even a simple HTML page gets a comment system. What else could you ask for?

A lot:

- Control. Some feature missing from Disqus? Too bad.
- No tracking: Who knows what they collect about your users and what they do with their information.
- A lighter system: Disqus is a metric ton of JavaScript and inflates your page by a few megabytes.
- And it has many other [problems](https://notes.peter-baumgartner.net/2017/09/14/alternatives-for-disqus/). See [this](http://donw.io/post/github-comments/) as well for some concrete findings.

This is why I turned Disqus off 3 months back on this site. But I had an itch, I would still like the possibility for someone to drop comments other than personal emails or such. These are my findings.

## The alternatives

My starting points for reviewing systems are [this blog article](https://notes.peter-baumgartner.net/2017/09/14/alternatives-for-disqus/), [this HN thread](https://news.ycombinator.com/item?id=15511297) and [alternatives.to](https://alternativeto.net/software/master-comments-system/). Here, I will be reviewing the options in more depth.

Note: I will intentionally skip proprietary alternatives, as they typically share Disqus's issues. By proprietary, I mean software without any open-source component. I will also omit discontinued and unmaintained projects to avoid more problems down the line.

### Discourse

![Discourse](/images/530-blog-comments/discourse.png)

This one sits on the edge of overkill. [Discourse](https://www.discourse.org/) is a growing company that also proposes its community discussion system as [FOSS](https://github.com/discourse/discourse). It is written in Ruby.

Expect quality software, but also some inadequacy regarding the scale of the tool. Embedding is done by creating an iFrame on the host site (example at [Jeff Atwood's Coding Horror](https://blog.codinghorror.com/why-ruby/#discourse-comments)). From there, a "continue discussion" button takes the user to a full-fledged forum. Then you have to deal with potential issues of styling, cross-origin policy, configuration, etc. I don't think it's worth it.

### Commento

![Commento](/images/530-blog-comments/commento.png)

[Commento](https://commento.io/) has the right idea: a simple and lightweight but beautiful thread system using an embedding script. It also has a [community edition](https://github.com/adtac/commento-ce) sponsored by Mozilla and Digital Ocean and a [docker-compose installation](https://docs.commento.io/installation-docker.html). Its server-side is in Golang.

Unfortunately, Commento is not production-ready just yet as of two months ago. Crucial things were either missing or not working, like admin authentication or domain creation restriction. The integration was difficult as well, despite the Docker image helping a great deal.

On the upside, the client side is the best in my opinion. Polishing the server side would easily make Commento the best option.

### Schnack

![Schnack](/images/530-blog-comments/schnack.png)

[Schnack](https://schnack.cool/) is even lighter than the others, with an embedding script of 8KB. It is entirely [open-source](https://github.com/schn4ck/schnack) and has a Docker image. Its server-side is Node.

One large pitfall lies in the need for users to authenticate using third-party services: Github, Twitter, Google or Facebook. On the one hand, this means the server-side does not need to have an authentication system. On the other, I would rather users be able to comment without using other proprietary services that may track them.

Ultimately, I almost went for Schnack but I found out too late lacked a crucial feature: editing and removing comments. An [issue](https://github.com/schn4ck/schnack/issues/8) has been open for over a year.

### Isso

![Isso](/images/530-blog-comments/isso.png)

[Isso](https://posativ.org/isso/) is similar in many regards to Shnack: its open-source server-side runs in Docker and it has a simple embedding script. 

It is different from Schnack in several regards: it is written in Python and its embedding script is heavier at 40KB, although it makes up for it by not requiring third party authentication, instead allowing anonymous comments. I actually prefer this account model, as I mentioned in opposition to Schnack's.

### Staticman (pull request to Github)

![Staticman](/images/530-blog-comments/staticman.png)

[Staticman](https://staticman.net/) is the odd man out. It aims to provide comments - which are dynamic content - in a static fashion. Or rather, it uses Github as the server-side that generates the comments and stores them in your repo. On the paper, this is great for static sites as it means potentially not needing a personal server, for example by using Netlify (such as my site) or Github Pages.

The way this works is that Staticman has an API at https://api.staticman.net (or you can [host it yourself](https://github.com/eduardoboucas/staticman)) that your site sends POST requests to. Staticman then opens a pull request to your site's repo.

The tricky part is that Staticman does not come with an embedding script. So you'll have to either find someone who has done it before you (examples with Jekyll [[1]](hthttps://staticman.net/demo) [[2]](https://github.com/eduardoboucas/staticman-recaptcha) and Hugo [[1]](https://github.com/eduardoboucas/hugo-plus-staticman) [[2]](https://binarymist.io/blog/2018/02/24/hugo-with-staticman-commenting-and-subscriptions/)) or do the integration yourself. Integration means implementing both the submitting and serving components. So note that the picture above may not represent your end result at all.

Another problem is that Staticman locks you in with Github. However, Gitlab support is [coming soon](https://github.com/eduardoboucas/staticman/issues/22). And since Gitlab is open-source and can be self-hosted, this would make Staticman a decent option.

### utterances (Github issues)

![Utterances](/images/530-blog-comments/utterances.png)

The [article](http://donw.io/post/github-comments/) mentioned in the introduction also talks about integrating Github issues. This has been done with a very simple embedding script by [utterances](https://utteranc.es/). It is flexible, reliable and simple. It is [open source](https://github.com/utterance/utterances) and coded in TypeScript. As you can see, its style mimics Github's.

My main gripe is once again with the Github lock-in. I thought about reimplementing the API calls to the [Gitlab issues API](https://docs.gitlab.com/ee/api/issues.html) but I would rather my main repo stay on Github for the time being.

### Others

I have also reviewed other small systems such as [jskomment](https://github.com/monperrus/jskomment) (not reliable), [HashOver](https://github.com/jacobwb/hashover) (not reliable, but keep an eye on [HashOver v2](https://github.com/jacobwb/hashover-next), under development and promising). Conversely, huge systems like Mozilla's [Coral Project Talk](https://coralproject.net/talk/) are well-funded and robust but way too complex for anything under enterprise-scale.

## My pick: Isso

As you can see, the choice can get technical, and there is no clear winner. Important factors are the scale of your site, how much time you want to dedicate to the setup process, and which features matter to you.

I ended up picking Isso, which is the least-effort solution that provides the features I wanted:

- Open source and lightweight,
- Simple embedding script,
- Ability to reply to comments as well as edit and delete them,
- Comment notifications (by email),
- Moderation features: comment approval, notifications, spam filtering.

### Installing Isso

Isso's configuration can get involved, as there are [many settings](https://posativ.org/isso/docs/configuration/server/). The best way to set it up for me was to install the [Debian package](https://packages.crapouillou.net/), which also comes with a default configuration and global install that allows you to hook it up to this [systemd service unit](https://github.com/jgraichen/debian-isso/blob/master/debian/isso.service). This allows easy management and automatic restart.

Overall, the [Docker image](https://hub.docker.com/r/wonderfall/isso/) may be a better option but my OpenVZ VPS doesn't run it. The Debian package's [sample TOML config](https://github.com/jgraichen/debian-isso/blob/master/debian/isso.conf) may be useful to mount if you go down the Docker road.

Keep in mind that the most important setting to change is `host`, which should be the domain of the site embedding the comments, e.g https://fedidat.com/ in my case.

### Server configuration

This is my Nginx proxy/forwarding file with TLS and HSTS support. I used Certbot to generate the certificate.

```nginx
server {
    location / {
            proxy_pass http://127.0.0.1:8000;
    }

    #The rest of the file was generated by Certbot
    listen [::]:443 ssl ipv6only=on;
    listen 443 ssl;
    ssl_certificate /etc/letsencrypt/live/comment.fedidat.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/comment.fedidat.com/privkey.pem;
    include /etc/letsencrypt/options-ssl-nginx.conf;
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem;
}
server { #redirect to HTTPS
    if ($host = comment.fedidat.com) {
        return 301 https://$host$request_uri;
    }
    listen 80 ;
    listen [::]:80 ;
    server_name comment.fedidat.com;
    return 404;
}
```

Also, if using the ufw firewall, allow HTTPS traffic by running this:

    sudo ufw allow "Nginx HTTPS"

### Embedding

Embedding Isso comments in a site is as simple as this:

```js
<script data-isso="//comments.example.com/"
        src="//comments.example.com/js/embed.min.js"></script>
<section id="isso-thread"></section>
```

There are many more customization features on [the documentation](https://posativ.org/isso/docs/quickstart/).