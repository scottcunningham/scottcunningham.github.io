---
id: 130
title: "Let's Encrypt and the SSL Cert Mafia"
date: 2015-12-21T16:40:35+00:00
author: scott
layout: post
permalink: /lets-encrypt-and-the-ssl-cert-mafia/
categories:
  - Tech
tags:
  - Encryption
  - "Let's Encrypt"
  - nginx
  - SSL
---
![Let's Encrypt logo]({{ "static/letsencrypt.png" | absolute_url }}){:height="100" width="100"}

Since Google <a href="https://googlewebmastercentral.blogspot.ie/2014/08/https-as-ranking-signal.html" target="_blank">has started favouring HTTPS-enabled websites</a> over plain old HTTP, SSL certs are almost mandatory for many website owners. Up until now, getting one has either meant:

  * self-signing (which is sub-optimal since browsers warn about self-signed certs as if they're the end of days)
  * using a free cert from an untrusted CA such as StartSSL (which is sub-optimal because people visiting your website have to either install the StartSSL root certs or deal with more extreme browser warnings)
  * paying protection fees to the SSL Mafia (which is sub-optimal because it's often prohibitively expensive — several times the cost of my inexpensive hosting setup)

Obviously, none of these seem like great solutions. But why is that the case? It seems like the trusted CAs all seem to be aimed towards enterprises who can afford to shell out exorbitant sums of cash for a filled-out tickbox on a security compliance checklist.

Enter [Let's Encrypt]("https://letsencrypt.org/"). Let's Encrypt seems like quite a nifty tool. They're a CA who is already trusted (unlike StartSSL), and they offer certs for free (unlike other CAs). I recently installed one of their certs myself and it seems to work quite well. What's best is that they don't charge a penny for their service. Great!

The entire certificate creation process is completely automated and they have automated tools (written in Python, to boot!) to change your web server's config to use the new certs. The only problem is that they only support Apache with these tools, so getting set up with a more modern web server like Nginx is a bit more complicated but still not difficult. More information about that in a future post!
