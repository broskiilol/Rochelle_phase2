# Digital forensics
## Challenge 1: Nutrela chuncks
> One of my favorite foods is soya chunks. But as I was enjoying some Nutrela today, I noticed a few chunks weren’t quite right. Seems like something’s off with their structure. Could you help me fix these broken chunks so I can enjoy my meal again?

### Solution

- A PNG file is given, but when i download it, it is not opening. Maybe something is not right with the file, so let us open it in hexed. ![Nutrela_1](https://github.com/user-attachments/assets/5a148b31-fddc-4885-8b40-ae7e4706023e)

- There is something wierd about the header, a normal PNG header is supposed to be 89 50 4E 47 0D 0A 1A 0A. so let us change it. ...this did not help. So there must be something more.
- The challenge title is asking us to look at the chunks, so lets us do that 
   A chunk has 4 parts: 1- length of chunk (4 bytes)- how many bytes chunk data has
                        2- Chunk type (4 bytes)- types are IHDR, IDAT, IEND, PLTE
                        3- chunk data- self explanatory
                        4- CRC (4 bytes)
    IHDR: contains image info like height, width , colour, etc.
    IDAT: contains data which will be appeared on the screen
    IEND: Marks end of image data and end of file (last chunk)
- I edited these chunks (they were written in lowercase, i wrote them in uppercase) and got the PNG file to open ![Nutrela_2](https://github.com/user-attachments/assets/099215a2-0f19-4418-af17-76f127acfe8a)

![Nutrela_3](https://github.com/user-attachments/assets/a29c5b56-4c53-4423-a290-60219aa93f4b)


**Flag:** `nite{n0w_y0u_kn0w_ab0ut_PNG_chunk5}`

### What I learned
- PNG is short for portable network graphics (unrelated but who knew that!?) 
- chunks are basically blocks of data used to store information about the PNG file
- CRC- cyclic redundancy code- ensures data integrity


### References
- https://medium.com/@0xwan/png-structure-for-beginner-8363ce2a9f73 


## Challenge 2: RAR of the abyss
> Two philosophers peer into the networked abyss and swap a secret. Use the secret to decrypt the Abyss’ RAwR and pull your flag from the void
### Solution

- We are given a .pcap file. I opened it in wireshark. I look at the TCP packets to see the conversation going on.
- I can see that Neitzche and camus are having a conversation and neitztche has given the password as `b3y0ndG00dand3vil`. One of the TCP packets also has the hex of a .rar file.![Rar1](https://github.com/user-attachments/assets/935ecc12-f03a-4ad7-b5d5-a1fa309028d7)
 SO i save that packet as abyss.rar and try to extract is giving the password. 
- This gives me error ![Rar2](https://github.com/user-attachments/assets/52d14149-fc02-4194-bdba-f74f3197f5bf)
. I realised that i was pasting the hex code of the tcp packet wrongly. instead of pasting it as hex, i was pasting its ASCII values. On changing it, i was able to extract the flag.txt file in it.
- ![rar3](https://github.com/user-attachments/assets/21db94a3-f3d9-40e1-b808-f8d4d638bd23)
 On opening the .txt file, i got the flag.

```bash
rochelle@DESKTOP-IF15GUC:/mnt/c/Users/Rochelle/Downloads$ unrar x abyss.rar

UNRAR 6.11 beta 1 freeware      Copyright (c) 1993-2022 Alexander Roshal

Enter password (will not be echoed) for abyss.rar:


Extracting from abyss.rar

Extracting  flag.txt                                                  OK
All OK
rochelle@DESKTOP-IF15GUC:/mnt/c/Users/Rochelle/Downloads$ cat flag.txt
nite{thus_sp0k3_th3_n3tw0rk_f0r3ns1cs_4n4lyst}
```

**Flag:** `nite{thus_sp0k3_th3_n3tw0rk_f0r3ns1cs_4n4lyst}`

### What I learned
- I learnt to extract .rar files and understand how to read the tcp packets in order to get clues.

### References 
no references

## Challenge 3: NineTails

> Looks like I got a little too clever and hid the flag as a password in Firefox, tucked away like one of NineTails’ many tails. Recover the "logins" and the "key4" and let it guide you to the flag
Hint:
I named my Ninetails "j4gjesg4", quite a peculiar name isn't it?
### Solution

- They gave a .rar file. So i extracted it in ubuntu using `unrar`. This gave me a .ad1 file. 
- On extracting the file from this, i get an authroot.stl file. This is a garbage file which i got from extracting the file in the wrong manner. I was trying to extract it from WinRAR but this is a wrong approach.
- I need to use `FTK imager` or `autopsy` to properly open the .ad1 file, so i download it first.

### What I learned
- .rar file is like a folder. It has various other files in it.
- .ad1 file is a local disk image format file.
- .stl file means stereolithography. This is the file format for CAD and 3D printing
## Challenge 4: Hide and seek
 > Sakamoto’s at it again with a game of hide and seek, but this time, it’s not with Shin or his daughter. An old friend hid some secret data in this image. Can you find it before the others do?

Hint:
Even in retirement, Sakamoto never loses at hide and seek. Maybe stegseek can help you keep up.
### Solution

- a .jpg file is given with a picture ![sakamoto](https://github.com/user-attachments/assets/b4cf38f3-2944-4c66-850c-72b12ebb3d1f)

- I try to run steghide with it, but i need a passphrase (which i do not have, so i need to get that first). I run exiftool to check for any information that might help, but it all seems normal.
- The hint tells me to use stegseek, so i try that. I download stegseek, then i download a rockyou wordlist (cause no passphrase is already given) and thus i use it and get:

```bash
$ stegseek sakamoto.jpg rockyou.txt
StegSeek 0.6 - https://github.com/RickdeJager/StegSeek

[i] Found passphrase: "iloveyou1"
[i] Original filename: "flag.txt".
[i] Extracting to "sakamoto.jpg.out".
```
- This means that the passphrase needed is `iloveyou1`, and the flag `flag.txt` is now stored in `sakamoto.jpg.out` so i read it
```bash
$ cat sakamoto.jpg.out
nite{h1d3_4nd_s33k_but_w1th_st3g_sdfu9s8}
```

**Flag:** `nite{h1d3_4nd_s33k_but_w1th_st3g_sdfu9s8}`


### What I learned
- I largely learnt to use stegseek and understood the importance of `rockyou.txt` file.

 ## Challenge 5: Re:Draw 
 > Her screen went black and a strange command window flickered to life, lines of text flashed across before everything went silent. Moments later, the system crashed. By sheer luck, we recovered a memory dump. 

Note: There are three stages to this challenge and you will find three flags.

What we know: just before the crash, a black command window flickered across the screen, something in its output might still be visible if you dig through memory. She was drawing when it happened, and remnants of a painting program linger, which could reveal more if inspected in the right way. Finally, a mysterious archive hides deeper in memory, likely holding the last piece of her work.

Hint:
Learn up on volatility 2 and its various plugins and what they are used for

### Solution:

- We are given a .7z file, so i opened that first, which gave me a .raw file. I was not able to open that because i didnt have that extension supported on my laptop. i converted the .raw file to .jpg and got (memorydump.jpg)
- This memory dump doesnt make sense, so i rethink. Hint said to looks at volitility 2, so i start downloaded it on my system.

