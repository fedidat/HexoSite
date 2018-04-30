---
title: Setting up email for a personal website
date: 2018-04-30 14:20:52
tags:
---

Much of the info on the Internet about setting up an email with a 
custom domain is outdated. So I'm doing a little update
on the various ways of implementing email for a personal website.
Then I'll show how I did it.

## The state of email

There are many ways of setting up email these days:

- Self-hosted (usually Postfix and Dovecot): This is clearly the best solution for control and privacy. The sad truth is that it is too difficult these days to setup email for most. You would likely need to setup several daemons (SMTP, IMAP/POP, DKIM) as well as SPF rules and a spam filter. And these parts interconnect heavily, may go down, etc. So you'll probably want to look elsewhere. Unless you deal with sensitive information, but not ones that justify using end-to-end encryption such as PGP.

- Hosted webmail:

    - paid (e.g Google's G Suite, Office 365 Business Email, Protonmail, Rackspace, 1and1):

    - free (Yandex):

- Forwarding service (ImprovMX):