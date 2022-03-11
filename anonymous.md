<div align="center">
    <h1>Anonymous</h1>

## Table of Contents

*Possibly include a table of contents here, though this is only necessary for larger writeups ...*

- [Table of Contents](#table-of-contents)
- [recon](#recon)
- [exploiting](#exploiting)
- [etc.](#etc)

## NMAP

nmap -sC -sV -A -T4 10.10.181.3
Starting Nmap 7.80 ( https://nmap.org ) at 2022-03-11 14:36 PST
Nmap scan report for 10.10.181.3
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

## exploiting

*Descriptive subheadings make for a more structured writeup as well.*

## etc.

*Etc. this is just a template, you'll know what to do... ^^ Change it, if you feel like it, use it like this if you don't ...*

---

... m4ttm00ny (March 2021)
