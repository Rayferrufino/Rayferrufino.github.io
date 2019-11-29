---
layout: post
title: NIST Security Content Automation Protocol
author: Rayzer0
category: Blue Team
date: 2019-2-8
---

# Security Content Automation Protocol SCAP 

| Security Content Automation Protocol SCAP               |
|---------------------------------------------------------|
|   **Common Configuration Enumaration CCE**              |
|Provides a stantard nomenclature for system configuration issues|
|**Common Platform Enumeration CPE**                      |
|Provides a standard for describing product names and services|
|**Common Vulnerability and Exposure    CVE**             |
|Provides a standard nomenclature for describing security related software flaws|
|**Common Vulnerability Scoring Sytem CVSS**|
|Provides a standardized approach for measuring and describing  the severity of security related sotfware flaws|

## Prioritizing Remediation
#### Criticality of the systems and Informartion Affected by the Vunerability
Consider what CIA  the vulnerability affects and it is consequences.
#### Difficulty of Remediation the Vulnerability
If fixing a vulnerability will require expensive human or finacial resources  that should be weight in.
#### Severety of the Vulnerability
The more severe the issue is,  the more important it is to correct it.
#### Exposure of the Vulnerability
can the vulnerability be easily exploit? if not focus on those that are easy to be exploited first

## Barriers to vulnerability Scanning
- **service degradations**
- **Customer Commitments**
- **IT governance**

# Common Vunerability Scoring System CVSS

|Access Vector Metric| Describes how an attacker would exploit the vuln|
|--------------------|-------------------------------------------------|
|**VALUE**           |             **DESCRIPTION**                     |
|Local "L"           |The attacker must have physical or logical acces to the affected system|
|Adjacent Network "A"|The attacker must have access to the local network that the affected system is connected to|
|Network "N"         |The attacker can exploit the vuln remotely over the network|

|Access Complexity Metric| Describes the difficulty of exploiting the vuln|
|------------------------|------------------------------------------------|
| **VALUE**              |             **DESCRIPTION**                    |
| HIGHT "H"              |Exploting the vuln requires specialized conditions that would be difficult to find|
| MEDIUM "M"             |Exploting the vuln requires somewhat specialized conditions|
| LOW "L"                |Exploting the conditions doesn't require any specialized conditions|


|Authentication  Metric  | Describes the authentication hurdles that an attacker would need to clear to exploit|
|------------------------|------------------------------------------------|
| **VALUE**              |             **DESCRIPTION**                    |
|MULTIPLE 'M'            |Attackers would neeed to authenticate two or more times to exploit the vuln|
|SINGLE 'S'              |Attacker would need to authenticate once to exploit the vuln|
|NONE 'N'                |Attackers do not need to authenticate to exploit the vuln|

|Confidentiality Metric  | Describes the type of info disclosure that might occur if an attacker successfuly exploits the vuln|
|------------------------|------------------------------------------------|
| **VALUE**              |             **DESCRIPTION**                    |
|NONE 'N'                |Thre is no confidentiality impact|
|PARTIAL 'P'             |Access to some info is possible, but the attacker does not have control over what information is compromised|
|COMPLETE 'C'            |All information in the system is compromised|


|Integrity  Metric       | Describes the type of info alteration that ight occur if an attacker succesfuly exploits the vuln|
|------------------------|------------------------------------------------|
| **VALUE**              |             **DESCRIPTION**                    |
|NONE 'N'                |There is no integrity impact                    |
|PARTIAL 'P'             |Modification of some info is possible, but the attacker does not have control over what info is modified|
|COMPLETE 'C'            |The integrity of the system is totally  compromised, and the attacker may change any info at will|

|Availability  Metric    | Describes the type of disruption that might occur if an attacker succesfully exploits the vulnerability|
|------------------------|------------------------------------------------|
| **VALUE**              |             **DESCRIPTION**                    |
|NONE 'N'                |There is no availability impact                    |
|PARTIAL 'P'             |The performance of the system is degraded|
|COMPLETE 'C'            |The system is completely shutdown|


# Interpreting CVSS Vector

|Access Vector |Network Vector |Authentication|Confidentiality|Integrity| Availability|
|--------------|---------------|--------------|---------------|---------|-------------|
| **AV:N**     |  **AC:M**     |**Au:N**      | **C:P**       |**I:N**  | **A:N**     |


