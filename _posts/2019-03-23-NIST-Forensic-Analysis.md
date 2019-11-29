---
layout: post
title: NIST Forensic Analysis
category: Blue Team
---

# NIST Forensic Toolkit

- **Digital Forensinc Workstation**
- **Forensinc Investigation suite or Forensic software**
- **Writeblockers**
- **Forensic drive duplicators**
- **Wiped drives and wiped remoable media**
- **Cables and adapters**
- **Camera**
- **Labeling documenting tools**
- **Chain-of-custody forms, notebooks and checklists.***

![CHAINOFCUSTUDY](https://i1.wp.com/www.andreafortuna.org/wp-content/uploads/2018/04/ChainOfCustody.jpg?w=678&ssl=1)

# Forensic Software

|Forensic Suites|
|---------------|
|FTK Access Data|
|EnCase|
|Caine|
|Autopsy|
|SIFT|

|Tools|
|-----|
|DumpIt|
|Memoryze|
|dd|
|WinDbg|

# Imaging Media and Drives
#### Forensinc images match exactly the original source drive, volume, partition, or device. including Slack space and unallocated space.
#### Slack space is the space left when a file is written.
#### Unallocated space is space that has not been partitioned.
![slack-space](https://itknowledgeexchange.techtarget.com/overheard/files/2016/07/slack-space.png)

## Analysis Utilities
- Timelines of systems changes
- Validation tools that check known good version of files against those found on a system.
- File analysis capabilities that can look at filesystem metadata (MASTER FILE TABLE FOR NTFS) to identify changes, access and deletions.
- Windows Registry analysis
- Log file parsing and review

### Live memory capture analysis
#### Memory dump analysis is use when recovering decryption keys for full disk encryption products like Bitlocker
#### Hibernation files and crash dumps can both contain the data needed to decrypt the drive

# The Forensic Process
1. Determine what are you trying to find out
2. Outline the location and types of data that would help you answer step 1
3. Document and review your plan.
4. Acquire and preserve evidence
5. Perform initial analysis.
6. Use initial analysis to guide further work.
7. Report on the findings of the investigation.

# Order of volatility
![volatility](https://getcertifiedgetahead-1165.kxcdn.com/wp-content/uploads/2016/08/OrderOfVolatility2.png)

# Target Locations

|Windows|Use|
|-------|---|
|Windows Registry| Information about files and services, locations of deleted files, evidence of applications being run|
|Autorun Keys| Programs set to run at startup (often associated with malware or compromised)|
|Master File Table| Details of inactive/removed records|
|Event logs| Logins,services start/stop/, evidence, of applications being run|
|Volume Shadow copy| Point in time information from prior actions|
|User directory and files| logged-in users artifacts|
|Recycle Bin| Files that were intended to be deleted but forgotten|
|Hibernation files and memory dumps| Memory artifacts of commands to run|
|Removable drives| System logs may indicates drives were plugged in|

# Imaging Live Systems
#### When dealing with  full disk encryption, malware, an image may need to be collected while the system is running.
#### Live imaging may not obtaion some desirable data:
- Can leave remnants due to the imaging utility being mounted from a removable driver.
- The contents of a drive or memory may change during the imaging proccess.
- Malware or other software may be able to detec the imaging tool and could take action to avoid it or disable it.
- live images tipically do not include unallocate space.

# USB Historian Tool
![usb-historian](https://4discovery.com/wp-content/uploads/2018/10/usb_ss_03.png)

# Memory Capture
#### FMEN and LiME, linux modules that allow access to physical memory. FMEN is designed to be used with dd.
#### DumpIt, a windows memory capture tool that simply copies a system's physical memory to the folder where the DumpIt program is.
#### The Volatility Framework supports a broad range of operating systems. including windows, Linux, and MacOS.
#### FTK and EnCase have built-in memory capture tools.

# Core Dump and Hibernation Files
#### Contain the contents of live memory, they can include data that might otherwise be accessible on the drive of the system, such as #### memory-resident encryption keys ormalware that runs only on memory.Ex. ```%SystemRoot%\MEMORY.DMP```

# Mobile Device Acquisitions
## There are four primary modes of data acquisition:
#### Physical, SIM card, emory card, backups
#### Logical, requires forensic tool to create an logical image of the device
#### Manual access, unlocked phones, and taking pictures and notes.
#### Filesystem, deleted files as well exiting directories.
