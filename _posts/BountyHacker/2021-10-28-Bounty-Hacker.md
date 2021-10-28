---
layout: post
author: Temerio
---
![Bounty Hacker](https://i.imgur.com/qodkcem.jpg)
*You talked a big game about being the most elite hacker in the solar system. Prove it and claim your right to the status of Elite Bounty Hacker!*

Puedes encontrar la Room [aquí](https://tryhackme.com/room/cowboyhacker) 

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
Vemos 3 puertos abiertos y nos conectamos al sitio web(Port 80)
![WebPage](https://imgur.com/swkGsAb)
Al no encontrar nada probamos con el siguiente puerto FTP (Port 21) e intentamos loguearnos como "Anonymous".
```bash
┌──(temerio㉿Hackademy)-[~/Desktop/Thm/BountyHacker]
└─$ ftp 10.10.107.126    
Connected to 10.10.107.126.
220 (vsFTPd 3.0.3)
Name (10.10.107.126:temerio): Anonymous
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
```
Al dejarnos ingresar procedemos a listar el contenido.
```bash
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-rw-r--    1 ftp      ftp           418 Jun 07  2020 locks.txt
-rw-rw-r--    1 ftp      ftp            68 Jun 07  2020 task.txt
226 Directory send OK.
```
El File Transfer Protocol (FTP) contiene 2 archivos llamados:
*locks.txt
*task.txt

Procedemos a descargarlo mediante el comando "get".
```bash
ftp> get locks.txt
local: locks.txt remote: locks.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for locks.txt (418 bytes).
226 Transfer complete.
418 bytes received in 0.00 secs (2.5071 MB/s)
ftp> get task.txt
local: task.txt remote: task.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for task.txt (68 bytes).
226 Transfer complete.
68 bytes received in 0.00 secs (535.5343 kB/s)
```
