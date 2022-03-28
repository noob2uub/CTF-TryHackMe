Our first hint is this photo, lets start taking a look at it in more details

![Screenshot_2022-03-28_12-52-43](https://user-images.githubusercontent.com/68706090/160476067-126b5ae3-49fe-40dc-8e8d-3dd74dc710c7.png)

# Strings

```console
noob2uub@kali:~/Documents/tryhackme/Sakura$ strings Screenshot_2022-03-28_12-52-43.png 
IHDR
sBIT
IDATx
-|bcpIf
        DDDD
'"""
:"""
#"""
1"""
#"""
1"""
#"""
jxJc
```
I didn't post everything but I did not find anything interesting

# Exiftool

```console
noob2uub@kali:~/Documents/tryhackme/Sakura$ exiftool Screenshot_2022-03-28_12-52-43.png 
ExifTool Version Number         : 12.39
File Name                       : Screenshot_2022-03-28_12-52-43.png
Directory                       : .
File Size                       : 68 KiB
File Modification Date/Time     : 2022:03:28 12:52:50-07:00
File Access Date/Time           : 2022:03:28 12:53:03-07:00
File Inode Change Date/Time     : 2022:03:28 12:52:50-07:00
File Permissions                : -rw-r--r--
File Type                       : PNG
File Type Extension             : png
MIME Type                       : image/png
Image Width                     : 436
Image Height                    : 659
Bit Depth                       : 8
Color Type                      : RGB
Compression                     : Deflate/Inflate
Filter                          : Adaptive
Interlace                       : Noninterlaced
Significant Bits                : 8 8 8
Image Size                      : 436x659
Megapixels                      : 0.287
noob2uub@kali:~/Documents/tryhackme/Sakura$ 
```
Nothing with exif tool

Now to further inpect the file we see there is binary in the background. Lets take a look 

![Screenshot_2022-03-28_13-03-46](https://user-images.githubusercontent.com/68706090/160477851-88be5f7e-4090-4e51-9d7f-50f425ba46b5.png)

01000001 00100000 01110000  01101001 01100011 01110100  01110101 01110010 01100101  00100000 01101001 01110011  00100000 01110111 01101111  01110010 01110100 01101000  00100000 00110001 00110000  00110000 00110000 00100000  01110111 01101111 01110010  01100100 01110011 00100000  01100010 01110101 01110100  00100000 01101101 01100101  01110100 01100001 01100100  01100001 01110100 01100001  00100000 01101001 01110011  00100000 01110111 01101111  01110010 01110100 01101000  00100000 01100110 01100001 01110010 00100000 01101101 01101111 01110010 01100101 

"A picture is worth 1000 words but metadata is worth far more"

User Name found in the source code: SakuraSnowAngelAiko

now lets search https://whatsmyname.app/

We see that he is on reddit, github, and datezone, but datezone is tagged as XXX so I personally wont be going there for this box. 

![Screenshot_2022-03-28_13-22-46](https://user-images.githubusercontent.com/68706090/160480665-b01cd59f-5bc1-48dc-9e69-37ff34d49be4.png)

In his github we found a PGP page and a bunch of info about crypto. Lets take a look at the pgp to find out the missing flag. 

I created an .asc file from the PGP file and found his email

# GPG

```console
noob2uub@kali:~/Documents/tryhackme/Sakura$ gpg --import aiko.asc
gpg: can't open 'aiko.asc': No such file or directory
gpg: Total number processed: 0
noob2uub@kali:~/Documents/tryhackme/Sakura$ ls
Screenshot_2022-03-28_12-52-43.png  Screenshot_2022-03-28_13-03-46.png  Screenshot_2022-03-28_13-22-46.png
noob2uub@kali:~/Documents/tryhackme/Sakura$ gpg --import aiko.asc
gpg: key ECDD0FD294110450: public key "SakuraSnowAngel83@protonmail.com" imported
gpg: Total number processed: 1
gpg:               imported: 1
```
Then we also find his first name doing a good search

```console

Aiko Abe - Senior Software Engineer - Self-employed | LinkedInhttps://jp.linkedin.com › sakurasnowa...· Translate this page
日本 · Senior Software Engineer · Self-employed
Georgia Institute of Technology. Master of Science - MSComputer and Information Systems Security/Information Assurance. 2007年 – 2010年.
```
### Crypto Information

Looking through his github I found a mining script for Etherium 

![Screenshot_2022-03-28_15-37-37](https://user-images.githubusercontent.com/68706090/160498467-9bf0c13c-8888-46f1-af95-baaf0e77af61.png)

Then looking more into the history I can see his wallet address

![Screenshot_2022-03-28_15-39-01](https://user-images.githubusercontent.com/68706090/160498518-96159b42-c6ac-4c2e-8f33-ffeed4381fac.png)

Then checking his transaction history of his wallet I go to etherscan.io, and you can see that he also has transactions for Tether. 

![Screenshot_2022-03-28_15-40-06](https://user-images.githubusercontent.com/68706090/160498679-8cc80b9c-91f8-4085-a83f-e15a774766e3.png)

# Twitter Account 

We can see that his Twitter name is Aiko Abe

![Screenshot_2022-03-28_14-00-22](https://user-images.githubusercontent.com/68706090/160486325-86c5ac34-8c95-4715-8c47-186c2ddedbbe.png)

### Wifi Information 

Diving further into his tweets I come across this. 

![EsdhaUSVkAAM803](https://user-images.githubusercontent.com/68706090/160486449-b52291d6-1737-4427-b6ee-d5e2602e75cd.png)

0a5c6e136a98a60b8a21643ce8c15a74

To get the URL for where he save his SSID is located in TOR using DeepPaste, but the site was down, so I just used the hint, along with the hash that I got. This was a little annoying.

### Wigle Net

I decided to check his SSID against Wigle Net and found something

![Screenshot_2022-03-28_15-59-28](https://user-images.githubusercontent.com/68706090/160500695-4aba3a0e-138a-44b6-b242-bff5c79423b6.png)

That provided me with the BSSID/MAC information. 

# Home Bound

Looking back at the twitter acount we find a picture that was taken prior to him getting on the plane. 

![Esh-uTvUcAc-sXC](https://user-images.githubusercontent.com/68706090/160502139-5c81407b-2d62-4e15-8a2a-58f201c5db9e.jpeg)

This one took me a while and I was looking at that air plane and thinking he was some where in Japan, so I was going through images of Japan. Then I saw the Washington Monument in the background and face palmed myself. 

![Screenshot_2022-03-28_16-10-43](https://user-images.githubusercontent.com/68706090/160502254-46f86de5-b2b6-4671-be61-c3047bfc65f0.png)

This one was much easier, looking at this phone and searching Sakura Lounge Air Port, I find out its in the Tokyo International Air Port.

![EsiM12KVoAEhAsI](https://user-images.githubusercontent.com/68706090/160502779-c2e6257e-9563-4e57-86e2-695f45c3d40f.png)

This next one was easy, I just overlayed the image next to good maps and found the lake. 

![EsiNRuRU0AEH32u](https://user-images.githubusercontent.com/68706090/160503173-acaf0353-ebfa-4245-bd3f-cf3f1ff952cd.jpeg)

Then lastly to find his location, I just went back to wigle net and pulled up the map of where his BSSID was located. 

![image](https://user-images.githubusercontent.com/68706090/160503304-eb443ad1-c0be-4560-8f90-a44acaa042f8.png)








