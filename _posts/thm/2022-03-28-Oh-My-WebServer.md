---
title: Oh My WebServer -- Writeup
date: 2022-03-05
categories: [TryHackMe, Medium]
tags: [tryhackme, ctf, rce, docker, omi]     # TAG names should always be lowercase
---

# Summary -
Medium rated machine whose Apache server is vulnerable(cve-2021-42013) to path traversal and rce. After getting initial access, we have to escape docker.

# Enumeration -
nmap, here we go...
### nmap -
```bash
# Nmap 7.92 scan initiated Sat Mar  5 09:53:37 2022 as: nmap -T4 -p- -A -oA port_scan 10.10.89.68
Nmap scan report for 10.10.89.68
Host is up (0.21s latency).
Not shown: 65533 filtered tcp ports (no-response)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 e0:d1:88:76:2a:93:79:d3:91:04:6d:25:16:0e:56:d4 (RSA)
|   256 91:18:5c:2c:5e:f8:99:3c:9a:1f:04:24:30:0e:aa:9b (ECDSA)
|_  256 d1:63:2a:36:dd:94:cf:3c:57:3e:8a:e8:85:00:ca:f6 (ED25519)
80/tcp open  http    Apache httpd 2.4.49 ((Unix))
|_http-title: Consult - Business Consultancy Agency Template | Home
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.49 (Unix)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Crestron XPanel control system (90%), ASUS RT-N56U WAP (Linux 3.4) (87%), Linux 3.1 (87%), Linux 3.16 (87%), Linux 3.2 (87%), HP P2000 G3 NAS device (87%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (87%), Linux 2.6.32 (86%), Linux 2.6.39 - 3.2 (86%), Infomir MAG-250 set-top box (86%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 5 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 80/tcp)
HOP RTT       ADDRESS
1   8.53 ms   10.17.0.1
2   ... 4
5   155.98 ms 10.10.89.68

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Mar  5 10:06:33 2022 -- 1 IP address (1 host up) scanned in 776.46 seconds
```

**port 80** -
Enumerated website but cannot find any interesting stuff... Back to basics. So, searched for Apache 2.4.49 and found out it is vulnerable to path traversal and rce. Great...
There is a readily available exploit which works but let's do it a bit manually...

[https://blog.qualys.com/vulnerabilities-threat-research/2021/10/27/apache-http-server-path-traversal-remote-code-execution-cve-2021-41773-cve-2021-42013](https://blog.qualys.com/vulnerabilities-threat-research/2021/10/27/apache-http-server-path-traversal-remote-code-execution-cve-2021-41773-cve-2021-42013)

_Above article explained it nicely so do give it a read..._

So, let's get Burp started and intercept the request...
Path traversal was failing so I tried rce one and that worked!

![[Pasted image 20220305222153.png]](/assets/img/tryhackme/ohmywebserver/20220305222153.png)

So, with that we should be able to get reverse shell.

# Exploitation -
Let's try to get reverse shell now.

![[Pasted image 20220305222442.png]](/assets/img/tryhackme/ohmywebserver/20220305222442.png)

So, python3 is installed... Let's use python3 reverse shell then.

![[Pasted image 20220305222548.png]](/assets/img/tryhackme/ohmywebserver/20220305222548.png)

And we got a reverse shell.

# Post Exploitation -
Let's run linpeas.

![[Pasted image 20220305223041.png]](/assets/img/tryhackme/ohmywebserver/20220305223041.png)

Looks like we are inside a docker container...

![[Pasted image 20220305223231.png]](/assets/img/tryhackme/ohmywebserver/20220305223231.png)

Interesting... setuid on python3... Gtfobins here we go...
```bash
/usr/bin/python3.7 -c 'import os; os.setuid(0); os.system("/bin/bash")'
```

![[Pasted image 20220305223405.png]](/assets/img/tryhackme/ohmywebserver/20220305223405.png)

We got root on this container... Now, just need to escape it ig... Sigh...

![[Pasted image 20220306142752.png]](/assets/img/tryhackme/ohmywebserver/20220306142752.png)
_Interesting..._

No mention of 10. ip. So, .1 is probably the host machine.

![[Pasted image 20220306143430.png]](/assets/img/tryhackme/ohmywebserver/20220306143430.png)

Well, tried nmap version reveal but no luck. That looks like winrm port but its a Linux machine.

![[Pasted image 20220306204244.png]](/assets/img/tryhackme/ohmywebserver/20220306204244.png)

Found this on hackbooks website. It even has a link for the latest CVE-2021-38647 (OMIGOD) exploit which looks a bit similar to room name.

![[Pasted image 20220306205125.png]](/assets/img/tryhackme/ohmywebserver/20220306205125.png)
_[cve-2021-38647](https://github.com/horizon3ai/CVE-2021-38647)_

To get a root shell, host a python3 reverse shell file and upload that to the server and use it to get reverse shell.

![[Pasted image 20220306205722.png]](/assets/img/tryhackme/ohmywebserver/20220306205722.png)
