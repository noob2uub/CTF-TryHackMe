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
