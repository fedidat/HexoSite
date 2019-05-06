---
title: IntelliJ not remembering passwords
date: 2019-05-05 18:59:35
tags:
---

I installed IntelliJ Ultimate on a new Mac and remarked that it would not remember database passwords, no matter what:

>The specified database user/password combination is rejected: [28000][10100] [Database]\[JDBC](10100) Connection Refused: [Database]\[JDBC](11640) Required Connection Key(s): PWD; [Database]\[JDBC](11480) Optional Connection Key(s): AccessKeyID, AuthMech, AutoCreate, BlockingRowsMode, ClusterID, DbGroups, DisableIsValidQuery, DriverLogLevel, EndpointUrl, FilterLevel, IAMDuration, Language, loginTimeout, OpenSourceSubProtocolOverride, plugin_name, profile, Region, SecretAccessKey, selectorProvider, selectorProviderArg, SessionToken, socketTimeout, ssl, sslcert, sslfactory, sslkey, sslpassword, sslrootcert, SSLTruststore , SSLTrustStorePath, tcpKeepAlive, TCPKeepAliveMinutes, unknownLength

And then I enter my password and all is fine... until the next query. And this gets annoying very quickly because I keep having to copy/paste the password.

## The cause

It turns out that the link between my Mac Keychain and IntelliJ was not working too well. I just had to head to Preferences and tell IntelliJ to save passwords using KeePass (which is a neat feature by the way) and everything works great again.

![Use KeePass instead of Mac Keychain](/images/690-intellij-alzheimer/solution.png)

## The root cause

What's the actual problem though? I don't know. The keychain works well and IntelliJ is awesome. I do suspect the keychain to be guiltier here, as it tends to like nagging me for passwords. Better safe than sorry I suppose. If you know a likely cause, please send me an email.