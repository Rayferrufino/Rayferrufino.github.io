---
layout: post
title:  "HTB Optimun Windows"
description : Nmap, Nikto and windows-suggester will be sufficient to pawn the box! 
tags: HTB, nmap, metasploit
---
# Recon
Nmap scan
```ruby
Nmap scan report for 10.10.10.8
Host is up (0.047s latency).
PORT   STATE SERVICE VERSION
80/tcp open  http    HttpFileServer httpd 2.3
|_http-csrf: Couldn't find any CSRF vulnerabilities.
|_http-dombased-xss: Couldn't find any DOM based XSS.
| http-fileupload-exploiter: 
|   
|_    Couldn't find a file-type field.
| http-method-tamper: 
|   VULNERABLE:
|   Authentication bypass by HTTP verb tampering
|     State: VULNERABLE (Exploitable)
|       This web server contains password protected resources vulnerable to authentication bypass
|       vulnerabilities via HTTP verb tampering. This is often found in web servers that only limit access to the
|        common HTTP methods and in misconfigured .htaccess files.
|              
|     Extra information:
|       
|   URIs suspected to be vulnerable to HTTP verb tampering:
|     /~login [GENERIC]
|   
|     References:
|       http://capec.mitre.org/data/definitions/274.html
|       http://www.mkit.com.ar/labs/htexploit/
|       http://www.imperva.com/resources/glossary/http_verb_tampering.html
|_      https://www.owasp.org/index.php/Testing_for_HTTP_Methods_and_XST_%28OWASP-CM-008%29
|_http-server-header: HFS 2.3
| http-slowloris-check: 
|   VULNERABLE:
|   Slowloris DOS attack
|     State: LIKELY VULNERABLE
|     IDs:  CVE:CVE-2007-6750
|       Slowloris tries to keep many connections to the target web server open and hold
|       them open as long as possible.  It accomplishes this by opening connections to
|       the target web server and sending a partial request. By doing so, it starves
|       the http server's resources causing Denial Of Service.
|       
|     Disclosure date: 2009-09-17
|     References:
|       https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2007-6750
|_      http://ha.ckers.org/slowloris/
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```
Nikto scan
```ruby
+ Target IP:          10.10.10.8
+ Target Hostname:    10.10.10.8
+ Target Port:        80
+ Start Time:         2019-10-09 23:29:40 (GMT-4)
---------------------------------------------------------------------------
+ Server: HFS 2.3
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ Cookie HFS_SID created without the httponly flag
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ OSVDB-38019: /?mod=<script>alert(document.cookie)</script>&op=browse: Sage 1.0b3 is vulnerable to Cross Site Scripting (XSS). http://www.cert.org/advisories/CA-2000-02.html.
+ Scan terminated:  3 error(s) and 5 item(s) reported on remote host
+ End Time:           2019-10-09 23:36:05 (GMT-4) (385 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
```
![](https://lh3.googleusercontent.com/6PdEmUJWCFUra6_unjn4CcPgMIKPZwvalE904fv4mHMiEtfzK5IAiW6VKagUOKdDoCLo0O-GbHCvjlLGLbZTJC8KhEDeVIdvV4f5GvCPHuAn_6wL197gDcDNwIZAnpe14o9sbUEuFqMwGxwLlUdvP7Q2ips2WspJWGIKsPEGe4dj3QW3GcX-Vq3O3DoWB9RI6TahiMxzlEMs5M9nVMb-kFUPySzAtG7GfUQraKoWSFzrWltCAeP9JbS8lG7Q6D19YfE9vB8yX1fGaHi0Fx9sWeTmGKZ18M6mEVX8xM-HeDxuvLWeSHpbXiPBZsP3d64lfCkXfhLnU7w3_CgeNFPuMXrveDFA79Nr4WJMJJg6MiCx-i4feDyPKGZFfn3E65ANo1-4kiReOcVWywnt357AUgskpG3xOA1DO7zpq9Hl7rH9XHHokdkU33DGxsroU2ud4I_Wph7H1_5ky0QY3bjOv2JuO4XRDuhDODuXubhG_4uMJzO9PGzpn2QX7aOVkgUnuhKJLjgUg-znjsGKe9unvlqeT9BHrgeRX-2y2jHeRI0_XpdpId2tXe96ibZOK5k2ExL2eVPwqjgAiELEwJrvcCOvlaWeGYOKvLeEtewbBIg9DqZKsk3OmgONeJBdc7OC81xPmwTTTLeRqy0RI_0RTxo3HJZyjqdQHrSO-LKZnw8NugCNBpLOkqc=w1259-h412-no)


# Exploitation

Our nmap scan reveals that there is a vulnerability on port:80
- Rejetto HTTP File Server (HFS) 2.3.x - Remote Command Execution CVE: 2014-6287
- We use metasploit  exploit/windows/http/rejetto_hfs_exec
Exploit is successful and we get user shell. 

# Privilege Escalation
We run windows-suggester and we find a potential exploit for privilege escalation

* https://github.com/GDSSecurity/Windows-Exploit-Suggester
- To feed the vuln-windows suggester: meterpreter > execute -f "cmd.exe /c systeminfo > systeminfo.txt"

```ruby
Computer : OPTIMUM
OS : Windows 2012 R2 (6.3 Build 9600).
Architecture : x64
System Language : el_GR
Domain : HTB
Logged On Users : 1
Meterpreter : x86/windows
OS Name: Microsoft Windows Server 2012 R2 Standard
OS Version: 6.3.9600 N/A Build 9600
```
- Microsoft Windows 8.1 (x64) - 'RGNOBJ' Integer Overflow ms16-098 exploit.
After findng a compiled version of the exploit, I use meterpreter to upload the file. Then I execute the program to get root shell.

![](https://lh3.googleusercontent.com/j2hsNUlTEZDsi-UroLewVCAq6XMLje_Xl_HirtcoOYhtJ-RNDn96HoUDKhB7Mp54JMApe_6ob4qV60BYEDu_czi1ICA53I2mXoTkKXgi9VGEnp4MEi7zDh7NK6Wul8BpPM7IaPttAbDNoJC_C86_aKAuc53xRF2YbwHI7L11c4mTEoMC1iHfMZvcHDzQCsVq-gd0aD0ZLyUTwekLRzLg6XMIEzc8KO4ewBG_A37No-ez64vTdtoR39No-fIb6MLMnpRbrM1n2ByMDY-_4ZAofz5xFU-PqZ3bIkysRuI9H5smS_1tUc_LkZ71W4jH0QWtixrOFpAQtstn3JmcGITRoWL5xU1gpD4B16YJtxDTzkZD19jQ5cCQ9LTIHSOYmsoWtXyKCBstWqi__4FYthkrFh8V0mMM0RcesllvLh7zdsRT9H8JWn8Akws9ixJEspk9IQ__DkEQ0LxTdtu-4_55kHxqzqZhcOFFTLLD8P4TyMTH8_he0paLFcs9QHcRw-LaQ8KhDK9ItSLTr7wTRpXS3d_jEIOxk9DhNHFpUH39cuCuszgGJk4bUYFylKjefmM7bREoWDV1yPezwefPikMtR9LpOgcXfGJTzJR2iRlpghofhDMiNRGqcBteEXDI0BoFw8WI2k0rBqWEcYGyBDjbcnHyAdV3SuGdgJtdDJHnnXzG2tp7sE5Sz2g=w428-h465-no)

# FLAGS
```ruby
USER "d0c39409d7b994a9a1389ebf38ef5f73"
ROOT "51ed1b36553c8461f4552c2e92b3eeed"



