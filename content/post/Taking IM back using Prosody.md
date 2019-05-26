---
title: "Taking IM Back Using Prosody"
date: 2019-05-26T11:09:30+02:00
tags:
  - XMPP
  - IM
categories:
  - dgplug
draft: true
---
The common way for most people to chat is using an app on their phone which typically one of [Whatsapp](https://whatsapp.com), [Messenger](https://messenger.com) or [Telegram](https://telegram.org).
In the same vein we have apps like [Threema](https://threema.ch) and [Signal](https://signal.org) which are more popular in certain circles.
But the one consistent feature for all these services, is the centralization of accounts and forcing users to be trapped inside a silo.
It is true whether the services or clients themselves are open source or not.
In the case of Signal, the author doesn't want to federate with people who want to run their own servers[^1].
Given that the Freedom of the Press Foundation recommends Signal as a secure communications tool[^2], I have nothing against the security of the service and the client.
It has audited by cryptographers and the underlying protocol for end-to-end encryption is used by other messaging services.
Given this, you would still want to have control over the service and client for various reasons including not wanting to be dependent on centralized services.

# XMPP
[XMPP](https://xmpp.org/) is an open standard for messaging.
Notably [GTalk](https://developers.google.com/talk/) and earlier iterations of Messenger supported XMPP and used to federate with other servers.
So, it has a proven track record of actually working at scale but being closed off to grow their own proprietary services and lock users into such platforms.
XMPP is generally extended by XEPs, XMPP Extension Protocols, that include features such as end-to-end encryption, file uploads, avatars etc.
Even the definition of the process XEPs is [XEP-0001](https://xmpp.org/extensions/xep-0001.html) :)

To use XMPP, one can sign up for an account at one of the free services which need to be verified or you can run your own server.

# Prosody
[Prosody](https://prosody.im) is an XMPP server that implements a lot of XEPs[^3] and further has community modules which can be used just as easily[^4].
I will be using Debian 9 on the server and the project provides repositories for installing the latest stable release to make installation simple.

To install start by adding their repository to Debian's sources list,
```
# /etc/apt/sources.list.d/prosody.list
deb https://packages.prosody.im/debian stretch main
```
add their key
```
wget https://prosody.im/files/prosody-debian-packages.key -O- | sudo apt-key add -
```
and finally install the package using `apt`,
```
sudo apt install prosody
```

## Configuring Prosody
The configuration of Prosody is a single Lua file located in `/etc/prosody/prosody.cfg.lua`.
If you are already configuring this on server, the first course of action is to change the domain under which you want to run the server.
```
VirtualHost "myxmppserver.im"
```

Next is to specify the certificate used for SSL,
```
https_certificate = "/etc/prosody/certs/myxmppserver.im.crt"
```
I use [Lets Encrypt](https://letsencrypt.org/) to obtain certificates for free and if you already use for the server, it can be linked to the current location.
```
sudo prosodyctl --root cert import /etc/letsencrypt/live
```
At this point you can start the server using `systemctl`,
```
sudo systemctl start prosody
```
By default it will log under `/var/logs/prosody/`, so be sure to check there in case there are issues.
To test the installation, you need to add an account.
By default, the configuration prevents creating accounts by using clients as a safety feature but if you are planning to run a public server this can changed later.
```
sudo prosodyctl adduser test@myxmppserver.im
```
Test the account using a Jabber client.
There are many programs but I find the android app [Conversations](https://conversations.im), to be fully featured and a pleasure to use.
If all is well, we can move on to enabling some nice to have features in Prosody.

In the `modules_enabled` table, uncomment `mam` and `csi_simple`.
`mam` allows one to access the archive and this allows Conversations to pull down the history when you were offline.
For using some community modules, it was recommended by [linkmauve](https://linkmauve.fr) to have a clone of the modules repository and link the required modules into modules library under prosody.
```
hg clone https://hg.prosody.im/prosody-modules/ prosody-modules
sudo ln -s ~/prosody-modules/mod_smacks/mod_smacks.lua /usr/lib/prosody/modules
```
You can link these modules as required.
To enable them, add the module to the `modules_enabled` table and restart the server.
You can check for compliance of your server on https://compliance.conversations.im/ which can be useful to see if you want to enable other modules according to your requirements.
Please remember to create a separate account to run these tests by using `prosodyctl` since you will have to provide passwords to the compliance site.

In conclusion, one can run their own XMPP server or use a trusted server, for example, https://jabberfr.org/ and chat with anyone on XMPP whether on your server or on their own islands.
You can meet me at xmpp:jagan@j605.tk, See ya :)

[^1]: https://lwn.net/Articles/687294/
[^2]: https://freedom.press/organizations/open-whisper-systems/
[^3]: https://prosody.im/doc/modules
[^4]: https://modules.prosody.im/
