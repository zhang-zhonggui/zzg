---
title: adguard设置上游dns
date: 2024-05-31 21:22:01
tags: dns
---



# adguard设置上游dns

<!--more-->

最近在闲置的小Linux主机中安装adguard用来加速dns访问速度同时也是未来避免运营商对我经行dns污染以及抢答,使用总统下来,虽然多了一层,但网比之前好多了,这是我整理一份上游的dns地址包含国内外主流的dns,其他包含dot与doh,不使用ip的原因就是避免运营商抢答

```txt
tls://dot.pub
tls://dns.google
tls://doh.360.cn
tls://dns.quad9.net
tls://dns.alidns.com
tls://118117.alidns.com
tls://dns.quad9.net:853
tls://cloudflare-dns.com
https://doh.360.cn
https://doh.pub/dns-query
https://1.1.1.1/dns-query
https://1.12.12.12/dns-query
https://dns.google/dns-query
https://sm2.doh.pub/dns-query
https://dns.quad9.net/dns-query
https://dns.alidns.com/dns-query
https://dns.ipv6dns.com/dns-query
https://118117.alidns.com/dns-query
https://cloudflare-dns.com/dns-query
```

