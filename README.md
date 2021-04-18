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



























