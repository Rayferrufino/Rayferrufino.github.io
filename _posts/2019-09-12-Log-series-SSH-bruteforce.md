---
layout: post
title: Log Series 6 SSH brute-force
author: Rayzer0
category: Blue Team
date: 2019-6-05
---

# Valid SSH session Login

```perl
Aug  6 03:11:13 debian sshd[1421]: Accepted password for debian from 192.168.56.3 port 53358 ssh2
Aug  6 03:11:13 debian sshd[1421]: pam_unix(sshd:session): session opened for user debian by (uid=0)
Aug  6 03:11:13 debian systemd-logind[412]: New session 4 of user debian.
Aug  6 03:11:16 debian sshd[1427]: Received disconnect from 192.168.56.3 port 53358:11: disconnected by user
Aug  6 03:11:16 debian sshd[1427]: Disconnected from user debian 192.168.56.3 port 53358
Aug  6 03:11:16 debian sshd[1421]: pam_unix(sshd:session): session closed for user debian
Aug  6 03:11:16 debian systemd-logind[412]: Session 4 logged out. Waiting for processes to exit.
Aug  6 03:11:16 debian systemd-logind[412]: Removed session 4.
Aug  6 03:11:22 debian sshd[1432]: Accepted password for debian from 192.168.56.3 port 53360 ssh2
Aug  6 03:11:22 debian sshd[1432]: pam_unix(sshd:session): session opened for user debian by (uid=0)
Aug  6 03:11:22 debian systemd-logind[412]: New session 5 of user debian.
```

# Invalid SSH session Login Attempt
```perl
Aug  6 02:54:16 debian sshd[1278]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=192.168.56.3  user=root
Aug  6 02:54:18 debian sshd[1278]: Failed password for root from 192.168.56.3 port 53312 ssh2
Aug  6 02:54:23 debian sshd[1278]: Failed password for root from 192.168.56.3 port 53312 ssh2
Aug  6 02:54:29 debian sshd[1278]: Failed password for root from 192.168.56.3 port 53312 ssh2
Aug  6 02:54:31 debian sshd[1278]: Connection closed by authenticating user root 192.168.56.3 port 53312 [preauth]
Aug  6 02:54:31 debian sshd[1278]: PAM 2 more authentication failures; logname= uid=0 euid=0 tty=ssh ruser= rhost=192.168.56.3  user=root
Aug  6 02:54:37 debian sshd[1280]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=192.168.56.3  user=root
Aug  6 02:54:39 debian sshd[1280]: Failed password for root from 192.168.56.3 port 53314 ssh2
Aug  6 02:54:42 debian sshd[1280]: Failed password for root from 192.168.56.3 port 53314 ssh2
Aug  6 02:54:47 debian sshd[1280]: Failed password for root from 192.168.56.3 port 53314 ssh2
Aug  6 02:54:47 debian sshd[1280]: Connection closed by authenticating user root 192.168.56.3 port 53314 [preauth]
Aug  6 02:54:47 debian sshd[1280]: PAM 2 more authentication failures; logname= uid=0 euid=0 tty=ssh ruser= rhost=192.168.56.3  user=root
```
