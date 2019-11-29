---
layout: post
title:  "HTB Popcorn Linux"
description : Burpsuite and linuxenum is all what you need to pwn Popcorn!
tags: HTB, dirtycow
---

# Recon
Nmap Scan
```ruby
Nmap scan report for 10.10.10.6
Host is up (0.021s latency).
PORT STATE SERVICE VERSION
22/tcp open ssh OpenSSH 5.1p1 Debian 6ubuntu2 (Ubuntu Linux; protocol 2.0)
80/tcp open http Apache httpd 2.2.12 ((Ubuntu))
|_http-csrf: Couldn't find any CSRF vulnerabilities.
|_http-dombased-xss: Couldn't find any DOM based XSS.
| http-enum:
| /test/: Test page
| /test.php: Test page
| /test/logon.html: Jetty
|_ /icons/: Potentially interesting folder w/ directory listing
|_http-server-header: Apache/2.2.12 (Ubuntu)
```
2. Gobuster scan
```ruby
Gobuster v1.4.1 OJ Reeves (@TheColonial)
=====================================================
=====================================================
[+] Mode : dir
[+] Url/Domain : http://10.10.10.6/
[+] Threads : 10
[+] Wordlist : /usr/share/dirbuster/wordlists/directory-list-2.3-small.txt
[+] Status codes : 204,301,302,307,200
=====================================================
/index (Status: 200)
/test (Status: 200)
/torrent (Status: 301)
/rename (Status: 301)
=====================================================
```
![](https://lh3.googleusercontent.com/jqaCBQu2bCGLe_0OJfG_bpO4t-ahOBicsPZQajB_u4VLU5aPQwd86gxGMODMBxbp3yESGQkiIIPI9vDDmxqDBcI3n9nswYkz6nxw49iPp1bkH4jMOYeEiVra7cABh0LuMo7h928Cly2--J_nOaCp9Tm4VqV-FD5i-LyNvDQ1jZlco23JXQohDtYBaBWRaph1B5X-wq2GyUfRaazDnZPWmU1DyJptVLfE8yCjZw493UTAmDazEYwD7I5KSrTBclLbqApgO6an4pOLn0NP7mGlb5FYtkRukAqv5SayJG2uhEk-FlJBVi2tcu9zI56_rDukW_rmNTohl4tuB00WT8-gUpUjsC3cNui4JonmVXSOvao6QKjlcKPcxa6OxmhgFxNnElTs6ar0xPtq3bwdqoPs4A2Aqv_v1xm2l2q-Tf0KYcqph6cqloi9kGXjeqjDuc-kezwBAOw_vv4Qo6fCM-McOKmgtfoN3WGKOjsj8OOyv_NGFhAKUDs5gjcPjh3WeNbchqcX7UfzuYF6xjiOFFRI9x8Io2iGdLn4WgXUoPWsvUkDQdDVxCV_aTiS7M-36QiIlZYrgk_fOBr7gLi9bjESPxhJqwkUgR5Rs5mNjlt1XLi9pTle-K3AKl-dPbKMBVe8Oe6kXGmkxJ0LXUWRobDk_mORxe0yjznOg7jCLXT1au6xVsBo29CONtE=w536-h306-no)

Nikto scan
```ruby
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          10.10.10.6
+ Target Hostname:    10.10.10.6
+ Target Port:        80
+ Start Time:         2019-10-30 13:15:22 (GMT-4)
---------------------------------------------------------------------------
+ Server: Apache/2.2.12 (Ubuntu)
+ Server may leak inodes via ETags, header found with file /, inode: 43621, size: 177, mtime: Fri Mar 17 13:07:05 2017
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ Apache/2.2.12 appears to be outdated (current is at least Apache/2.4.37). Apache 2.2.34 is the EOL for the 2.x branch.
+ Uncommon header 'tcn' found, with contents: list
+ Apache mod_negotiation is enabled with MultiViews, which allows attackers to easily brute force file names. See http://www.wisec.it/sectou.php?id=4698ebdc59d15. The following alternatives for 'index' were found: index.html
+ Retrieved x-powered-by header: PHP/5.2.10-2ubuntu6.10
+ /test: Output from the phpinfo() function was found.
+ OSVDB-112004: /test: Site appears vulnerable to the 'shellshock' vulnerability (http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2014-6271).
+ OSVDB-112004: /test: Site appears vulnerable to the 'shellshock' vulnerability (http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2014-6278).
+ Allowed HTTP Methods: GET, HEAD, POST, OPTIONS 
+ /test.php: Output from the phpinfo() function was found.
+ /test/: Output from the phpinfo() function was found.
+ OSVDB-3092: /test/: This might be interesting...
+ OSVDB-3233: /test.php: PHP is installed, and a test script which runs phpinfo() was found. This gives a lot of system information.
+ OSVDB-3233: /test/index.php: PHP is installed, and a test script which runs phpinfo() was found. This gives a lot of system information.
+ OSVDB-3233: /test/php_info.php: PHP is installed, and a test script which runs phpinfo() was found. This gives a lot of system information.
+ OSVDB-3268: /icons/: Directory indexing found.
+ OSVDB-3233: /icons/README: Apache default file found.
+ OSVDB-3092: /test.php: This might be interesting...
+ 8674 requests: 0 error(s) and 49 item(s) reported on remote host
+ End Time:           2019-10-30 13:19:35 (GMT-4) (253 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
``` 
Nikto reveals the path for the php info file
![](https://lh3.googleusercontent.com/0QXQkfKjxGgZMe_hh-eczHXWyoLM2yJNtLucEVRUmzMikydK1-_Sbtc2oZxSM-CpnyMF8C5YRY_GUm9nFMzB8LkEOGQFFAGjFz27XneUgGfaV7gzGw8nP42F9gYOwuzt7uCXPWRzhk49tLilf1b4GbtSlekJEYt0eQxjXAUFwXUvhTXVidJApdIOKHNdSVpD8Y6g0eCLb66r0hiHl3lCuZmAFBT55WqhIxTO1iAgEn9CLrdHvMv9b2gVM7oQtu_iGOxPgCZ1dd2CB9ffjGzdz4JoCQekxahHjgHBgP8QspCL-SUI5JBGAJu5HHajOERH5dBAotEuOGeTMkGJg1C4R_zvfn0u49t9SKUI9Dn2i-F2CHHsB2F_eTTqDmo7e8hPFduOzUjYxy48Yt1tRz3q9dVi65Mh20-wSS0YiUke4pzdr6p23JVn-5avx4dcPvDdWa4F52Sm8D9XWXH7iq_NZIX8kKXMscLwuJmTc1KBg-X4CtIIKAGa4yW8b6KoCopRD5AIL_PrGZyBk0c3e83SGy7WBtp9aAH7_shi3rpPnf-8_KteTTPkDJsunh1yiV4Ocf7NkA5wFC2buuQ1sSnnAmFJYYjuWYwNW_VPzEI6ffQrHITFFDSkDynh-Ili9eq_40RGFuVREVB3I9aJeAjRNjqla9RexWZGMz3Em5u2ZL-SJ9k0_sW16CM=w493-h499-no)

While gobuster reveals the directory - torrent
![](https://lh3.googleusercontent.com/R2E8Yy_9mOJ8ORYODRNtzRJueTd8mK_LdpHVDoopqAq4JzmsgKuPFZL0KHMAMY0WNj-4yBpZBcETeCVP0uCxFfpcd7y79slEspwslfqL9v0ROYoTHBZiOuBtVm6ZxT1tEHz5DX9lelg1C78_5xlduIQUOt1mALTtWSdPPeKizErooRIUWHaS8RPMx7Po8CXUPO8OAiUSJx39AKcxqkkyTJ855vALluSi4jq6yqtp_fX2-UAO438DObb_n2UxH1JandkzpKW913K-P5JK4EKyE68XzXrLNUVsmtZCAwgxsJwLPHhGhoUiYFt0fKoH_F4XTn_MCtnlJ-pD6Sb8Sxy-abrSEw9_3XdbYhryawAK9oOfFXoaEVrQXFI6OO3T021YSYIntwlHRiupRCBK8Yoo5JMIHEjPZDJHA6asrtiHzB20kg3PSn2ZC4STvgZjPKscilOOmWLnSzHwk2pl4TgweTigrB5_pChwm6stnkatdo3pR_YleksRUVHCn5GccvZsganMcvdMI-DL2E0qmvWlWbIpJpkuGbEqkpcpGC0ZTJ6tgTBIRWOLklHylm87HAHpI9WyzFfJqTo9HyIlsnwCtSBJgClaa3NQM2i13uHGVjXTYpMrIMglv6CRkU3yYVCUBOXrvpYxxXNTKvBGqux48oHJltjqatiQuX9L-P0GPMmN1RwzCVcNWTU=w812-h490-no)

# Exploitation Burpsuite
First I try some password guessing but did not work. However I tried some SQLinjection bypass...and I was able to access the web app
![](https://lh3.googleusercontent.com/hG6pJ1UahVEXCdo6irQk15Q-NGnQSoTwCUMmb1Dc9bqvuzA1HJgWBoSUSFLDDbF8WqXDzwq6toyvwwGkb89HiOcsR3oRofrwxpO3m3IHTo1M6pisd4Di5GxYIubtPAon2tKheBugv1lV9TSAgvoLpvaTJDBjsHkMDH0Ipd1F9EleuGhwNgHd1EUyaXN7_5Dkh9gtY4ObLmoRaUlv8WOjIN9n6a2EXSTdNgZzLPXLHVoZMMOos7uN1vhYnfmm2TKjcR0UhmfKaM7DqKQTfYe6GvyqQm7_tRMOMk1-i1woQiUcC79WNyOV9ikiswCTPkqaFJKeofqoP1-As82G0hiQnAuYOMlC3gxjFYw1R5uCAErrCj6Uwrk5qcDlW5NjxNXRu9xp5j_l5KAMcJLaYxWW1IzkSThTpfuNidfqLXw26tMj1CkJrTDpVvF9DE2FnPChPb5I-_AijB8MSUCwjA7mxKwwxr6o0cFkbs4rQnm2fSnpUWDx1Kgpy7MbA0IUUBWht-oABZ1HkrPpjSaRZLXRwzoehJ7opVYjnqziu7Pp48OeYwEeNRHsqELeawDKv0AT8Abaz_suBneLqWu8xBpCItJbTKvXA9I2WM0i-0lnCIiURsVeq8rhgxOJ9RTXz2wGq_xggw1_2OL7ehuaOMSCXQ_EFutURzhB9O_hJykHgX31QzhepqmDPGw=w345-h209-no)

We find a potential location to place a reverse shell on the upload image of the torrent file
We use Burpsuite to modified the php file extension to bypass the extension rule.
```ruby
Content-Type: application/x-php
Content-Type: image/png
```
I decide to use the reverse php shell from pentestmonkey to get a shell to get.
* Optional you can createa php meterpreter payload and use it as well
![](https://lh3.googleusercontent.com/kRHzMi60Em5QDXnZREVNO5LetJ0zXXBLununslJkuV2RgasIKCLmO4xYd9iiXjSkMfFiuUwiNfCUVLNEosP4VIe1hDrP1-X-Fj8WeBGNqmI2wB04g4Fk1KIh2DKAukVTO16_3WJuYrT2EzPNFGzmJknM9tIOpcuhMWMS2p0fkbw5AJ2PMuAXNN6Fob0NFH9YHGJ1eWm-2AOJnHiowzIVefvEZMGNTgeSLbDXmTtDfoSYp2RTOl4qstbiqpIsHq4yHmR9EYWOgvlyViXkA4Ra1K5xeryD2UcDAdMp4-TDd3O2kFJObFMa3k95E7XcTvnoE_YUOo68ARA5le52TcgKQPCFsNel61WqI6ETg1IkSJxSSIFcOptMfIvSefHYrVaqENq98Tsy3fgk36cECQcR7bvCDMKALUEIb8ZY8IXgC1pfSwPJLpTqbRMulpJhG9Xit4ptlI9oDxsb3Q6NxKs2CDzR54X9YK5YGM7eOOhUlnkDBFCsdI1n4rMdKTgSFqugkBn8a0wzGnN5gqvVejzm3-bIdymSrFHVt5VSRvy3jpkUhHLVLl_p_pSUrh-Dov9IJEdiaw9G5SElgHi0x89BPjoiPOwFlXwlyz6R05tfpaf1NxES5u_3ysMbrU3bhbmu3Nuj21S9nCot8M3EHY8VHGGyYNwihuJZJ3O0ATzPn71XBfS2LGlnrIc=w599-h244-no)

![](https://lh3.googleusercontent.com/P_T71fHMPGtpsAxzIXu77NqBaOa7hhYEYmSjy4Moume_RZmr-AB_cGfmcuwGmGhzR_ValI75hGyhtqfvOxWae6uZl-T9mcxdR1ipuFrH28Pl7Z7O66nW4df9Tz4iQF4JSJnWlFAjt4PzCDxTZbmuWJCkWcYlPuWydl-4Y3VdbqzwQQ_RwrHDx6YhtP4gej55WnareZJuIVI7UbFI_82HbNlk2Vns5M0COCKg8hCmc9HNeO1buExgs9s1f7IMehNS9N9zmvjeYxQBDwURGnmDKQTdiSSTxmJzNtdMwnst6mfmwrjGYZa8ykyDSHKx4S3LxhsnrbYT7KwxobCMOf7fPCkxeLJKMjYsxQlrP7ObBr25tt97I1RLdQLXFcBdxfN9h55iabxmmy_GDNuLa_moQk9vvLfxpB2wKA8zqVajQhNvU8ivBb2T0yVOtnZF_N1BAiCPgEnWEorJWpAZLJwm8MntDjziYIJamgLTgESG9qPX2WqEfYKLfIzNiaB_3TgDWY7JD1G5ypQM6-XFkX8sIg_z1aj6KmcVPljcwHMpUz6m-yR7QIGNCvkoXD6HDqiK_pM3eZa2PR5CXzN9JcojCRxxa3johiR-yn4GBjoA1FaGGRoMOTfvKsYbvCbJ5sFXjhwjAu3tsDWVUvD1Koaw4ViY9RXLah-9QQPOPrYTbtAsx14ydLZtlmI=w765-h259-no)


# Privilege Escalation 1 "Full-Nelson"
- Base on the Linux kernel 2.6 I compile and use:
```ruby
(RedHat / Ubuntu 10.04) - 'Full-Nelson.c' Local Privilege Escalation 15704.c
```
![](https://lh3.googleusercontent.com/ETLoBS-owH8r3yJazCSEcu0bWcd4gzwrh8wjPQmVQLoWwjhvDquVMjq8SclLB2XZEH2Gpl33k90027mhjns6SMu-fYnur3WrBjIH4c2hHQBlYotQ5hlI4xK3mdscWwRXGo9QIWEhcy10uGQgujQ0cSL2UY7TRXl8J9EJgoCrlY4ePv7YHzvV6IrDenJMEdkf0N1Kd_Nn8QEJVcvzMx4gb6GGu1DSGvWycMrwI2pkbQS7o-uV3DT_hsNJKjhSqvzLo5bQeoAUu8XoCAXVajUxRHFYoqza8yFuLQhxlLrAtThrWFVy2Eo2q6HXMqY8JOtrRfMGRK3kgLM9BIgYMJ3xCcUoH2HG1is7Q70RoFII-4o_4yNEOCaoWC4-itWWc7mylzg2-a7NIjxQE6d3Jpy9W54gMUKZPcavueqljSQgxwYLQWaEBLjmU6D8Doz9z0FsJQyCg2RHERkiC3lrQCPGg5fhhrA1ILp1WOPmDTeTeUMITEhNAjawiKLAyf56SyaQw8ru4Jwd67pvqpPQjHqebJhkaE1N9C6r7ZKIm0MWwg8VQAyRX2LQEGB-ddaTl-5Is2RF9STotQOZ3xFX1Vo7zX4nbDxl4OmRN02zyKL9wW3kyC_bxw_TQAJ9FavSNMQsa8n62o3dQtpzih6p6_ppWa-F-cs_h6pkdmtY8u8IGqsqZlBtK_eHAL4=w775-h219-no)

![](https://lh3.googleusercontent.com/8ipVtWoNN8bLz7y0iBJdn9Q_jWHzrhiabHrzjOLkhtzbQHEhWOP76pd_tT4airb2vJVU_3uaCacC8mKFRd523NH1SpKjAdxH4govwsvkw2Qx3qmXii9rJVatSIR8fPU6ZsBb06Fnbf8mfYCQd4rnYn0D7J8acJTk22elgPBdIYd43ooepi2g60-2NFEW__0CNIjwaARnR_gPf3ePf6dY7k151UsDkK7tKW0z_FzqatbSyzZPyX57_puFE8uUVtt94dvPgTx06xmjhv58sIlFZPiGaVHzksRIOOSRZhrbN73aw3ZEFxt0CLm6fmsDZ962y-mTo0ryF_EJGr29uAGpedi1x_B-6ezLAhlNk6ECTNdwVQi9aO1f-QNbdm-iuRdLBFTvpRV_9y_quTe0_PHXD04mnOnvuigQR-isH_BBfE0ruN14_L-Z37vx4qTqwH9kLzG1_akL2DWufSapwgFCKSS6XAFNfFtpn5VbfeTrxLqET7CDcT-aZ6d6gavYyyZLb6FBkSOcZdzn8CnSrueivm-w2Btro1vzC6qQz8ZUj_lb-Sg8DrQjaKPn94dGcUYIdkpWbqPBmWAOv1oBKKM-5ia7Qo9Pz3sbPzxmyThYJy7UUzdd600PnJctZ0vfT-CnBNraBapwIHVATfj_TU_OxY-fAC1fQqExUaPFP_SWRzW-csGuoy5RGyg=w514-h384-no)


# Privilege Escalation Method 2 DirtyCow
You can also escalate this machine using the infamous exploit dirtycow too.
```ruby
'Dirty COW' 'PTRACE_POKEDATA' Race Condition Privilege Escalation 40839.c
```
```ruby
 ROOT  "f122331023a9393319a0370129fd9b14" 
 USER  "5e36a919398ecc5d5c110f2d865cf136" 
