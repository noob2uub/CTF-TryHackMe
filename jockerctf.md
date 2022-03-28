JokerCTF
This is my write-up for the room [JokerCTF](https://tryhackme.com/room/jokerctf)

Description: We have developed this lab for the purpose of online penetration practices. Solving this lab is not that tough if you have proper basic knowledge of Penetration testing. Let’s start and learn how to breach it.

Enumerate Services
- Nmap
Bruteforce
- Performing Bruteforce on files over http
- Performing Bruteforce on Basic Authentication
Hash Crack
- Performing Bruteforce on hash to crack zip file
- Performing Bruteforce on hash to crack mysql user
Exploitation
- Getting a reverse connection
- Spawning a TTY Shell
Privilege Escalation
- Get root taking advantage of flaws in LXD

Difficulty
My Difficulty:

Try Hackme Difficulty: Medium



Reconnaissance
Host = 10.10.150.7581.3

Enumeration / Scanning
Let's start with an initial Nmap scan.

Service / Port Enumeration

# NMAP

```console
nmap -sC -sV -A -T4 -Pn 10.10.150.75
Starting Nmap 7.92 ( https://nmap.org ) at 2022-03-28 09:30 PDT
Nmap scan report for 10.10.150.75
Host is up (0.15s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 ad:20:1f:f4:33:1b:00:70:b3:85:cb:87:00:c4:f4:f7 (RSA)
|   256 1b:f9:a8:ec:fd:35:ec:fb:04:d5:ee:2a:a1:7a:4f:78 (ECDSA)
|_  256 dc:d7:dd:6e:f6:71:1f:8c:2c:2c:a1:34:6d:29:99:20 (ED25519)
80/tcp   open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: HA: Joker
|_http-server-header: Apache/2.4.29 (Ubuntu)
8080/tcp open  http    Apache httpd 2.4.29
| http-auth: 
| HTTP/1.1 401 Unauthorized\x0D
|_  Basic realm=Please enter the password.
|_http-title: 401 Unauthorized
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: Host: localhost; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 17.49 seconds
```

We see that ports 22, 80, and 8080 are open

### Browsing to 10.10.150.75

![Screenshot_2022-03-28_09-38-39](https://user-images.githubusercontent.com/68706090/160445902-dcb477e5-c1f4-430d-b5cf-faf68d790464.png)

Going through this page there are a few images with joker quotes, they may come in handy, but lets keep looking for the first secret. 

![Screenshot_2022-03-28_09-40-30](https://user-images.githubusercontent.com/68706090/160446146-5d4e48ac-70ff-44b9-acfe-15c76f66d0ad.png)

There is allot to read in this section, so let start from the top and work our way down. Nothing but a good story, so lets run go buster. I like to start with a common or small wordlist then run a larger one to follow up, just so i can start searching things and then find out if i missed anything. 

# GOBUSTER
```console
gobuster dir -u http://10.10.150.75 -w /home/noob2uub/Documents/Wordlists/common.txt 
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.150.75
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /home/noob2uub/Documents/Wordlists/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/03/28 09:48:24 Starting gobuster in directory enumeration mode
===============================================================
/.hta                 (Status: 403) [Size: 277]
/.htaccess            (Status: 403) [Size: 277]
/.htpasswd            (Status: 403) [Size: 277]
/css                  (Status: 301) [Size: 310] [--> http://10.10.150.75/css/]
/img                  (Status: 301) [Size: 310] [--> http://10.10.150.75/img/]
/index.html           (Status: 200) [Size: 5954]                              
/phpinfo.php          (Status: 200) [Size: 94768]                             
/server-status        (Status: 403) [Size: 277]                               
                                                                              
===============================================================
2022/03/28 09:49:35 Finished
===============================================================
```

nothing to exciting here so I dedcided to run a 2.3-small.txt and another common.txt scan but for php,html, and txt

```console
gobuster dir -u http://10.10.150.75 -w /home/noob2uub/Documents/Wordlists/common.txt -x php,html,txt
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.150.75
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /home/noob2uub/Documents/Wordlists/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              html,txt,php
[+] Timeout:                 10s
===============================================================
2022/03/28 09:52:36 Starting gobuster in directory enumeration mode
===============================================================
/.hta.html            (Status: 403) [Size: 277]
/.hta.txt             (Status: 403) [Size: 277]
/.hta.php             (Status: 403) [Size: 277]
/.hta                 (Status: 403) [Size: 277]
/.htaccess.txt        (Status: 403) [Size: 277]
/.htpasswd            (Status: 403) [Size: 277]
/.htaccess            (Status: 403) [Size: 277]
/.htpasswd.php        (Status: 403) [Size: 277]
/.htaccess.php        (Status: 403) [Size: 277]
/.htpasswd.html       (Status: 403) [Size: 277]
/.htaccess.html       (Status: 403) [Size: 277]
/.htpasswd.txt        (Status: 403) [Size: 277]
/css                  (Status: 301) [Size: 310] [--> http://10.10.150.75/css/]
/img                  (Status: 301) [Size: 310] [--> http://10.10.150.75/img/]
/index.html           (Status: 200) [Size: 5954]                              
/index.html           (Status: 200) [Size: 5954]                              
/phpinfo.php          (Status: 200) [Size: 94768]                             
/phpinfo.php          (Status: 200) [Size: 94768]                             
/secret.txt           (Status: 200) [Size: 320]                               
/server-status        (Status: 403) [Size: 277]                               
                                                                              
===============================================================
2022/03/28 09:57:18 Finished
===============================================================
```
### We find secret.txt that should be something interesting. 

```console
Batman hits Joker.

Joker: "Bats you may be a rock but you won't break me." (Laughs!)

Batman: "I will break you with this rock. You made a mistake now."

Joker: "This is one of your 100 poor jokes, when will you get a sense of humor bats! You are dumb as a rock."

Joker: "HA! HA! HA! HA! HA! HA! HA! HA! HA! HA! HA! HA!"
```
Nothing to interestesting just an exchange between the two, lets run a .txt search with another word list. Maybe rockyou since he talks about hitting him with a rock?

Ok, that was a waste of time the flag was a bit more obvious then I thought it would be.  

### Browsing to port 8080

We see we have a weblogin available, so lets gather a bit more information on this page. 

![Screenshot_2022-03-28_10-12-29](https://user-images.githubusercontent.com/68706090/160451447-84d63393-782c-438c-bcb9-9bc8beb964b2.png)




