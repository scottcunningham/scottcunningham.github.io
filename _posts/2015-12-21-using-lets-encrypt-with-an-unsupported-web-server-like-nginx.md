---
id: 133
title: "Using Let's Encrypt with an unsupported web server (like nginx)"
date: 2015-12-21T23:51:17+00:00
author: scott
layout: post
permalink: /using-lets-encrypt-with-an-unsupported-web-server-like-nginx/
categories:
  - Tech
tags:
  - Encryption
  - GitHub
  - "Let's Encrypt"
  - nginx
  - SSL
---
![Nginx logo]({{ "static/nginx.png" | absolute_url }}){:height="100" width="100"}
![Let's Encrypt logo]({{ "static/letsencrypt.png" | absolute_url }}){:height="100" width="100"}

Let's Encrypt is a fantastic tool, there's no doubt about it. However, as mentioned in a previous post, they only really support Apache using their completely automated method. If you're using a more modern web server like nginx (I am!), it's slightly more complicated but by no means insurmountable.

Let's Encrypt's tool is essentially a Python script with a load of wrappers around it. Its main job is to generate and install some SSL certs for your web server so that you can serve HTTP traffic. Pretty simple. It also does some other magic, including:

  * Installing some pre-requisites (OS-level packages, such as python, virtualenv, etc)
  * Verifying that you actually own the domain that you're generating a cert for (more on this later)
  * Generating certs and taking care of the process of getting them verified

In order to do #2, the tool listens on port 80 while a remote HTTP client talks to it to verify that yourwebsite.ninja really resolves to the server that you're running the scripts on. This mode is called "standalone mode" by Let's Encrypt, and there's apparently a much more elegant solution for Apache (which isn't of much use to me, unfortunately!).

Let's get started!

First of all, clone down the repo with all of the code in it:

```
$ git clone https://github.com/letsencrypt/letsencrypt ; cd letsencrypt
```

Install all of the dependencies for your system with the letsencrypt-auto script:

```
$ ./letsencrypt-auto
```

Once that's all sorted, you'll get an error message saying that another PID is listening on port 8080, so it can't run the verification process. That's good. Stop your web server:

```
$ service nginx stop
```

Then re-run the script with appropriate params:

```
./letsencrypt-auto certonly \
    --standalone \
    --email hello@yourwebsite.ninja \
    -d yourwebsite.ninja
```

If you also want certificates for subdomains pointing to the same machine (eg. www.yourwebsite.ninja and blog.yourwebsite.ninja), you can append extra `-d $DOMAIN_NAME` sections to the command. This will create certs in `/etc/letsencrypt/live/$DOMAIN_NAME`. Easy!

Now all that's left to do is to tell nginx to start using your certs! In your server block (probably in `/etc/nginx/sites-enabled/$SOMETHING`), make sure that nginx is listening on port 443:


```
server {
  # The existing 'listen' statement probably says to only listen
  # on port 80. Fix it!
  listen 443;   
  ssl on;

  ssl_certificate /etc/letsencrypt/live/yourwebsite.ninja/fullchain.pem;

  ssl_certificate_key /etc/letsencrypt/live/yourwebsite.ninja/privkey.pem;

  [...]

}
```

It might also make sense to redirect all HTTP traffic to HTTPS. In the same file, just above the `server` block that you just modified:

```
server {
  # Just redirect all HTTP traffic to yourwebsite.ninja to HTTPS
  listen 80;
  server_name yourwebsite.ninja;

  location / {
    rewrite ^(.*)$ https://yourwebsite.ninja$1 permanent;
  }
}
```
