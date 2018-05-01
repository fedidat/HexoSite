---
title: Setting up email for a personal website
date: 2018-04-30 14:20:52
tags:
---

Much of the info on the Internet about setting up an email with a 
custom domain is outdated. So I'm doing a little update
on the various ways of implementing email for a personal website.
Then I'll show how I did it.

My requirements are:

- Custom domain

- Being notified in my mail client / app.

## The state of email

There are many ways of setting up email these days:

1. **Self-hosted** (usually Postfix and Dovecot): 

    - **Home setup**: This is clearly the best solution for control and privacy. The sad truth is that it is too difficult these days to setup email for most. You would likely need to setup several daemons (SMTP, IMAP/POP, DKIM) as well as SPF rules and a spam filter. And these parts interconnect heavily, may go down, etc. So you'll probably want to look elsewhere. Unless you deal with sensitive information, but not ones that justify using end-to-end encryption such as PGP. See more info [here](https://www.digitalocean.com/community/tutorials/why-you-may-not-want-to-run-your-own-mail-server).

    - **Dedicated or VPS**: You keep the control of your own server for a fraction of the time and cost investment, but lose most of the privacy advantage. Even less worth it in my opinion.

2. **Hosted webmail**:

    - **Paid** (Google's G Suite, Office 365 Business Email, Outlook Premium, Protonmail, Rackspace, 1and1 and thousands of others): Here you start to have various degrees of service, with storage, features and privacy options being the most significant. 1and1 is the cheapest at $1/month, Protonmail claims to be the most secure,and G Suite and Office throw in an online productivity suite.

    - **Free** (Yandex, Migadu): I would have once included Zoho here. Unfortunately they have started paywalling the forwarding, POP and IMAP features, forcing users to use their webmail or mobile apps, making the platform useless to most. The remaining options are Migadu and Yandex. Migadu is minimalistic, efficient and seems to care about privacy, but they insert "*Sent by Migadu*" at the end of each message and are quite limited. Yandex is what you'd expect from a huge Chinese company, heavier, and you'll probably expect to leave all expectation of privacy at the door.

3. **Two-way forwarding service** (ImprovMX, ForwardMX.io, Mailgun): If you already have an existing mailbox (e.g Gmail) and would like to just use it to send from and receive mail to your custom domain, you can use a service that will receive your emails, forward them to your mail server and let it send emails with your domain. ImprovMX does that for free but seems very shady, without a privacy policy or owner information. Mailgun and ForwardMX.io seem more reliable, with Mailgun having a limited free tier.