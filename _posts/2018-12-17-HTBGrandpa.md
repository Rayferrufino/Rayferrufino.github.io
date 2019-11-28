---
layout: post
title:  "HTB Grandpa Windows"
description : Easy box, metasploit and windows suggester will accomplish the task
tags: HTB, metasploit
---

# Recon
```ruby
Nmap scan report for 10.10.10.14
Host is up (0.048s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Microsoft IIS httpd 6.0
| http-methods: 
|_  Potentially risky methods: TRACE COPY PROPFIND SEARCH LOCK UNLOCK DELETE PUT MOVE MKCOL PROPPATCH
|_http-server-header: Microsoft-IIS/6.0
|_http-title: Under Construction
| http-webdav-scan: 
|   Public Options: OPTIONS, TRACE, GET, HEAD, DELETE, PUT, POST, COPY, MOVE, MKCOL, PROPFIND, PROPPATCH, LOCK, UNLOCK, SEARCH
|   WebDAV type: Unkown
|   Allowed Methods: OPTIONS, TRACE, GET, HEAD, COPY, PROPFIND, SEARCH, LOCK, UNLOCK
|   Server Date: Sat, 12 Oct 2019 05:01:08 GMT
|_  Server Type: Microsoft-IIS/6.0
| vulners: 
|   cpe:/a:microsoft:iis:6.0: 
|_    	IIS_PHP_AUTH_BYPASS.NASL	0.0	https://vulners.com/nessus/IIS_PHP_AUTH_BYPASS.NASL
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

# Exploitation

> So our nmap scan reveals that our target machine is vulnerable to IIS_PHP_AUTH_BYPASS.NASL

We use metasploit module 
> exploit/windows/iis/iis_webdav_scstoragepathfromurl

![](https://lh3.googleusercontent.com/osFd4cr3mBmgodm9thBZdZ5qFXHdTcmnMWzy_S1KGsrkN-gm3FH1zCwZJYO6AqiGp3tY4R9tEreSuKW027mhpWkpGORuH16NnOpQ-ehNXH24bt9dexOSGa1fpvf38uM18d3n4YUSy8m3iQ4AHKDiSj39u-wWzubWXPu_buEE4UoVsQsKDidbbrClzlElA27be3PbYos1wq3hWG6kf1ZucLI3Xoh3rOqE_fKBVeIwKrbjMJfOc3mtJOLf-3B9-5lOe8vFneVo82eioPwpAibxhpkGiGFwspegaFNLI7SUSs7wnOugH5PnmJarnSc8sVIgueMXm_-Tj69j_cQt8PJpwiUx7lAMAydWpRZO1dWc-T0rvTjWcjn4nIui0bRuvtCCNtqsWeGY6tM9qUHMvmKezkfLEWpANYt3skrABTL6z7HQ-kzAxqSYiOA2tVvvuPWiPGTChKoNCp1tGPRG-I1c2lvbFkoWL4usnPBwNZRGe2JFJjVsTRyaFcRJcKCEVuNi87M3qt4LvBk7gyWjhxlxC8-sCucoDlHNQFXmHI81QZ7SHQ46DqrmUVW4T3dUePKchzTO18X4QmkzeGS8u-peW5H1m3kcpxTqUBpBmSfre29oed2weUOtEW5mTQUzrSGKOd1qffiL7lkSnkQHHodmlgWVoV_MXwYZCqzemuPYG8wHCPYNG61Vj0Y=w1014-h274-no)

However our shell is very unstable so we use msfvenom to create a more consistent shell with meterpreter
![](https://lh3.googleusercontent.com/ItG-f5o7iKEEuj-ndMAj7yDc3N6vx2ZxaHUyOybNheB_zz90hykYrJabDHYqAuKtfz_Iiyu2fs8Vd7aaFNuXHNkbcTfKlKhFAvBs8ogRUfcKWKRZgtkHuXPfow69Y7EnXbmztfdBpKk-_cS66EN6XzhYeXvN3LGtj0l8-xGWNs8Pc_Ioo2jk_JEc2bpUL8BvEAO5sZKZ75ZCetfJSyHwjOGyl8y9H9pA-B6AGFh2m5BYuqBKYnGvjbi-zCoCD05C4PaUgg_xrpD60MW3vKlaFKeFXwTH82SwB5xZcgRwu0OoNPdjkWgpsY_yAk92dAPd_RG9wxwWqfZQUWf1htDLaQ7dbCbP4396YwDPosAQQV5famVeqJG-8I8yULRu7qd3YWCSgsvvggxHWedlfmhjQ7-PE4-Sjfqdeay7BY4B2fFx6nHwVtIP3NSACzZT6gtARH42gAJTlMgSfdTT2FGWIE20EoTU2zf1gEGzGFAOQcKGITmKfMdZvWZH9T9FQG33HBUhbxVYXDg4TCZJdtk-eA1YkhHL0XvrG7fsk44zAJzOegpKdVWyJnvVlU-vSC-0xUndJ-G6Jkbsg1O_JUoxW7ioWM7BJs1Z2imaRPylHx6EzUxpb6abOPeTxmqh8OYtZ6ufoCrJjjHLP1pg2v_cfNGF86skoTmDTMG2ZtC5PTBLhEFdovAMSxQ=w1226-h138-no)

We upload our shell to our target machine
![](https://lh3.googleusercontent.com/_hAabGcbBNLuhgLAM-zDkkJlz-ZUDwwk7alpQ6IX_oKwfpMRf3X122sgH0wy0C1U6bSo2BedjyN0CScTS7OPG2_pEduaSOAYQPJM2i8a0EOUqEC8X-FAihJ75-nFAx9RvJBpZ3bq8ONVtDp8ZCCfNsvQsw4dek1_xLL7RJh7n7Omk3mcktUPnrlH1ra-HUFx_XJS0fhg-0ssVtKUC2nYNGKM__ek-hixcV78HiW2JOJZFqjO0cXild5Nfc8-GfYzLefCtMfZiiT0qB7qFRD0qfm1u0pBCMPk4td5uK7JNe_gumxL3NyCIH8zQy0xE8pwXCeRP74zIDvTPfVsUQ1ZNiBnXFrbsjig9QCAasqgRjRDHlpLDcgPT5m6-q_BeJuJTP6NQdRkNZmpM0VyldgqNITQB8LNaQGNAZw7-y6ScdK4DcdFlDQchNrD2CApN3N1e4_OWZ9RkT8XBaKLiaOMPKZIs44nl_xd2kTsyp-gLRRaWpPM0rvnhBi-9awQeCwt2-0f3DM6dAkGng9w0ett3MMyAsQ4nglNY-_MzdP1aHkzTVfpGHVELgmddS0QudHQ9_pRAHIqsTLwGP7awPrxLs-q-mD9NwebI0ufWukNqTte2LyYznRfkruq7G0iTXz3BlZP_YdiCprK4oAqe2ZQGUMbKnH_-Uk0le23rp88U2YHN2qTXET5jPs=w805-h106-no)

# Privilege Escalation
We grab the systeminfo from the machine
```ruby
Host Name:                 GRANPA
OS Name:                   Microsoft(R) Windows(R) Server 2003, Standard Edition
OS Version:                5.2.3790 Service Pack 2 Build 3790
OS Manufacturer:           Microsoft Corporation
OS Configuration:          Standalone Server
OS Build Type:             Uniprocessor Free
Registered Owner:          HTB
Registered Organization:   HTB
Product ID:                69712-296-0024942-44782
Original Install Date:     4/12/2017, 5:07:40 PM
System Up Time:            0 Days, 0 Hours, 46 Minutes, 43 Seconds
System Manufacturer:       VMware, Inc.
System Model:              VMware Virtual Platform
System Type:               X86-based PC
Processor(s):              1 Processor(s) Installed.
                           [01]: x86 Family 6 Model 63 Stepping 2 GenuineIntel ~2298 Mhz
