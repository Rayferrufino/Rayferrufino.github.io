---
layout: post
title: Log Series 1 AWS
author: Rayzer0
category: Blue Team
date: 2019-1-28
---


# Reading network logs

![AWS-capture](https://media.amazonwebservices.com/blog/2015/flow_see_a_stream_2.png)

# AWS Flow Log Syntax 
```bash
<version> <account-id> <interface-id> <srcaddr> <dstaddr> <srcport> <dstport> <protocol> <packets> <bytes> <start> <end> <action> <log-status>
```

| Field         | Description   |
| ------------- |:-------------:| 
| version       | The VPC Flow Logs version |
| account-id    | The AWS account ID for the flow log.      |
| interface-id  | The ID of the network interface for which the traffic is recorded.    |
| srcaddr       | The source IPv4 or IPv6 address. The IPv4 address of the network interface is always its private IPv4 address. |
| dstaddr       | The destination IPv4 or IPv6 address. The IPv4 address of the network interface is always its private IPv4 address.|     
| srcport       | The source port of the traffic.      |
| dstport       |The destination port of the traffic. |
| protocol      | The IANA protocol number of the traffic      |
| packets       | The number of packets transferred during the capture window.      |
| bytes         | The number of bytes transferred during the capture window.      |
| start         | The time, in Unix seconds, of the start of the capture window.      |
| end           | The time, in Unix seconds, of the end of the capture window. |
| action        | The action associated with the traffic:ACCEPT REJECT.|
| log-status    | The logging status of the flow log: OK: Data is logging normally to the chosen destinations. NODATA SKIPDATA      |


# Flow Log Record Examples
## Flow Log Records for Accepted and Rejected Traffic

The following is an example of a flow log record in which SSH traffic (destination port 22, TCP protocol) to network interface eni-abc123de in account 123456789010 was allowed:
```
2 123456789010 eni-abc123de 172.31.16.139 172.31.16.21 20641 22 6 20 4249 1418530010 1418530070 ACCEPT OK
```
The following is an example of a flow log record in which RDP traffic (destination port 3389, TCP protocol) to network interface eni-abc123de in account 123456789010 was rejected:
```bash
2 123456789010 eni-abc123de 172.31.9.69 172.31.9.12 49761 3389 6 20 4249 1418530010 1418530070 REJECT OK
```
The following is an example of a flow log record in which no data was recorded during the capture window:
```
2 123456789010 eni-1a2b3c4d - - - - - - - 1431280876 1431280934 - NODATA
```
The following is an example of a flow log record in which records were skipped during the capture window:
```
2 123456789010 eni-4b118871 - - - - - - - 1431280876 1431280934 - SKIPDATA
```

## Security Group and Network ACL Rules
```
2 123456789010 eni-1235b8ca 203.0.113.12 172.31.16.139 0 0 1 4 336 1432917027 1432917142 ACCEPT OK
2 123456789010 eni-1235b8ca 172.31.16.139 203.0.113.12 0 0 1 4 336 1432917094 1432917142 REJECT OK
```
An ACCEPT record for the originating ping that was allowed by both the network ACL and the security group, and therefore was allowed to reach your instance.

A REJECT record for the response ping that the network ACL denied.

