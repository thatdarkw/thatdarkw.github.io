---
title: Wonderland -- Writeup
date: 2021-06-05
categories: [TryHackMe, Medium]
tags: [tryhackme, ctf, wordpress, bruteforce, suid]     # TAG names should always be lowercase
img_path: /assets/img/tryhackme/wonderland/
---

# Summary -
"Fall down the rabbit hole and enter wonderland." As the room descriptions says, pretty fun machine just don't get caught in rabbit holes.

# Enumeration -
Lets run quick nmap scan.

```bash
sudo nmap -A <ip>
```

This returned 2 open ports.

port      |service
----------|----------
22        |SSH
80        |Web server

Let's enumerate port 80.
Nothing much on home page except some picture and words. Picture is from /img so let's check it and download them to check for steganography.
Using steghide on white_rabbit_1.jpg with no pass extracts hint.txt file which says follow the r a b b i t.

![steghide](steghide.png)

Interesting....

Let's directory burst cause no idea what this hint means.

```
dirsearch -u http://10.10.163.161/ -e php,txt,html -x 400,401,403 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -r
```
This returns 3 interesting directories which are /r/ , /poem/, and /img/. Already checked /img/ so let's check this two.
/poem contains a nice poem. Give it a read!

/r contains some message.
I used -r so its gonna directory burst every directory it find with all dictionary words but I'm gonna stop it after 10% done cause that's enough.

No interesting directory after /poem/ but on /r/ we found another directory which is /r/a/. It contains some conversation type message. Again after some time I stop the scan and tell dirsearch to start on /r/a/.

Now, dirsearch founds /r/a/b/...

Interesting huh? Remember the hint. Go on like this or you already know from the hint now. I dirsearch all the way till end after then I noticed r a b b i t hint actually suggests to go to the /r/a/b/b/i/t/ directory.

Now, in the directory check the page source where some creds are hidden.

![alice_creds](creds.png)

# Exploitation -
Use Alice's creds to ssh to the machine.

# Post Exploitation -
Woah... We are alice user now. But still a long road remains....

```
sudo -l
```

![sudo -l](sudo_l.png)

Above command reveals /usr/bin/python3.6 /home/alice/walrus_and_the_carpenter.py can be run as rabbit user.

Let's read walrus_and_the_carpenter.py . We don't have write privilege but inside this file is an import statement which imports random.

Hmmm... So, to exploit it we can create a random.py which will contain python reverse shell and we'll place this in the same folder as walrus_and_the_carpenter.py file so when it imports it calls random from our random.py file which will generate reverse shell. Start the listener and do this.

```
sudo -u rabbit /usr/bin/python3.6 /home/alice/walrus_and_the_carpenter.py
```

Phew... We got rabbit user but its not over yet. In rabbit user's home directory contains a root suid. We don't know what it does so let's grab it to our machine and analyse it using Ghidra. Copy it using python http server or using scp however you want.

![teaParty](teaParty.png)

After analysing it in Ghidra, head to main function to see c code.
In there, the program is setting suid and sgid and then printing some statement and getting some input and then stops.
Found the way to exploit it? Congrats! If not allow me to explain.

![main_function](main_c.png)

In the program you can see its using system() to do system commands. For first command its using full path but for second which is date is not. So, yes we can modify our path and point it to the date executable we gonna create.

Create date file which will contain reverse shell. Make sure to give it shebang so it'll know where this belongs. Start listener and get shell.

Congrats!!! You are now hatter user. Use the creds and then ssh directly as hatter to get stable access.
If you ran linpeas before you may notice we can now get direct root from here. If not then go ahead and run linpeas which will highlight 2 capabilities having set as setuid. Why I didn't say this before? Cause ofcourse, only hatter can execute them.

![linpeas](capabilities.png)

Grab the capability exploit for perl from gtfobins and use it to get root.
```
perl -e 'use POSIX qw(setuid); POSIX::setuid(0); exec "/bin/sh";'
```

![perl_capability](perl_capability.png)

Congrats, root is owned now.
