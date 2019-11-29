---
layout: post
title:  "Pentest Report 'Writeup' Linux"
description : I was recently asked to write a report for a pentest job. Company name omitted :) 
tags: HTB, pentest, sudo, perl
---

# 1.0 Penetration Test Report Preface
> - 1.1 Introduction

This report contains all efforts that were conducted in order to fully compromise the Hack the Box “Write-up” machine. This report should contain all lab data in the report template format as well as all items that were used to own the box. 
> - 1.2 Objective

The objective of this report is to perform a penetration test against one of active machines on Hack the Box network. In this scenario an active machine named Write-up with an internal IP address of 10.10.10.138 on the Hack the Box private network has been selected for this exercise. The task is to follow a methodical approach in obtaining access to the target machine from beginning to end.
> - 1.3 Technical Requirements

In order to complete the penetration test report the following technical requirements need to be met:
•	Computer
•	Internet Access
•	Access to Hack the Box private network (VPN)

# 2.0 High-Level Summary

I was tasked by C~~ontext Information Security~~ to register with Hack the Box and complete 1-2 active machines and write a technical report. Hack The Box is an online platform that allows security analyst to test their penetration testing skills and methodologies in real time scenarios and thus helps improving not only basics but also advanced concepts of pentesting. Proof of work is done through the use of flags which contain unique hash values. All boxes contain two flags, a user flag and a root flag, user flags are tied to a low privilege vulnerability that will give a hacker an initial foothold on the target machine while root flags require a privilege escalation targeting admin accounts, preferably root on Linux machines. A Linux base machine “Write-up” was selected among the list of public available active boxes for the exercise. Overall objective was to penetrate the machine, identify the system, and exploit flaws while reporting the findings back to ~~Context Information Security~~.
When performing the penetration test, there were a few alarming vulnerabilities that were identified on the Write-up machine. When performing the attacks, I was able to gain access through a security flaw on the web application, and from there I gained an initial foothold on the target machine and obtained access to a user account on the system. Using the user account I was able to privilege escalate to root using a reverse shell due to another security vulnerability related to administrative level rights and path priorities. The target system was successfully exploited and access was granted. The system as well as a brief description on how access was obtained are listed below:

•	User – CMS Made Simple < 2.2.10 - SQL Injection CVE: 2019-9053
•	Root – PATH hijacking. Perl reverse shell

> - 2.1 Recommendations
The following recommendations should be applied.  The CMS Made Simple application should be upgrade to fix the vulnerability that causes the blind SQL Injection and discloses the administrator username and hashed password. In addition, administrators with root level access should avoid at all cost creating cron jobs that use relative path with root level rights. This allow malicious attackers to compromised the path execution and leverage their user rights.

# 3.0 Methodology
I utilized a widely adopted approach to performing penetration testing that is effective in testing how secure the Hack the Box Labs machines are. Below is a breakdown of how I was able to identify and exploit the target system.
1. **Reconnaissance**
- Information Gathering
- Service Enumeration
2. **Exploitation**
3. **Privilege Escalation**
4. **Reporting (This technical report)**

