---
title: Plotted-TMS -- Writeup
date: 2022-02-23
categories: [TryHackMe, Easy]
tags: [tryhackme, ctf, rce, cron, doas]     # TAG names should always be lowercase
---

# Summary -
Easy rated machine which is pretty straight forward except there are some rabbit holes. But as the description says, everything is plotted here.

# Enumeration -
Nmap, here we go...
### nmap -
```bash
# Nmap 7.92 scan initiated Wed Feb 23 08:58:11 2022 as: nmap -T4 -p- -A -oA port_scan 10.10.58.61
Nmap scan report for 10.10.58.61
Host is up (0.16s latency).
Not shown: 65532 closed tcp ports (reset)
PORT    STATE SERVICE VERSION
22/tcp  open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 a3:6a:9c:b1:12:60:b2:72:13:09:84:cc:38:73:44:4f (RSA)
|   256 b9:3f:84:00:f4:d1:fd:c8:e7:8d:98:03:38:74:a1:4d (ECDSA)
|_  256 d0:86:51:60:69:46:b2:e1:39:43:90:97:a6:af:96:93 (ED25519)
80/tcp  open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.41 (Ubuntu)
445/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.41 (Ubuntu)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.92%E=4%D=2/23%OT=22%CT=1%CU=37924%PV=Y%DS=5%DC=T%G=Y%TM=62163EB
OS:D%P=x86_64-pc-linux-gnu)SEQ(SP=105%GCD=1%ISR=10B%TI=Z%CI=Z%II=I%TS=A)OPS
OS:(O1=M506ST11NW7%O2=M506ST11NW7%O3=M506NNT11NW7%O4=M506ST11NW7%O5=M506ST1
OS:1NW7%O6=M506ST11)WIN(W1=F4B3%W2=F4B3%W3=F4B3%W4=F4B3%W5=F4B3%W6=F4B3)ECN
OS:(R=Y%DF=Y%T=40%W=F507%O=M506NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=A
OS:S%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R
OS:=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F
OS:=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%
OS:T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD
OS:=S)

Network Distance: 5 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_smb2-time: Protocol negotiation failed (SMB2)

TRACEROUTE (using port 80/tcp)
HOP RTT       ADDRESS
1   10.57 ms  10.17.0.1
2   ... 4
5   158.61 ms 10.10.58.61

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Feb 23 09:03:41 2022 -- 1 IP address (1 host up) scanned in 330.26 seconds
```

Found /management directory while directory busting on port 445 http server. (Also, found some rabbit holes as well.)

![[Pasted image 20220223200651.png]](/assets/img/tryhackme/plotted-tms/20220223200651.png)

Looks like its running some traffic management system.

![[Pasted image 20220223200742.png]](/assets/img/tryhackme/plotted-tms/20220223200742.png)

Simple search using searchsploit revealed a couple of exploits.

![[Pasted image 20220223200829.png]](/assets/img/tryhackme/plotted-tms/20220223200829.png)

# Exploitation -
50244.py which is written in python2 is worked for me without any fixing. (Tho it did give some exception but it managed to upload web shell so we can use that to get rev shell.)

![[Pasted image 20220223195940.png]](/assets/img/tryhackme/plotted-tms/20220223195940.png)

# Post Exploitation -
Ran linpeas and it revealed something interesting...

![[Pasted image 20220223202211.png]](/assets/img/tryhackme/plotted-tms/20220223202211.png)

On manual enumeration found mysql creds...

![[Pasted image 20220223202606.png]](/assets/img/tryhackme/plotted-tms/20220223202606.png)

Found some username and hashes inside db entry.

![[Pasted image 20220223202652.png]](/assets/img/tryhackme/plotted-tms/20220223202652.png)

And using crackstation, I got the password for puser.

![[Pasted image 20220223202811.png]](/assets/img/tryhackme/plotted-tms/20220223202811.png)

I remember seeing plot_admin user in the machine. Let's try creds for that user...

Okay, fuck it! Remember the linpeas output. Let's enumerate on that. Thank goodness deployed machine started acting funny otherwise I'd have stuck in this rabbit hole much longer.

![[Pasted image 20220223205344.png]](/assets/img/tryhackme/plotted-tms/20220223205344.png)

Since, our user own this folder. We can simply delete backup.sh and replace it with our reverse shell. Start listener and cron will do its job.

![[Pasted image 20220223205457.png]](/assets/img/tryhackme/plotted-tms/20220223205457.png)

### PrivEsc to root -
Ran linpeas again coz last time my rev shell crashed while running it.

![[Pasted image 20220223212338.png]](/assets/img/tryhackme/plotted-tms/20220223205457.png)

Looks like we can run openssl as root... Interesting...
So basically doas is just like sudo.

We can just read the root flag but that won't be fun right? Ehehe~

Gonna put authorized_keys inside /root/.ssh/authorized_keys file so we can get ssh connection easily.

![[Pasted image 20220223220958.png]](/assets/img/tryhackme/plotted-tms/20220223220958.png)

Phew... Let's try to ssh and we should get root shell. (Hopefully...)

![[Pasted image 20220223221110.png]](/assets/img/tryhackme/plotted-tms/20220223221110.png)