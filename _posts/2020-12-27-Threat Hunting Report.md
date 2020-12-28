---
layout: post
title:  "Threat Hunting Report"
description : The following are examples of how we perform Threat Hunting reporst for our clients.
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

A quick search of the provided URL returns multiple resources mentioning it exactly. The best part here is these are hits on the exact URL we are looking for.

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
