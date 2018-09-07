---
title: Better analytics with GoAccess and Matomo (Piwik)
date: 2018-08-20 18:51:01
tags:
---

I have been ranting for a while lately about having to use Google Analytics. I hope this will be over sometime soon, as I will switch to better analytics.

## Why?

### 1. Privacy

The first reason is obvious. Google is the worldwide king of data, and it's a good thing if we can help from donating more of it when there is no good reason for it other than convenience.

### 2. Reliability

Many people - including myself - use ad blockers, and rightfully so. But private analytics usually do not track you for any malicious purpose, and only benefit the users, as site owners will use the information to give users a better service. Ad blocking tools know this and blocking lists will often give private analytics a pass, making it more likely for owners to understand their users. Note that your mileage will vary, as uBlock Origin seems to block Piwik.

### 3. Security

Robots, hackers and the like, either disable Javascript or try their best to remain stealthy. This is a problem that can be solved with the way analytics was meant to be done: server side. I was amazed when I saw the amount of requests to suspicious URLs being performed by suspicious IPs like /phpMyAdmin. Scary stuff, keep your servers secure!

### 4. Openness

I am a fervent supported of open source, and I have a hard time suggesting users to run third-party scripts that contain who-knows-what. It's the browser equivalent of `sudo curl | sh`.

## GoAccess

Let's start with the most correct solution: server-side access log analytics with [GoAccess](https://goaccess.io/). It is an excellent piece of software done right.

Installing the latest package from their repo on Debian is simple (otherwise look [here](https://goaccess.io/download)):

``` bash
echo "deb http://deb.goaccess.io/ $(lsb_release -cs) main" | sudo tee -a /etc/apt/sources.list.d/goaccess.list
wget -O - https://deb.goaccess.io/gnugpg.key | sudo apt-key add -
sudo apt-get update
sudo apt-get install goaccess
```

Then, if you use Apache or Nginx like 99% of owners, you can do the following to get a command-like ncurses report (make sure to `chmod` the logging folder, e.g /var/log/apache/ or /var/log/nginx/):

``` bash
goaccess access.log -c --log-format=COMBINED
```

If all goes well, you can go on to generate the very neat HTML report, with filtering and other features:

``` bash
goaccess access.log -a -o /path/to/report.html --log-format=COMBINED
```

Here is a quick preview of the HTML report on a VPS that had been running for a night:

![GoAccess preview](/images/453-better-analytics/goaccess-small.png)

Or even better, use the real-time server to always have access to the latest report (make sure the port is open in your firewall):

``` bash
goaccess access.log --real-time-html -o /path/to/report.html --log-format=COMBINED --port=7890
```

Security is an important issue here. I suggest either setting up `cron` to generate and email the report to you, or to put the report on a special path protected with a basic_auth or .htaccess login. After all, take a look at what GoAccess caught after just a night of Nginx running on a VPS with a firewall:

![GoAccess hax](/images/453-better-analytics/goaccess-hax.png)

If you have an issue, refer to GoAccess' [get started](https://goaccess.io/get-started) page.

Of course, GoAccess has its downsides. It cannot discriminate between requests to a font or 404 page and actually interesting hits to a post like Google Analytics can. This is a job for Matomo!

## Matomo (formerly Piwik)

First of all, this is an open-source self-hosted alternative to Google Analytics that started in 2007, functioning much the same way as GA, with a Javascript tracker snippet and a dashboard backend.

Piwik recently split into Matomo and Piwik Pro. There was a legal fight with the owners of the trademark, and the open source folks had to fork their superior product and rebrand it as [Matomo](https://matomo.org/). Fair enough.

So, how should you install Matomo? I started to write a lengthy guide and got into problems with Nginx FastCGI, plus the packages got messy. So I'll just recommend you use this [excellent docker-compose](https://github.com/auchri/docker-nginx-piwik).

This comes with MariaDB and Nginx. If you don't want to run a dedicated server on another port, and instead you want to have example.com/piwik, use the simple [matomo image](https://hub.docker.com/_/matomo/) and use your main server as a reverse proxy to that.

Here is a quick preview of Matomo:

![Matomo dashboard](/images/453-better-analytics/altpi.png)

## Alternatives

Sadly (or fortunately, depending on your opinion), there are few alternatives for open source self-hosted trackers. I will mention [count.ly](https://github.com/Countly/countly-server) for having a modern Node.js stack and an actual Docker image and script suite. Sadly, it is heavier than Matomo, being a full-fledged process, and will refuse to run under 2GB of RAM.

Here is an image of the Dashboard UI from their Github:

![Countly dashboard](/images/453-better-analytics/countly.png)

## Conclusion

Depending on convenience, ethics and dedication, you may want to implement any of these solutions. I believe UNIX philosophy heavily leans towards GoAccess being the correct choice and encourage you to use it if you control your web server.