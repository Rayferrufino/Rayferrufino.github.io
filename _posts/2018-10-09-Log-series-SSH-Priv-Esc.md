---
layout: post
title: Log Series 7 SSH Privilege Escalation achieved
---

# SSH Valid login and  SUDO call env
```perl
Aug  5 14:09:20 debian sudo:   debian : TTY=pts/0 ; PWD=/home/debian ; USER=root ; COMMAND=/usr/bin/su
Aug  5 14:09:20 debian sudo: pam_unix(sudo:session): session opened for user root by (uid=0)
Aug  5 14:09:20 debian su: (to root) debian on pts/0
Aug  5 14:09:20 debian su: pam_unix(su:session): session opened for user root by (uid=0)
Aug  5 14:09:22 debian su: pam_unix(su:session): session closed for user root
Aug  5 14:09:22 debian sudo: pam_unix(sudo:session): session closed for user root
Aug  5 14:09:23 debian sudo:   debian : TTY=pts/0 ; PWD=/home/debian ; USER=root ; COMMAND=/usr/bin/su
Aug  5 14:09:23 debian sudo: pam_unix(sudo:session): session opened for user root by (uid=0)
Aug  5 14:09:23 debian su: (to root) debian on pts/0
Aug  5 14:09:23 debian su: pam_unix(su:session): session opened for user root by (uid=0)
Aug  5 14:09:41 debian su: pam_unix(su:session): session closed for user root
Aug  5 14:09:41 debian sudo: pam_unix(sudo:session): session closed for user root
Aug  5 14:09:43 debian sudo:   debian : TTY=pts/0 ; PWD=/home/debian ; USER=root ; COMMAND=/usr/bin/su
Aug  5 14:09:43 debian sudo: pam_unix(sudo:session): session opened for user root by (uid=0)
```

# SSH Invalid Attempt to SUDO
```perl
Aug  6 02:52:28 debian sudo:   debian : 3 incorrect password attempts ; TTY=pts/2 ; PWD=/home/debian ; USER=root ; COMMAND=/usr/bin/su
Aug  6 02:52:31 debian sudo: pam_unix(sudo:auth): authentication failure; logname= uid=1000 euid=0 tty=/dev/pts/2 ruser=debian rhost=  user=debian
Aug  6 02:52:42 debian sudo:   debian : 3 incorrect password attempts ; TTY=pts/2 ; PWD=/home/debian ; USER=root ; COMMAND=/usr/bin/su
Aug  6 03:14:30 debian sudo: pam_unix(sudo:auth): authentication failure; logname= uid=1000 euid=0 tty=/dev/pts/6 ruser=debian rhost=  user=debian
Aug  6 03:14:50 debian sudo:   debian : 3 incorrect password attempts ; TTY=pts/6 ; PWD=/home/debian ; USER=root ; COMMAND=/usr/bin/cat /etc/shadow
```
