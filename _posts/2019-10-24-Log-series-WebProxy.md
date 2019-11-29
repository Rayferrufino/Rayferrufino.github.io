---
layout: post
title: Log Series 5 WebProxy
author: Rayzer0
category: Blue Team
date: 2019-5-15
---

## Bluecoat proxy logs
## http://log-sharing.dreamhosters.com/bluecoat_proxy_big.zip
## tail -n300 Demo_log_001.log
## dont **cat** these logs; millions of lines

```perl

2005-05-04 17:16:08 1 45.14.4.61 304 TCP_HIT 207 431 GET http hg.travelocity.com.edgesuite.net /graphics/tvly_mc_125x25.gif - - DIRECT 80.67.66.62 image/gif "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1)" PROXIED Travel - 192.16.170.42 SG-HTTP-Service - none -
2005-05-04 17:16:08 154 45.14.4.127 200 TCP_NC_MISS 2973 720 GET http images.google.com /images ?q=tbn:-dEjG3JAHxgJ:www.kevcom.com/images/linux/linux.logo.2yp.jpg - DIRECT images.google.com image/jpeg "Mozilla/5.0 (Macintosh; U; PPC Mac OS X; en) AppleWebKit/312.1 (KHTML, like Gecko) Safari/312" PROXIED Hacking/Proxy%20Avoidance - 192.16.170.42 SG-HTTP-Service - none -
2005-05-04 17:16:08 18 45.23.4.216 304 TCP_RESCAN_HIT 422 405 GET http twinpeaksweather.com /java-sys/Dgclock.class - - DIRECT 66.235.216.135 application/octet-stream "Mozilla/4.0 (Windows 2000 5.0) Java/1.5.0_02" PROXIED News/Media - 192.16.170.42 SG-HTTP-Service - none -
```

### Base on the previous log we can see the following:

1. The proxy program used to capture logs is the **Symantec Bluecoat ProxySG.**
2. According to Symantec Support the HTTP codes recorded on the logs are **TCP_HIT, TCP_NC_MISS, TCP_RESCAN_HIT**

| CODE          | VALUE         | 
| ------------- |:-------------:| 
| TCP_HIT       | A valid copy of the requested object was in the cache. |
| TCP_RESCAN_HIT| The requested object was found in the cache but was rescanned because the virus-scanner-tag-id in the object was different from the current scanner tag.         |  
| TCP_NC_MISS   | The object returned from the origin server was noncacheable.     | 