> - 3.1 Information Gathering
The information gathering portion of a penetration test focuses on identifying the scope of the penetration test. During this penetration test, the specific IP address of the target machine was:
```ruby
Write-up Network
10.10.10.138
Attacking machine
10.10.14.14
```
> - 3.2 Service Enumeration
In this phase of the penetration test we focus on finding out what services are alive on our target machine. The objective is to find if there are any vulnerabilities that can be exploited on the services running or at the very least give us information on potential attack vectors into a system. We used Nmap an open-source port scanner for the service enumeration
The following ports were discovered after an Nmap scan:
![](https://lh3.googleusercontent.com/BgGY9StNsAkrYwSUNeBLfWjrJ9Z-6Ci0VdPUlVJaFhJrFmeGaTGkUJqzZ0_G5bP7ryj-L4d3B8_ogkb0je7X4EALN4SYuihEJIksAiy4MjnFTraaUOAY8Q798HR9lXOPlfjKuzJ_QxwvBNQu8_7I2f5JEqFTsd8yz9-kDpvszihJZbwfAOqNOBs6oBqUYrjtaRlaslfsafTFNFL5eNNfc80UEuhk7RdPs8ZmKHDiKqBLtBSzkLyDZqqTVlTcJ5HjfbzNJ6sZsi5pr1FVEohvDbv761HeSeg2H4OCn40GifeBLMWs6R_zVIfenrOdf0QyfpKsBl8WHlSwDyrEr5RRDMy6F87eQd0PQwf1jHKOId6CPsKpIm5jOPOVsoGgCWpklXcqIV-NaSzdILiGR0ILLtw2_S7T3uk0B_5-h8JIlr5JhPf3hk_wI67uGPfeU5mwOPAJJDLVyw1wr2fgS-53hbbA9NfA9rxn0X25Wfp_Iw780lhPazTB2FMkSYgaFsz930KkBYyQXTf-zMsfvvjdAU58upasJJ4wkT2WR4OMd5KSkAg2LyarD7Ej8Fb8dA6wQO-alDM3QQXgkRlUshS5C7t8CVzNWSjkd_RFGcTdET45k27ZJuTU8wjJB8Dub3mrUxObugmk0h-w9RV3jivcrNc4yeT673QvzJOAen5Z02TGlnOrKBxqIY4=w938-h297-no)

We noticed that port 8O is open. Most likely there is a web application is running and we should find more interesting information on this port so let’s try with another tool. This time we going to use Nikto. For those unfamiliar with this tool, Nikto is an open-source vulnerability web scanner that scans webservers for dangerous files, outdated server software, and webserver misconfigurations.

![](https://lh3.googleusercontent.com/Xr3NEpzNZj2g_pC6OcPzfux1-tX9A3_P0JjhKaF3QRdWFZLCal6WkwnE3cO2gTVyjXdDSOFCa7ct10m9w8IiNrbrLIdOMnkHdxfsHDc1GtACZdwv3DrWNLvE65g_Tk3_NccR7ojQdLsOjoU8ds56mGoMAN5_Xllu3FfbNJHWczUEIMVAaFCnzT159XD40ZUFt43lde15A231QyUBHWCY9LtyuSMP5VOwormSq6govIUIkB3HyvDVdOlDsD49ihtW2RGjxLPElg_f6D_AJL6IhDIS7IAfDPetY9kLU2yOk1Jwf6MRT-PLy8bDc4QGv1cJh6l0c_jcJ0uFCbKnfvom4cJPgVi45M4dS6zrRTt-sgtmV6912cVP9J1n7hydAVi-_zIsncaPQZD_vQQ6usjyF-8v39ZIJg2fZKe8M0ggbbHNHczbHARD0d-mlpANSEZ4fPdRW5Jrj_qEeVW9schIc8F3_L-3Y3iIw3EdL7JoIAKwN5NU1ZAJZQT3COvOU7lCtX2LcLbG-f3oMb2cwZ06Qlgdx5MPz7pjRw3kOQBjTmtDEyqLcIBWztz3cq_kne8cD2S3_IBYaBXxOkTzJhl0-gkhUkpctfjfg3rBtcTeyjHNol_XbhmlmBE_85rVcLry_EU5gEWl8JErqiA-jTxT0dsCqmO0ZZ_FHmTcr7J1bRbNXN92qa7tYWs=w1072-h341-no)

No much information is returned and the scan was particularly quick. Let’s check and find out what application is running on port 80.
We get a landing page that tell us about some sort of DoS protection that might explain why or Nikto scan returned short and probably the same would happen if we try a dirbuster scan and try brute-forcing the web application 

![](https://lh3.googleusercontent.com/_YhLVAVY_EqXHZpqGTba22QjuGIT-6alLMlE5bqTzpZXLIi3m4Pjatq7ng-ctfSMKf77uKZYS-Z2FuFKaCx_PPtQ_XUF0XUKBL4LYKmi7pFxlgolybsn9-WokxriNO0ttw3VQZrkJLYVcpiPdKQmZzXsrcKh9X-lmMAYPv8Qsu3SFUWn9D7ixb9AtZ2irZpBHlNCu59abrt8HvRvTDSGOS1xXBZUyMBW7mX8rDD7zbfnym1yYVaV2lgrh3Ja_QN3Aw29Id-Erh2_OT2Fz9HbA2JyioOcwggFrpoXrfI3VSwcP1DRYo2Uq9v4xYTeMgaWAVBbwqm5P8T0ddM3QBkegkrW9Sevmq5icGIUgJSZLJSy5VMSycglnfDr3PtHUgu5VAAXQZVoF_meeOJR3e-PRn8wW2mvqvAdhpHM0CMEn30Mawk75IfWzc0Y3b0OgGPDUYxjSZBccLy2Zrd44YZ8PuVqssK6p_c3wgBDMPwJNQ-qtf7iUAx-tIDIrC-hJwHIy25U8H89BXo1nVZvJcoUjD5ikoeKTw4hvtHpXDWbV2ofMjRsXaI6M8cX_JVYEFoOP__XTZbXf0WRVkyGvyoqnROs-zaQIyEX78CG6d7VvPxEkOi8xMHKp-d4Cl8LrxmsvI3mSVsbVRF0ONIcwfYaBPCv0KjYCeosWs44N3AYoYrQSDsrHJ3KQTk=w778-h568-no)

Let’s try a different approach and use a web specialized tool called OWASP ZAP.
![](https://lh3.googleusercontent.com/ry4gY15AeIu_0COtt1vq-Bm0OjFKOAIIQdF9mCUlPNASit4vJ5VORoOifeUrlIgf29JNAj37G2IQQhFOE3hKxCbrZ8GBTQXAFjklLH_yK7m3IvDjCe3HHJ2ZVvh5r6rRKPfmv0t2xydo9AyIJWScNnogOAMRvTOsRtXL5r4ILh6DryM90ZibMoW4wutVajFBKbygtjWfxBKdp_xSFc_K7a8uuNVKQvl-b1uCaC-EaXbyziKT6elL0ezC1C79NwscbiIDTL-dZjPH4KrJZuWYMcZFx10_eFJOvtNscG6NPfB3HX6JKbE4pg5PoUCOaAQKD98aEzZfoveT71lprkpgVOeCsbVQz8wQzcsHoXep4vfgM30m6GYtZ1V3edYP2I0S1uojebrN5tfGfjjDo06SXgs8cPTDjbLbfEovkYYFOvn7kpgWRrZ17JGoCBmKzNPwEzFiaxJxOcDyvMxGN4huy4D7MuYAU29eandJF3my8RhUq2PchrizKGnKcAfp-OzbGgRNQ6hedjz7KnM5ENSVxZPTpqeB2Og8vMfmfg-L74WajmyI33IjwMMgEcH-aNeiWwxTXkPuP6OZaSyQX2cHRyhCieB_ic_VCMPR_5LvPVG62MEwqhECqZtasn8zHl25iEwBM2P_U3f5s5G57jnLJRdINJIUKxTYuSm5DDRvXI9i9eJKZL4g9WI=w1349-h554-no)

We located some interesting pages:
-	Writeup.php
-	Ypuffy.php
-	Blue.php
-	Robots.txt
-	Sitemap.xml

![](https://lh3.googleusercontent.com/r5o4Czea5NM400KYLYI5rp83h6wLgxh0sBpfXQffKieLAshoM25UQjjklRA9d_f6BVlLeB1gFUN5hrTK8lvPSuTXLnY5biXERxD-CvCT76V6R2IfncVPUNyJRcimIuzGKK47Nl4woBOYB7gnLN3yJfRdU0oDRGxPrzyWNuJE5wbDxYsixa9B9n55hk6oI-ku3GVaAFwTEvN3YZcsoDwBZU9STRtdBAnX07hKNa6TymDmyzTom_4iNBqVti4oSlP92fHu5HXpZRsA6A0Bv5ldoR2wwJisdidRNtWZMILmJyA0hcPosq1h28UnYDAOuIn7IfLrfeHTiNJRa7kyjbQ3QTeQTU_Se9uBLfeW0-eufNVsZ_Zw_UBiNtKpmNZ8TSWEGbRS39pyMEry-zXNMc3tMFll4BRXz2ASCVXgNoF1HSSI3GNlbwHHIzv6FyEKDVweoADqsSAR97Wj3UxnaR1YNXxSXpYsvOVefO6202FwPVz6m7uOgxLwlWmUN_XbTJrwnCbBRvIKOAHPAHv8xV8SgoLIR-0r2Y5Gdmvgc147JiOASAW_-9xVtOglncYekWsWWBAmky8I46HEmRQn-SI9zzcf23mb1vbYeTJ58pZQIx-JnBDVOFFxQdSQqAcEwrmd0SrnBGQRr_S8Am2hk9bm9U76kF1ghPZfoCaO1uB2GhcTcdPf8W0mB-U=w1231-h568-no)

We then inspect the source code

![](https://lh3.googleusercontent.com/ywMdae-uB3NGhce1ZHx1f96hl0dSt7uYKUQiKuEaETilfrFwgV5VKlzKCrgSpSgKie6jahUqllAdkduEjx1y-eXgeIh_Oxs7x4U7yzbnPhSSZNH2pSBlnWwUOxeg59FjatjxM0XO8mttjebAc99itNNRHZJ16m-omokNVhczOnrGccBRmX0ooXWucSAS2OhKzBbJPkJqOKsXhwhJ5x4EJRJdCL5h7U88ifEITJIATBtVC_NY_TTxwVqjNcW1DCkXiom2elCwzjwzYEyZXr8DkvlY0aGSGIR6Pvi8WJjfm29vfcxV-aPpwdTLn63RCJqCwd1UmsSCffOuekmtjkhdt-iXECqoWWiMExMtCbOT3GxSEF29rTniUALkpkJ62RnwlbgjMcv2SOF_v2aBDjrfMYZVzLweKRzEhoG_EfrhZAHyOkVY5rRRwQz9gDf1fIKhXeyAr6d5n_ZEDsCdDZzKslZHPZJ_xnijvejM6jiDkN16rI8S6Xe-IY3hwhSK5wvu6FH0wqc3vSiJzmfmhtHKc-HHUjPhaqaJbvcx2deZq8fzAmm5yb1NMYVKDITJTzhU6vVGVpPUuZEBRhKIrg_cq0ioW7FsRE62cnzjQiLJexeYCzDruz6dAEWY9YSEWEbwxSa6M9Gz9eftomLMwkJnkmW7vrYMLiY5cfia2sdRp2HaUOBYRqQE7mI=w1041-h357-no)

We find out the name of the application running. CMS Made Simple.
Let’s google the application and see if we can find some exploits on Exploitdb and start our exploitation phase. 

> - 3.3 Exploitation
Vulnerability Exploited:  `CMS Made Simple < 2.2.10 - SQL Injection`
System Vulnerable: 10.10.10.138
```ruby
User Flag: d4e493fd4068afc9eb1aa6a55319f978
```
> Vulnerability Explanation: An issue was discovered in CMS Made Simple 2.2.8. It is possible with the News module, through a crafted URL, to achieve unauthenticated blind time-based SQL injection via the m1_idlist parameter. Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection')
> Vulnerability Fix: The publishers of the CMS made simple recommend upgrading to the latest version of their application. It can be 
> found here: https://www.cmsmadesimple.org/downloads/
> - CVSS v3.0 Severity and Metrics:
> - Base Score: 8.1 HIGH
> - Vector: AV:N/AC:H/PR:N/UI:N/S:U/C:H/I:H/A:H 
> - Proof of Concept:  Python Sqlexploit.py –u http://10.10.10.138/writeup/
- Screenshots:  

We download the python file, chmod it and run it using the –u argument for the url in order to launch a blind SQL injection attack on the web application.

The exploit obtains the following sensitive information:















