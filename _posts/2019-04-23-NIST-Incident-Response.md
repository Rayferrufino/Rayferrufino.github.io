---
layout: post
title: NIST Incident Response
author: Rayzer0
category: Blue Team
date: 2019-6-13
---

# Phases of Incident Response 
![incidenr-chart](https://phoenixts.com/wp-content/uploads/2015/02/incidentresponse.png)

## PREPARATION

### NIST RECOMEMNDED INCIDENT RESPONSE TOOLKIT
- Digital Forensic Workstations
- Backup Devices
- Spare Server and networking equipment
- Forensic and packet capture software
- Laptops for data collection, analysis and reporting
- Bootable USB media containing trusted copies of forensic tools

## DETECTION AND ANALYSIS

### NIST FOUR MAJOR SECURITY EVENTS INDICATORS

- Alerts
- Logs
- Publicly Available information
- People

### NIST ACTIONS TO EFFECTIVE INCIDENT RESPONSE

- Profile network & systems to measure the characteristics of expected activity
- Understand normal behavior of users, systems, networks, and applications
- Create a loggin policy that specifies the information  that must be logged by systems, applications, and network devices
- Perform event correlation to combine information from multiple sources.
- Synchronize clocks accross servers, workstations, and network devices.
- Capture netowork traffic as soon as an incident is suspected
- Filter information to reduce clutter
- Seek assistence from external resources

## CONTAINMENT, ERADICATION, AND RECOVERY
1. Select a containment strategy appropiate  to the incident circumstances.
2. Implement the selected containment strategy, as needed to limit the damage caused by the incident.
3. Gather additional evidence, as needed to support the response effort and potential legal action.
4. Identify the attackers and attacking systems.
5. Eradicate the effects of the incident and recover normal business operations.

## POST INCIDENT ACTIVITY
### NIST lessons-learned processes:
- Exactly what happened and at what times?
- How well did staff and management  perform  in responding to the incident
- Were the documented procedures followed? Where they adequated?
- What information was needed sooner?
- Were any steps or actions taken that might have inhibited the recovery?
- What addional tools or resources are needed to detect, analyze, and mitigate future incidents?

# Policy
- Approved by the highest level, preferably CEO
- Write the policy in a manner that makes it  timeless
- Policy is not use to describe technical content or technologies 

## NIST POLICY KEY ELEMENTS
- Statement of management of the policy
- Purpose and objectives of the policy
- Scope of the policy
- Definition of roles, responsabilities, and level of authority
- Performance measures for the CSIRT
- Reporting and contact forms


## Procedures and Playbooks
provides detailed, tactical information that CSIRT members need when responding to an incident.

## External Communications
- Law Enforcement
- Information Sharing Partners
- Vendors 
- Public Relations PR

# NIST Threat Clasification

- External/Removable media
- Attrition
- Web
- Email
- Impersonation
- Improper usage
- Lost or theft
- Unknown

# NIST Fuctional Impact Categories

|NIST Fuctional Impact Categories| Definition|
|--------------------------------|-----------|
|NONE                            |No effect to the organization's ability to provide all services to all users.
|LOW                             |Minimal effect: the organization can still provide all critical services to all users but has lost efficiency|
|MEDIUM                          |Organization has lost the ability to provide a critical service to a subset of system users.|
|HIGH                            |Organization is no longer able to provide some critical services to any users.|

# NIST Recoverability effort

|NIST Recoverability Effort      | Definition|
|--------------------------------|-----------|
|REGULAR                         |Time to recover is predictable with existing resources.|
|SUPPLEMENTED                    |Time to recover is predictable with additional resources.|
|EXTENDED                        |Time to recover is unpredictable, additional resources and out-side help are needed|
|NOT RECOVERABLE                 |Recover from incident is not possible, launch investigation|

# NIST Information Impact 

|NIST Information Impact     | Definition|
|--------------------------------|-----------|
|NONE                        |No info was exfiltrated, changed, deleted, or compromised|
|Privacy Breach              |Sensitive PII odf taxpayers, employees, beneficiaries was accessed or exfiltrated|
|Propietary Breach           |Unclassified proprietary info such as protected critical infrastructure info PCII was accessed or exfiltrated|
|Integrity Loss              |Sensitive or propretary info was changed or deleted|
