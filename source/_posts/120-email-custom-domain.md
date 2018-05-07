---
title: Setting up email for a personal website
date: 2018-05-02 13:10:34
tags:
---

Much of the info on the Internet about setting up an email with a 
custom domain is outdated. So I'm doing a little update
on the various ways of implementing email for a personal website.
Then I'll show how I did it.

My requirements are:

- Custom domain,

- Being notified in a mail client / app, or forwarding to my Gmail.

## The state of email

There are many services for setting up email these days:

1. **Self-hosted** (usually Postfix and Dovecot): 

    - **Home setup**: This is clearly the best solution for control and privacy. The sad truth is that it is too difficult these days to setup email for most. You would likely need to setup several daemons (SMTP, IMAP/POP, DKIM) as well as SPF rules and a spam filter. And these parts interconnect heavily, may go down, etc. So you'll probably want to look elsewhere. Unless you deal with sensitive information, but not ones that justify using end-to-end encryption such as PGP. See more info [here](https://www.digitalocean.com/community/tutorials/why-you-may-not-want-to-run-your-own-mail-server).

    - **Dedicated or VPS**: You keep the control of your own server for a fraction of the time and cost investment, but lose most of the privacy advantage. Even less worth it in my opinion.

2. **Hosted webmail**:

    - **Paid** (Google's G Suite, Office 365 Business Email, Outlook Premium, Protonmail, Rackspace, 1and1 and thousands of others): Here you start to have various degrees of service, with storage, features and privacy options being the most significant. 1and1 is the cheapest at $1/month, Protonmail claims to be the most secure,and G Suite and Office throw in an online productivity suite.

    - **Free** (Yandex, Migadu): I would have once included Zoho here. Unfortunately they have started paywalling the forwarding, POP and IMAP features, forcing users to use their webmail or mobile apps, making the platform useless to most. The remaining options are Migadu and Yandex. Yandex is what you'd expect from a huge Russian company, heavier, and you'll probably expect to leave all expectation of privacy at the door. Migadu is minimalistic, efficient and seems to care about privacy, but they insert "*Sent by Migadu*" at the end of each message and are quite limited.

![Image of Migadu's "Sent by Migadu" message at the end of each outgoing email](/images/120-email/migadu-caveat.png)

3. **Two-way forwarding service** (ImprovMX, ForwardMX.io, Mailgun): If you already have an existing mailbox (e.g Gmail) and would like to just use it to send from and receive mail to your custom domain, you can use a service that will receive your emails, forward them to your mail server and let it send emails with your domain. ImprovMX does that for free but seems very shady, without a privacy policy or owner information. Mailgun and ForwardMX.io seem more reliable, with Mailgun having a limited free tier but with the huge caveat of requiring recipients to authorize sending them emails.


## Choosing an option

As usual, I care about getting things done first and foremost.

And since I intend to use my mailbox either for non-sensitive content or for encrypted email and I consider hosted options to be equivalent,
I care about money and reliability more than privacy.

- I don't have the means to get a self-hosted server at this time, so that's out.
- Since top-notch privacy was out, paid hosted webmail looked equivalent to free.
- I quickly invalidated the forwarding services as you can see above. There's [a way](https://medium.com/issacaption/using-a-custom-domain-in-gmail-for-free-with-mailgun-and-sendgrid-2c54e681f378) to mix Mailgun for receiving and SendGrip (a mailing list service) but that's overkill and not as reliable in my opinion.
- What's left is free hosted webmail. With Migadu out, Yandex is the last candidate standing.

## Using Yandex with a custom domain

Here's how I connected Yandex to my domain, as well as set it up with Gmail to send and receive emails:

1. Sign up for [Yandex Mail](https://mail.yandex.com/) and open a normal free webmail

2. Verify your domain [here](https://domain.yandex.com/) by either putting some HTML file on your server or setting up a CNAME record. I recommend doing the former because Yandex does not detect DNS changes quickly for some reason.

3. After that you'll be told to set up an MX record. I would also setup SPF and DKIM record, you can see those by pressing "DNS editor" after having verified your domain and seeing the TXT records that Yandex delegated DNS would have created. I also created a CNAME from `mail` to `domain.mail.yandex.net.` so that my mail subdomain redirects to my Yandex webmail. This is my resulting setup with Namecheap DNS:

![My DNS records for Yandex mail](/images/120-email/records.png)

4. Complete the process on the page by setting a username. By now, the email should work with your custom domain.

## Sending and receiving with another mailbox

In general, you can use [Yandex's IMAP and SMTP info](https://yandex.com/support/mail/mail-clients.html) to configure desktop or mobile clients. I use Gmail so my setup will be a little different.

Let's start with the way to setup forwarding from Yandex to another address. 

1. Head to Yandex's webmail, click on the gear on the top right and then on message filtering.

![Message filtering on Yandex mail](/images/120-email/find-filtering.png)

2. Setup a new filter that forwards all mail to your second mailbox.

![New filter on Yandex mail](/images/120-email/filter.png)

3. MAke sure it looks something like this:

![Filter result on Yandex mail](/images/120-email/filter-result.png)

Now with the other direction. I use Gmail so this is how you set it up to send mail with through another SMTP server, specifically Yandex's. 

1. Head to Gmail's settings > Accounts & Import, focus on "Send mail as", click on "Add another email address". 

2. Provide the user you use on Yandex, then [Yandex's SMTP server](https://yandex.com/support/mail/mail-clients.html) and your login info. Make sure to uncheck using this address as an alias.

That should be it. Test your setup and you're done.