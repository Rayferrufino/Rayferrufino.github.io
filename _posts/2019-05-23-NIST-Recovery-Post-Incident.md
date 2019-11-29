---
layout: post
title: NIST Recovery and Post-Incident Response
author: Rayzer0
category: Blue Team
date: 2019-3-25
---

# NIST Containment  Strategy Criteria

- Potential damage to and theft of resources
- Need for evidence Preservation
- Service availability (network connectivity, services porvided to external parties)
- Effectiveness of strategy (partial containment, full containment)
- Duration of the solution (emergency workaround to be removed in four hours, temporary workaround in two weeks, permanent solution)

# Segmentation
#### Network segmentation is a proactive strategy to prevent the spread of future security incidents.

# Isolation
#### Isolation is simply taking segmentation to the next level. Affected systems are completely disconnected from the remainder of the network although they may be still able to cominucate with each other and the attacker over the internet.

## Warning
#### Segmentation and isolation strategies carry with them significant risk to the organization. First the attacker retains access to the compromised system, creating potential for further  expansion of the security incident.Second, the compromised system may be used to attack other systems on the internet.

### Isolating the Attacker
#### Isolating the attacker is an variation on the isolation strategy and depends on the use of ***Sandbox systems*** that are set up purely to monitor the attacker activity and do not  contain any info or resources of value to the attacker. 

# Removal 
#### Removal of compromised system for the network is the strongest containment techniques in the cybersecurity analyst's incident response toolkit.

#### Removal Ins't Foolproof

# Evidence Gathering and Handling
#### NIST recommends that investigators maintain a detailed evidence log that includes the following:
- Identifying info (location,serial number, model number, host-name, IP  addresses)
- Name, title, and phone number of each individual who collected or handled the evidence during the investigation
- Time and Date 
- Locations where the evidence was stored.

# NIST Identifying Attackers
#### Indentifying an attacking host can be time consuming and futile process that can preveng a team from achieving its primary goal, minimizing the business impact

# NIST Incident, Eradication, and Recovery

#### Eradication is to remove any of the artifacts of the incident that may remain on the organization's network.
#### Recovery focuses on restoring normal operations and correcting security controls deficiencies that may have led to the attack.

# NIST Sanitazation and Secure Disposal
#### Generally speaking, there are three options available for the secure disposition of media containing sensitive information:

|Clear| Applies logical techniques to sanitaze data in all user-addresable storage locations for protection against simple non-invasive data recovery techniques|
|---|---|

|Purge|Applies physical or logical techniques that render target data recovery infeasible using state-of-the-art laboratory techniques.Ex orverwriting, Degaussing.|
|---|---|

|Destroy|Renders target data recovery infeasible using state-of-the-art technique.Ex Desintegration, pulverazation,melting, and incineration|
|---|---|

![sanitization](https://cdn.shortpixel.ai/client/q_lossless,ret_img,w_629/https://www.ewastesecurity.com/wp-content/uploads/2016/11/Screen-Shot-2017-05-03-at-2.09.40-PM.png)

