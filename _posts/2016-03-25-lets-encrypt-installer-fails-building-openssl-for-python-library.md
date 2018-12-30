---
id: 163
title: Let's Encrypt installer fails building OpenSSL for Python library
date: 2016-03-25T22:26:25+00:00
author: scott
layout: post
permalink: /lets-encrypt-installer-fails-building-openssl-for-python-library/
categories:
  - Tech
tags:
  - Encryption
  - "Let's Encrypt"
  - nginx
  - SSL
---
![Nginx logo]({{ "static/nginx.png" | absolute_url }}){:height="100" width="100"}

When upgrading my Let's Encrypt SSL cert on my tiny DigitalOcean VM recently, I ran into an annoying error: the installer would get to installing Python dependencies, reach M2Crypto (a pretty awkward wrapper around OpenSSL, but unfortunately with no better alternative) and fail with the following error:

```
building '_openssl' extension

creating build/temp.linux-x86_64-2.7/build

creating build/temp.linux-x86_64-2.7/build/temp.linux-x86_64-2.7

x86_64-linux-gnu-gcc -pthread -fno-strict-aliasing -DNDEBUG -g -fwrapv -O2 -Wall -Wstrict-prototypes -fPIC -I/usr/incl
ude/python2.7 -c build/temp.linux-x86_64-2.7/_openssl.c -o build/temp.linux-x86_64-2.7/build/temp.linux-x86_64-2.7/_op
enssl.o

x86_64-linux-gnu-gcc: internal compiler error: Killed (program cc1)

Please submit a full bug report,

with preprocessed source if appropriate.

See >file:///usr/share/doc/gcc-4.8/README.Bugs< for instructions.

error: command 'x86_64-linux-gnu-gcc' failed with exit status 4
```

Argh! It turns out that this error message doesn't provide us with much juicy context, as I found out when I encountered it. The important part is near the end:

```
x86_64-linux-gnu-gcc: internal compiler error: Killed (program cc1)
```

Hmm, I thought. Must be the kernel! I only had about 250 megabytes of memory left, so I tried stopping a few non-critical but memory-heavy services (like Redis for my Hubot) and luckily, that saved me — it was just a lack of free memory. But what could you do it that wasn't possible?

One option, while possibly quite slow, would be to create a temporary swapfile. Thankfully Digital Ocean's small VMs use SSDs so it's not the end of the world:

```
sudo dd if=/dev/zero of=/path/to/swapfile bs=1024 count=524288
sudo chmod 600 /path/to/swapfile
sudo mkswap /path/to/swapfile
sudo swapon /path/to/swapfile
```

And once you're done, you can remove it:

```
sudo swapoff /path/to/swapfile
sudo rm /path/to/swapfile
```

Nice!
