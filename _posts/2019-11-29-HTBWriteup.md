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
**Reconnaissance
- Information Gathering
- Service Enumeration
**Exploitation
**Privilege Escalation
**Reporting (This technical report)

