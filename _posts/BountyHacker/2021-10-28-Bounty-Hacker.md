---
layout: post
author: Temerio
---
![Bounty Hacker](https://i.imgur.com/100/100/qodkcem.jpg)
*You talked a big game about being the most elite hacker in the solar system. Prove it and claim your right to the status of Elite Bounty Hacker!*
## Reconnaissance

```bash
┌──(temerio㉿Hackademy)-[~]
└─$ sudo nmap -p- -sS --min-rate 5000 -Pn 10.10.107.126
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-10-28 16:38 EDT
Nmap scan report for 10.10.107.126
Host is up (0.27s latency).
Not shown: 55529 filtered ports, 10003 closed ports
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 127.96 seconds
```
