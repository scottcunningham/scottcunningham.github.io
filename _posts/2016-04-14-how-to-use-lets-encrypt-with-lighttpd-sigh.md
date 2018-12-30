---
id: 171
title: "How to use Let's Encrypt with lighttpd (sigh)"
date: 2016-04-14T22:21:16+00:00
author: scott
layout: post
permalink: /how-to-use-lets-encrypt-with-lighttpd-sigh/
categories:
  - Tech
  - Uncategorized
tags:
  - "Let's Encrypt"
  - lighttpd
  - SSL
---
Everyone has legacy systems. It's the same story. Some mostly unmaintained server that you really want to upgrade to the newest, shiniest tools but somehow never get around to.

I've got one of them, it's an old internal service running a website behind lighttpd. I know, I know. Lighttpd is such a pain to configure compared to shinier toys like nginx (and honestly, somehow, even older mules like apache at times). So what if you want to use Let's Encrypt with it?

1. Generate the cert in standalone mode, just like for nginx. See my previous post if you don't know how to do that.
2. Modify your SSL config file for lighttpd. Mine is in `/etc/lighttpd/config-enabled/10-ssl.conf`. It looks a bit like this: 

```
# /usr/share/doc/lighttpd-doc/ssl.txt
$SERVER["socket"] == "ip.address.goes.here:443" {
    ssl.engine  = "enable"
    # ah, self-signed certs
    #ssl.pemfile = "/etc/lighttpd/selfsigned.pem"
    ssl.pemfile = "/etc/letsencrypt/live/yourwebsite.ninja/lighttpd.pem"
    ssl.ca-file = "/etc/letsencrypt/live/yourwebsite.ninja/fullchain.pem"
    ssl.cipher-list = "whatever"
    ssl.honor-cipher-order = "enable"
}
```


In order for lighttpd to accept a SSL cert, you'll need to concatenate the key and cert into a single cert file:

```
cd /etc/letsencrypt/live/yourwebsite.ninja
cat privkey.pem cert.pem > lighttpd.pem
```

Bam! Reload lighttpd to pick up changes, and you should be good to go.