BIOS Version:              INTEL  - 6040000
Windows Directory:         C:\WINDOWS
System Directory:          C:\WINDOWS\system32
Boot Device:               \Device\HarddiskVolume1
System Locale:             en-us;English (United States)
Input Locale:              en-us;English (United States)
Time Zone:                 (GMT+02:00) Athens, Beirut, Istanbul, Minsk
Total Physical Memory:     1,023 MB
Available Physical Memory: 773 MB
Page File: Max Size:       2,470 MB
Page File: Available:      2,306 MB
Page File: In Use:         164 MB
Page File Location(s):     C:\pagefile.sys
Domain:                    HTB
Logon Server:              N/A
Hotfix(s):                 1 Hotfix(s) Installed.
                           [01]: Q147222
Network Card(s):           N/A
```
- We use the obtained info and use with windows-suggester and choose our exploit

```ruby
[E] MS14-070: Vulnerability in TCP/IP Could Allow Elevation of Privilege (2989935) - Important
2/3
[*] http://www.exploit-db.com/exploits/35936/ -- Microsoft Windows Server 2003 SP2 - Privilege Escalation, PoC
```
> We use metasploit module: exploit/windows/local/ms14_070_tcpip_ioctl 

Finally we got ROOT
![](https://lh3.googleusercontent.com/osFd4cr3mBmgodm9thBZdZ5qFXHdTcmnMWzy_S1KGsrkN-gm3FH1zCwZJYO6AqiGp3tY4R9tEreSuKW027mhpWkpGORuH16NnOpQ-ehNXH24bt9dexOSGa1fpvf38uM18d3n4YUSy8m3iQ4AHKDiSj39u-wWzubWXPu_buEE4UoVsQsKDidbbrClzlElA27be3PbYos1wq3hWG6kf1ZucLI3Xoh3rOqE_fKBVeIwKrbjMJfOc3mtJOLf-3B9-5lOe8vFneVo82eioPwpAibxhpkGiGFwspegaFNLI7SUSs7wnOugH5PnmJarnSc8sVIgueMXm_-Tj69j_cQt8PJpwiUx7lAMAydWpRZO1dWc-T0rvTjWcjn4nIui0bRuvtCCNtqsWeGY6tM9qUHMvmKezkfLEWpANYt3skrABTL6z7HQ-kzAxqSYiOA2tVvvuPWiPGTChKoNCp1tGPRG-I1c2lvbFkoWL4usnPBwNZRGe2JFJjVsTRyaFcRJcKCEVuNi87M3qt4LvBk7gyWjhxlxC8-sCucoDlHNQFXmHI81QZ7SHQ46DqrmUVW4T3dUePKchzTO18X4QmkzeGS8u-peW5H1m3kcpxTqUBpBmSfre29oed2weUOtEW5mTQUzrSGKOd1qffiL7lkSnkQHHodmlgWVoV_MXwYZCqzemuPYG8wHCPYNG61Vj0Y=w1014-h274-no)

# FLAGS
```ruby
ROOT "9359e905a2c35f861f6a57cecf28bb7b"
USER "bdff5ec67c3cff017f2bedc146a5d869"
```




















