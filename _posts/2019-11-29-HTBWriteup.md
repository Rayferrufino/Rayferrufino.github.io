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

### 3.3 Exploitation
Vulnerability Exploited:  `CMS Made Simple < 2.2.10 - SQL Injection`
System Vulnerable: 10.10.10.138
```ruby
User Flag: "d4e493fd4068afc9eb1aa6a55319f978"
```
> Vulnerability Explanation: `An issue was discovered in CMS Made Simple 2.2.8. It is possible with the News module, through a crafted URL, to achieve unauthenticated blind time-based SQL injection via the m1_idlist parameter. Improper Neutralization of Special Elements used in an SQL Command ('SQL Injection')`
> Vulnerability Fix: `The publishers of the CMS made simple recommend upgrading to the latest version of their application. It can be 
> found here: https://www.cmsmadesimple.org/downloads/`
 CVSS v3.0 Severity and Metrics:
> - Base Score: `8.1 HIGH`
> - Vector: `AV:N/AC:H/PR:N/UI:N/S:U/C:H/I:H/A:H`
> - Proof of Concept:  `Python Sqlexploit.py –u http://10.10.10.138/writeup/`
- Screenshots:  
![](https://lh3.googleusercontent.com/LrJEHKmQjTVeoqDPNwXjS-yrVB9AIzn4BWFYaWkjxjlsjI2Fg_imslxW5iTjsYtHdkIpsAkk9SD6yRU3VRL3CS8QQJBxOZhdXZGlKEkSJgTRvs4I9uiQmYrpoXtAQ4uFSnoS11B_07KbQEl9R06rOh8togyk0j1baVt3WTDFxElsu75pLFQFKB5mR6D08EdVg3seE2Bf33iwFC-YZS-BKmZb0icD-Qaognc_hhQrkbsaLQvuMJOPQLAks6Yi4Q4jdo5r-1aIE0gfFCBfB4U7h0ZqMpe_RvNEivB5nqKpzidZ_QEASRx3LneTGyVJUBAE62kT2Uo1TxLZgth0TI-F97gfv6w0WRZ_zttXkOKjU_oHBNWEWlhEzhNdWIKJGfcWtkZFujTIYo2sZYe0WOyyDEEAD36Ydj98XH65H3wyhEBDHhihNWD3EZIG5AIvCc4GK1dDVaXB7oH-sLmReyPNctutDY6c4oV8zEmCHxWqiywQVbAVBtD_frbozmD0n2GzWektQ8RMFim3TjJC6Y4YcsUpxc7zSEsHITkTCH-X3fhWrBJxihTdw2Fj-QgRD4PrVRBkm02_67vnJui6io0I3_W5uHhDkA7F2_TiK0HIZvw2nj2FUFqOwJIbNZPcxnKVmWQq8ZOeWFo5Yxi8RzzdT7qD0xfNTtGaPmdtGrgyjQsQ7XP_eFXlddc=w921-h362-no)

We download the python file, chmod it and run it using the –u argument for the url in order to launch a blind SQL injection attack on the web application.
![](https://lh3.googleusercontent.com/_jve3UJ_BEM2JnJSP-g7usQCgSFudtIyIeBAf-ptXGH_6XR2_NfZ3l19ti2YGQg8xVSjKzyWSUNr6l_orBBrERysL_G0eBxrW3H9W0iYxiMKSvMXSftoTmmbPE8Wj1BFgXCn16yjDk2ULS7yXJ0em0X47bQAvUr0vJAawG3VRb6sCVCw4KCi-qNwWHlYaIM40OdHtu8pWhTQhPBpw97w4JGEbTFy_23uFwk11nGdzuc2tBfSLKa3mvkmJpyVc0XeqPyhtSy2Mtjt_4tv0I0W19a7jbYkhsl7hIU018Gq-e4DaURULtX8WkT53QpviJuOaDMBUWDBahpQ5ohS7yQXeGUC5DqPxc-S-wVU0hgr71FRP5VTYrz-NisYEJDBJB4xmwFRPKDmU50N804k8PpFQTX9oG4iHvpV5A8rt5zPIYSoAOoKz120GVlS-d80sjLokf9E4VnOJy_8-XedRLqPa-ZZIQPu7jteWX354mHq5GOorw9fSPICLJ9kfwedDjOIjnvH0AUPTR2VHtwzZu3mZQgCp9gTZwcP2SgI3Taq9vSBT_94xRcCEJPZ8deHdqJY2I0b1_G0Z1loXryLrTgocelqsZrwoEw3JI1wUtDtMSsWfp1Zi8tTONzdJX5g7TTsIZDKD2McLq2FXtTT1MNk0a5_yGxwpBOYDbtE6Mv7TX59DVXwgNs8xJs=w787-h43-no)

The exploit obtains the following sensitive information:
![](https://lh3.googleusercontent.com/YerbTmZEG2AWEDtYjHfBmHIt3SVJ01FoGPLqZkAcEFxJuEeLjN-1Nxt9hEtf7KdZQAkBO4SOmOVP2aKpWLt_CzdgBzTskhT2LXZWH-SpzUEth31zfEds08CeUOeJI-AzRuqCQWi-NeLarhGiSa2fz4fEkyrBQPxtuzLZse7u1lGF1RADSTGY2WCcTgHyVEyZKxOKxgSzAIx8r49KQeawUqX4Z3rGWwPbjcnoKgzVv935YiNflgQq5DBldWkOXcztuAL6L2f-fO3_Hj0bvd2OeCbn6ctHnTe-aNIQy7V7zqZ09WUsFekCsDuJ0lI78s1PeBndXCy6wqql8eKuTggub1P2_29zaltU6NhL5wPgnQ0ldm8HHHfejRj2YBUxO_BNY6hxf6UC2RKrvV-fazEnION_IpiXvba0RSDl9hfv5428hQoUGB7znB0rxNagJmOI4wyzZYnMdaXEI4msyeP0agddt2Wyqfbou_69eoYouQbdboOqGvQxWiAmnXuITRo2FjK2naog9cB1mSZMZCXrAUVtRR7hQ9tn_jO7Ur0IRxkQdHy8l6IwkU1sbj8bsbyB_0lMevopIdczcK6xnYZWtyoaQr06rBCsch9RF9EIBCpXd1v4Y3s7g2ZmxALKweCrj-ADnqscFrZTyJc4VifqwiqDouXDUYeWPr69fs4twKHD2W6heUIckeA=w783-h229-no)

We use an online decoder for the hashed password and the salt: password + salt
![](https://lh3.googleusercontent.com/kMRieFI36ABkswVFpOOSQxfT1BD4vVfYe8xA8dax_YRXvlFRCoZsB9es8QHv05I5_-8yQsS2In27Q6lNrhhEP7WzUzMNcrkWSc5z_oF9f38cj349T6jEUB2OZAdkK1sRDw9I4k-0wwCJZiGBsxPSsUM4sxhTZgDoRCWslHpJBSHjXjVRPFk9CjYnbea6DfxE7CitRkTAK5jnxDiAB50PTI4hCOxE3lufRjTSSK_13u4uNGhCpFkaaM19bHDBK-ro-ZfclFXjRz5WIpbjumGzAyucVm4uK9DOq3P0z08BYKck2oYs1yLRU9QwlYHPhH-huhxM2RuHltEgmgxnqyb9XdMWyZtYCcRdhAEjlUPSZdgSfYSaBAx7jFbhFwOU6mYQ3Oc9j5w7NVCjSsBWypzIxhiJwfY71r7Hf36YN1P08yYa5-fiy2XmUAfLrbRZXU_pFdzLkkiHEBUHQHoE9WLQ5GS-5yE_ze8PDLgv918zuIyepcS1VHalz6UEmvU13o2V4l1SOXqfbFC7_z7jOfXcevcXvhkqE2t9BnEQVFRMJkP4FeG2xfpMCLVd2ZHwJYvvtsOXHDz_E7D2zS9kc6FbKlQ0bPfwpWFvutoMghRO4WGKUYAz0y2JCuBBSceWesFUtO77tOSUgLKDMqe45qEmi9He-_ubrIwhSfq_XWeJ6rKOnYGrHY8RXU8=w1021-h301-no)

We decoded the password: 
```ruby
raykayjay9
```
We used the obtained credentials and try login on port 22 through SSH
![](https://lh3.googleusercontent.com/GSRiDG4e_psrZPyskjOchPGt1RMPa_gBa7SNMQGTPaI8u8K0Ma5jnelWjXFPrQnLRWkkTgTb_z8b0NWjbMHPRt1QV-Lcj_MZRf3OocTcsST9Ub2-KcuVl0tgOJ35P62jWjgcr1hlnfhXF87T0JHJuvS8KUEKEYPiNr0Wgo-kp5DmLCiZ2gXPqW_cuyeY3z5ZxJqnlF8EtFQif_S5x5av4tEeYj2JPPIcygWt1Ui_2iaZAFXQyIHdj_jtQkKBLqdnz-yCBWlNLxQcMHLHvoyOS6CNGwO3MkCDhKZAogKgBomtofO3zGnFPEMuFqNwULVdBcQzC0f8EQrLLxOiebKEU45uJD1GKzCgCFjG-932cm9yljBdkA9g0WNK5r583tTTczBclev2VM6KF4h-w0P6ku5rApOSnbgdpsQZfqXyP7DxQdkpq7AIQJPauIhbHpWpGHi7XX-7DH2pofsktKrA9CA8DoWKva3rXaDwIl6oUb0lgIot7HV_r0cdsYZ_K__3a4krJtSYiMu-pKSnkdVPLy7Wic8wBTEPAiojS7n9cfDM5O12H66o_Z7_tNsOYl9YewQvP3o7aBMF5HBQjRCagXOYeOkslYyfgfriClBLgWubru2sUVIDkKxAy0Hgpol4bFUWd3Tc92vKDwG6kqZS9elLIJ3TNPTS1Wi2nQg0hSJXFfIgf9-rAvY=w1166-h504-no)

We were able to get a SSH shell and the user flag. 
![](https://lh3.googleusercontent.com/aSptxXf25CYoironaNbU0V6dV652Vh0brlrOVTRXJkP3ay4zwLoa_jDI3--SM3eIdPK7d6l1bTwdqctZpDSOpHPgTUb3qia5L-b7z7eUAjWsjk--mMHg5GKvbvkxlrx5NLHgaHTzXV2X1VVtQYriU9Mcq_YeZ4r4GAUP5jxFyluTg0RmJH_CRRAzW5pfSQsNWhlRDwbu3uVmtjlNNoKVo-NHaj__QACCUXHRKy5fwLgfD4Kyx46w5cpqtpRCvQBLn0SPyHzVVDhB_272OF-iPBXRlCD7UpA051xnfNGTQ9Tj8uBGH-UXIWWFi_tYREmjANQVcw5kpsIISlcuPiKFB6jx54swHo_1OMZsy9AN5HmgP3S-Xftk9DFTbPeLvw7139Q7cK5xHRUtH1_uhBe94pryC_5lDdX62eoAqMowSUrizpEr2Xkdjrj6ZmFJcmj6tXqaQKnXgWhwZ6m4DORBDGgIn_suGDXSU7UYj4fSoiwiCXuVTsAHzZaxjB2JOAWyb6zM3VPD3WvtL7ytvd56EtQDd8TxifpOX0oMvCsfMZQHI_lTCGR3T85f9u7NAX-NNVnyKRxpizBVKdeLeYoLY0al_K1FEjewycYJ0ENQG62pT_JUeKS0DGk7vWsAB_pF7cj-rV9-v10u3eOH4BOBV9nmP0_eiHKh9CWNmWDeO6XrmFgwCQOmlfA=w349-h109-no)

However user `Jkr` does not have sudo permissions, so we need to escalate.

### 3.4 Privilege Escalation
> Vulnerability Exploited: `Elevation of Privilege using PATH priorities`
> System Vulnerable: `10.10.10.138`
> Root Flag: 
```ruby
"eeba47f60b48ef92b734f9b6198d7226"
```
> Vulnerability Explanation: `There is a cron job named run-parts that runs as root every time a user logins into the system. The run-parts script runs all executables scripts that finds on the specified directory. However the run-parts uses relative path instead of absolute path allowing a malicious hacker to hijack the path and modified the script with a reverse shell.`
> Vulnerability Fix: `Using absolute PATH for scripts that run with root privilege.`
> Severity: `Critical`
> Proof of Concept: `We create a run-parts file on usr/local/bin which will trigger a reverse shell when the run-parts script tries to execute looking for is binary file on the relative path from left to right.`
![](https://lh3.googleusercontent.com/9XzrZwOpgm3ro-XRO8BHYETkngPaxAfe_lr03Vr1lDje2D9pK0bi5nHO3GAASNmCT8Hl7tmhsNBqh3SU_aO6kwHKPRY-z_Ey2h4H49Y9LoSK-Uu8kZj6zFGo7KSM709ZSoH02WcijdU9tSDgiG6Oa38z-htjx2BcbglNpRoYFz_evQqSvP7qgKlr24Qrm8onMfmJNFGackPln_xE5jm_yz9CDNJzEkNoo7SRFyAsNRRuEzJ7k9mLXv5Ps5cDqzS1an1FRab9DuAymygFf_qvxm9TClM4XtOXnduqBfWkeAEXeyWpSRtCKYrnmP7z_n2xF0AutGM9ELKgWajsK3lIhiFROY7_I9pFOE7GOvBvWW94jUvadxUDkbzYIRqTqAiGFONNpKaMPHtRX_ZnBLaqWG9oDmeDscz6pC0m8N4sczb1AFM_mf9ABabX7Wht3_suRtVnsgd1j6sjCe2ATrsEXgwkD1ACDaBb9rd8v9q5jLASh5A3fvoLl_dMFMa1aCXqrzohMXCIMj2Ny_MChzHI-GnH1JH-q9zsdwrppMJ_rGVdnUGeUIVUI80J-8jdHQibDtc4KaAi_Ov6Yl61A4yZ4A7bQpPQMNqGjGT6u_Dfah9ub-vFgPbj5FsmMqCqsQhqyPo5VtoAgw4iaiwUegXkVyqX1umHPKz3RXUIfJnH-Y_YyuPPPE75_4c=w1232-h92-no)

> Screenshots:
We download and install PSPY tool which help us to monitor the processes that run as root on our target machine
![](https://lh3.googleusercontent.com/Q7TqFzeiyG6uUEEs4w--_9SEeCtFRe112j1FDECHHWm2ctk5G7iWWksIS1DuUSHxqp4g9gFB5YkM6xS-jcRWFq64nvrISpMr1UYIG5uNJgKVwQnDaBwdWulac2BRIfemqQEJQ6NdyFrtoM_EuIMnXffI1G9AuKBKektRVf-Cq2mdPZknj5Gn42TlyW8tvLnIheT2xg5QRZuo05SPnlt4lLF7yeVcyerDIPuEVuvtTXhHzBYUGkk4bT3XQgyZK62PfscWRxD9FaooYQ2-9l1OLvxDR9WbboQ7eKNWwCi3_35cLI2YFp4h9xG5ffGQSkAVj8DwO-yyf2wfXHszbMPKzeL-7CbMzUuYByPdnYFdvoghQM_N3njHJbGxyAyfb7gdrFYGQiBDxiwZMFb2DnMV0YeUVocEfu-chHyBMfwCW1_Tl4wU79eDqaLzSgXNpcmr-NNE-iKJsDInBCRawdDy7eVlBMxaqVNpgnd1u19jzSRZWEV8EOvs169WFDG86-Ba39AHFfcdpqsoHG0H2EU7TsH67uqv4EwNLOyKa_si8hd0gNuOo-imqBHVarEnqBK7FGKCLD9FjyTDk9yp8uO36BKkd74mtzIIm4BBs7-c7JnY5xV90iSojTvmYeN-xnC93bBp887txRsWiPPEXaSB2F9DP7hFB4CUOdqWkWGfPujguU5uBAjeheM=w1006-h330-no)

![](https://lh3.googleusercontent.com/YNlZm5zISnUogyX0D8xRl4y1WEyBYXshHt9duuCoGvuaEfLEpp-vt4yNubquSYhNwySwXKQkr5qQhS4jSQL6mqyDL-YjNaXvVbKJz1bPlv9md9PrXZefteT70R3q-d_xW2F263SPO08464wbNYX9tiSrzrK4Zhc-3_4WW2u8CJvQJkVJ3MxWInE5xXBvxyyDp1NEUsVh8PhC8I2aSzz9Dx1y847uN234uhApCBcy2vBg5HSNhCnLgaarevzmYH-GZNEzc8-kkqVlfydEdTpRsf9gqIje_LMo4cPXZPsx5o_cWNOLNfvMveZhid4duWXvSIfeUlE3PeVZxUtI-cQpzIjpmrb3vC2-a4RzGLXqwXkbIKqD33jnoj9EdpjuHkEB48X5MHg14lQKNrQGC71zQYEfW5jGLseYXiYAUa2D5nNvKrhUmVBmq5ntqSCd4c4jY-f-e6DGKAFoM6ZimffUG2Y2WUDsWd8Ah0FL3H_XvvJ_yC8RpyYIsl3-b_oGtQebjNw57xeyGGMqN0WRh_MjutUj53NOITAfF3mn--yjPo7OERHN9oT0XIgHZdKizTrvJz_abk5OaoRiWzTrivnopaw-VCz4JDR8k-5LNhybB5vtwB8T1ubJipBWtGpRg45_ExSlj0qV83dQEImT488GZOnUL54raGv6uVhB0g8MaD3m4Eo6DOeuLrg=w624-h150-no)
















