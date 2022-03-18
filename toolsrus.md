# ToolsRUS

This is my write-up for the room [ToolsRus] (https://tryhackme.com/room/toolsrus)

Your challenge is to use the tools listed below to enumerate a server, gathering information along the way that will eventually lead to you taking over the machine.

This task requires you to use the following tools:

Dirbuster
Hydra
Nmap
Nikto
Metasploit
## Difficulty
My Difficulty: Easy

Try Hackme Difficulty: Easy

<p align="center">
<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/a/a7/Toys_%22R%22_Us_logo.svg/1280px-Toys_%22R%22_Us_logo.svg.png" style="width:25%;">
</p>

## Reconnaissance

Host = `10.10.244.140`

## Enumeration / Scanning

Let's start with an initial Nmap scan.

### Service / Port Enumeration
## NMAP

```console
nmap -sC -sV 10.10.123.159
Starting Nmap 7.80 ( https://nmap.org ) at 2022-03-18 11:20 PDT
Nmap scan report for 10.10.123.159
Host is up (0.15s latency).
Not shown: 996 closed ports
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 7b:61:82:61:88:8e:99:80:2e:23:a5:d2:55:c0:60:e6 (RSA)
|   256 5d:56:ff:7f:3c:1e:7f:90:0b:b2:90:56:d0:f9:fa:e5 (ECDSA)
|_  256 a8:6b:b8:2b:f2:fa:0d:58:ff:8a:50:01:79:21:ca:75 (ED25519)
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
1234/tcp open  http    Apache Tomcat/Coyote JSP engine 1.1
|_http-favicon: Apache Tomcat
|_http-server-header: Apache-Coyote/1.1
|_http-title: Apache Tomcat/7.0.88
8009/tcp open  ajp13   Apache Jserv (Protocol v1.3)
|_ajp-methods: Failed to get a valid response for the OPTION requestnmap -sC -sV 10.10.123.159
Starting Nmap 7.80 ( https://nmap.org ) at 2022-03-18 11:20 PDT
Nmap scan report for 10.10.123.159
Host is up (0.15s latency).
Not shown: 996 closed ports
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 7b:61:82:61:88:8e:99:80:2e:23:a5:d2:55:c0:60:e6 (RSA)
|   256 5d:56:ff:7f:3c:1e:7f:90:0b:b2:90:56:d0:f9:fa:e5 (ECDSA)
|_  256 a8:6b:b8:2b:f2:fa:0d:58:ff:8a:50:01:79:21:ca:75 (ED25519)
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
1234/tcp open  http    Apache Tomcat/Coyote JSP engine 1.1
|_http-favicon: Apache Tomcat
|_http-server-header: Apache-Coyote/1.1
|_http-title: Apache Tomcat/7.0.88
8009/tcp open  ajp13   Apache Jserv (Protocol v1.3)
|_ajp-methods: Failed to get a valid response for the OPTION request
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 20.72 seconds

Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 20.72 seconds
```
Next lets run a dir buster (wasn't getting results, so I had to reset the box with its new IP being 10.10.244.140, again had to reset it. Not sure what the issue is, but it seems that if I attempt to do multiple things on the box i.e. attempt to access web interface while scanning it crashes. Hopfullying final IP: 10.10.110.225)

## Gobuster
```console
gobuster dir -u http://10.10.110.225 -w /home/noob2uub/Documents/Wordlists/common.txt 
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.110.225
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /home/noob2uub/Documents/Wordlists/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/03/18 11:35:44 Starting gobuster in directory enumeration mode
===============================================================
/.hta                 (Status: 403) [Size: 292]
/.htaccess            (Status: 403) [Size: 297]
/.htpasswd            (Status: 403) [Size: 297]
/guidelines           (Status: 301) [Size: 319] [--> http://10.10.110.225/guidelines/]
/index.html           (Status: 200) [Size: 168]                                       
/protected            (Status: 401) [Size: 460]                                       
/server-status        (Status: 403) [Size: 301]                                       
                                                                                      
===============================================================
2022/03/18 11:36:55 Finished
===============================================================
```

Navigating to http://10.10.135.157/guidelines/ shows us this. We have a user name and confirming TomCat server thats runing on port 1234

(https://user-images.githubusercontent.com/68706090/159064424-42c10906-6b0b-49e9-95e0-7fcd8690afb4.png)
