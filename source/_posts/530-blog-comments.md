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

### remark42

![remark42](/images/530-blog-comments/remark.png)

[remark42](https://remark42.com/) is a sleek system that is full of features. It is [open source](https://github.com/umputun/remark), written in Go and does a lot of things right, like privacy, styling, security or packaging. It may be installed through docker-compose, and its same author also proposes a docker image that serves virtual hosts with Nginx and Certbot for HTTPS.

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

### Cusdis

![cusdis](/images/530-blog-comments/cusdis.png)

Cusdis is a nice and modern system but I feel the setup is not currently stable and mature enough to self-deploy. It does offer a free tier but so does Disqus.

More opinions on this [HackerNews thread](https://news.ycombinator.com/item?id=26878153).

### Others

I have also reviewed other small systems such as [jskomment](https://github.com/monperrus/jskomment) (not reliable), [HashOver](https://github.com/jacobwb/hashover) (not reliable, but keep an eye on [HashOver v2](https://github.com/jacobwb/hashover-next), under development and promising). Conversely, huge systems like Mozilla's [Coral Project Talk](https://coralproject.net/talk/) are well-funded and robust but way too complex for anything under enterprise-scale.

## My pick: remark42

After years with Isso, then removing comments while tinkering around for a better setup, remark42 became a mature solution, although its documentation could be somewhat clearer.

The simplest way to put together the backend is to get a server and set it up with a dedicated subdomain. Then use the following `docker-compose.yml`:

```yaml
version: '2'

services:
    remark42:
        build: .
        image: umputun/remark42:latest
        container_name: "remark42"
        restart: always
        environment:
            - REMARK_URL=http://[YOUR_CUSTOM_SUBDOMAIN]
            - SECRET=codewithoutcoffee
            - STORE_BOLT_PATH=/srv/var/db
            - BACKUP_PATH=/srv/var/backup
            - DEBUG=true
            - SITE=[YOUR_SITE_ID]
            - AUTH_ANON=true
            #- AUTH_GOOGLE_CID
            #- AUTH_GOOGLE_CSEC
            #- AUTH_GITHUB_CID
            #- AUTH_GITHUB_CSEC
            #- AUTH_FACEBOOK_CID
            #- AUTH_FACEBOOK_CSEC
            #- AUTH_DISQUS_CID
            #- AUTH_DISQUS_CSEC
        volumes:
            - ./var:/srv/var

    nginx:
       build: .
        image: umputun/nginx-le:latest
        hostname: nginx
        restart: always
        container_name: nginx
        links:
          - "remark42"
        volumes:
            - /etc/ssl:/etc/nginx/ssl
            - [PATH_TO_NGINX_CONF]
        ports:
            - "80:80"
            - "443:443"
        environment:
            - LETSENCRYPT=true
            - LE_EMAIL=[YOUR_EMAIL]
            - LE_FQDN=[YOUR_CUSTOM_SUBDOMAIN]
```

replacing YOUR_EMAIL, YOUR_CUSTOM_SUBDOMAIN and writing whatever you want for YOUR_SITE_ID, then using this file from https://github.com/umputun/remark42/blob/master/backend/nginx-proxy-example.conf into PATH_TO_NGINX_CONF:

```nginx
server {
    listen   443;
    server_name [YOUR_CUSTOM_SUBDOMAIN];

    ssl    on;
    ssl_certificate         SSL_CERT;
    ssl_certificate_key     SSL_KEY;
    ssl_trusted_certificate SSL_CHAIN_CERT;

    add_header Strict-Transport-Security "max-age=63072000; includeSubdomains; preload";

    limit_conn perip 10;

    location / {
         proxy_redirect          off;
         proxy_set_header        X-Real-IP $remote_addr;
         proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
         proxy_set_header        Host $http_host;
         proxy_pass              http://remark42:8080/; #from the container name
     }
}
```

Finally integrate into your website using this simple snippet, as used on this website:

```html
    <div id="remark42"></div>
    <script>
        var remark_config = {
          host: "https://[YOUR_CUSTOM_SUBDOMAIN]",
          site_id: '[YOUR_SITE_ID]',
          components: ['embed'],
          //theme: 'dark', //yes, it has a dark theme now!
        };
      </script>
      <script>!function(e,n){for(var o=0;o<e.length;o++){var r=n.createElement("script"),c=".js",d=n.head||n.body;"noModule"in r?(r.type="module",c=".mjs"):r.async=!0,r.defer=!0,r.src=remark_config.host+"/web/"+e[o]+c,d.appendChild(r)}}(remark_config.components||["embed"],document);</script>
```

And that should be it. Make sure to check the [remark42 README](https://github.com/umputun/remark42/blob/master/README.md) for any further details.

Note: I remain jealous of Dmitry Shuryalov's [site](https://dmitri.shuralyov.com/blog) in Golang with cute reactions.
