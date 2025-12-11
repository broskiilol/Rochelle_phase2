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
- I need to use `FTK imager` or `autopsy` to properly open the .ad1 file, so i download  FTKIMAGER first. In this i open ninetails.ad1 and i get a lot of folders. I search through them and find `firefox` folder (as asked for in the question)
![nintails2](https://github.com/user-attachments/assets/7767e4e0-d29b-4f6e-b4e8-70bac60779fc)

- In the profiles tab i get the logins.json and key.db files as asked for in the question.![nintails3](https://github.com/user-attachments/assets/3dd436cf-37b8-4c3f-903b-54b0222ac6a9)
 I extract them to my computer to a folder called `ninetails_hmm`. Now i need to decrypt them
- I install something called `firefox_decrypt` on my terminal and i run the extracted files at once through this decrypter (Major mistake i was doing was passing it one by one and failing)
```bash
$ python3 firefox_decrypt.py "/mnt/c/Users/Rochelle/Downloads/ninetails_hmm"
2025-12-08 17:49:53,108 - WARNING - profile.ini not found in /mnt/c/Users/Rochelle/Downloads/ninetails_hmm
2025-12-08 17:49:53,108 - WARNING - Continuing and assuming '/mnt/c/Users/Rochelle/Downloads/ninetails_hmm' is a profile location

Website:   https://www.rehack.xyz
Username: 'warlocksmurf'
Password: 'GCTF{m0zarella'

Website:   https://ctftime.org
Username: 'ilovecheese'
Password: 'CHEEEEEEEEEEEEEEEEEEEEEEEEEESE'

Website:   https://www.reddit.com
Username: 'bluelobster'
Password: '_f1ref0x_'

Website:   https://www.facebook.com
Username: 'flag'
Password: 'SIKE'

Website:   https://warlocksmurf.github.io
Username: 'Man I Love Forensics'
Password: 'p4ssw0rd}'
```
- These are different websites they have given with a username and password. So now i try to enter each of them to get the flag

**Flag:** `GCTF{m0zarella_f1ref0x_p4ssw0rd}`
### What I learned
- .rar file is like a folder. It has various other files in it.
- .ad1 file is a local disk image format file.
- .stl file means stereolithography. This is the file format for CAD and 3D printing (not needed in this challenge)
- You can login to a firefox account if u have certain information about it such as logins.json, key4.db, and cert9.db
    - logins.json gives the login
    - key4.db gives the decryption data
    - cert9.db gives certification to decode it
- Using `firefox_decrypt` we can get the login of firefox profile.
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

- We are given a .7z file, so i opened that first, which gave me a .raw file called `MemoryDump_Lab1.raw`. i converted the .raw file to .jpg and got (memorydump.jpg)
- This memory dump doesnt make sense, so i rethink. Hint said to look at volitility 2, so i start downloading it on my system. Then i get the info on the .raw file.

```bash
$ python2 ~/volatility/vol.py -f /mnt/c/Users/Rochelle/Downloads/MemoryDump_Lab1.raw imageinfo
Volatility Foundation Volatility Framework 2.6.1
INFO    : volatility.debug    : Determining profile based on KDBG search...
          Suggested Profile(s) : Win7SP1x64, Win7SP0x64, Win2008R2SP0x64, Win2008R2SP1x64_24000, Win2008R2SP1x64_23418, Win2008R2SP1x64, Win7SP1x64_24000, Win7SP1x64_23418
                     AS Layer1 : WindowsAMD64PagedMemory (Kernel AS)
                     AS Layer2 : FileAddressSpace (/mnt/c/Users/Rochelle/Downloads/MemoryDump_Lab2.raw)
                      PAE type : No PAE
                           DTB : 0x187000L
                          KDBG : 0xf800028100a0L
          Number of Processors : 1
     Image Type (Service Pack) : 1
                KPCR for CPU 0 : 0xfffff80002811d00L
             KUSER_SHARED_DATA : 0xfffff78000000000L
           Image date and time : 2019-12-11 14:38:00 UTC+0000
     Image local date and time : 2019-12-11 20:08:00 +0530
```
- The questions hints that there are 3 parts of the flag. for the first path, there is a hint that flag is in the output of a black command window so i go to the terminal. i see what processes are running using `vol.py -f /mnt/c/Users/Rochelle/Downloads/MemoryDump_Lab2.raw --profile=Win7SP1x64 pslist` In the list i see cmd.exe and conhost.exe too. So now i extract the command history
```bash
$ vol.py -f /mnt/c/Users/Rochelle/Downloads/MemoryDump_Lab2.raw --profile=Win7SP1x64 cmdscan
Volatility Foundation Volatility Framework 2.6.1
**************************************************
CommandProcess: conhost.exe Pid: 2692
CommandHistory: 0x1fe9c0 Application: cmd.exe Flags: Allocated, Reset
CommandCount: 1 LastAdded: 0 LastDisplayed: 0
FirstCommand: 0 CommandCountMax: 50
ProcessHandle: 0x60
Cmd #0 @ 0x1de3c0: St4G3$1
Cmd #15 @ 0x1c0158: 
Cmd #16 @ 0x1fdb30: 
**************************************************
CommandProcess: conhost.exe Pid: 2260
CommandHistory: 0x38ea90 Application: DumpIt.exe Flags: Allocated
CommandCount: 0 LastAdded: -1 LastDisplayed: -1
FirstCommand: 0 CommandCountMax: 50
ProcessHandle: 0x60
Cmd #15 @ 0x350158: 8
Cmd #16 @ 0x38dc00: 8
```
In this I can see that in command process 2692 there is `St4G3$1` at CMD #0. Now i look at the console for the stage 1 flag.

```bash
vol.py -f /mnt/c/Users/Rochelle/Downloads/MemoryDump_Lab2.raw --profile=Win7SP1x64 consoles
Volatility Foundation Volatility Framework 2.6.1
**************************************************
ConsoleProcess: conhost.exe Pid: 2692
Console: 0xff756200 CommandHistorySize: 50
HistoryBufferCount: 1 HistoryBufferMax: 4
OriginalTitle: %SystemRoot%\system32\cmd.exe
Title: C:\Windows\system32\cmd.exe - St4G3$1
AttachedProcess: cmd.exe Pid: 1984 Handle: 0x60
----
CommandHistory: 0x1fe9c0 Application: cmd.exe Flags: Allocated, Reset
CommandCount: 1 LastAdded: 0 LastDisplayed: 0
FirstCommand: 0 CommandCountMax: 50
ProcessHandle: 0x60
Cmd #0 at 0x1de3c0: St4G3$1
----
Screen 0x1e0f70 X:80 Y:300
Dump:
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\Users\SmartNet>St4G3$1
ZmxhZ3t0aDFzXzFzX3RoM18xc3Rfc3Q0ZzMhIX0=
Press any key to continue . . .
**************************************************
ConsoleProcess: conhost.exe Pid: 2260
Console: 0xff756200 CommandHistorySize: 50
HistoryBufferCount: 1 HistoryBufferMax: 4
OriginalTitle: C:\Users\SmartNet\Downloads\DumpIt\DumpIt.exe
Title: C:\Users\SmartNet\Downloads\DumpIt\DumpIt.exe
AttachedProcess: DumpIt.exe Pid: 796 Handle: 0x60
----
CommandHistory: 0x38ea90 Application: DumpIt.exe Flags: Allocated
CommandCount: 0 LastAdded: -1 LastDisplayed: -1
FirstCommand: 0 CommandCountMax: 50
ProcessHandle: 0x60
----
Screen 0x371050 X:80 Y:300
Dump:
  DumpIt - v1.3.2.20110401 - One click memory memory dumper
  Copyright (c) 2007 - 2011, Matthieu Suiche <http://www.msuiche.net>
  Copyright (c) 2010 - 2011, MoonSols <http://www.moonsols.com>


    Address space size:        1073676288 bytes (   1023 Mb)
    Free space size:          24185389056 bytes (  23064 Mb)

    * Destination = \??\C:\Users\SmartNet\Downloads\DumpIt\SMARTNET-PC-20191211-
143755.raw

    --> Are you sure you want to continue? [y/n] y
    + Processing...
```
In this i can see `St4G3$1 ZmxhZ3t0aDFzXzFzX3RoM18xc3Rfc3Q0ZzMhIX0=`. It seems like this part is encrypted to i decrypt it using base64. This gives `flag{th1s_1s_th3_1st_st4g3!!}`

- Now we move to the second part of the stage. This is likely to be related to paintings (hint from the question). So in the pslist i got before, i search for something similar and i get `mspaint.exe` process.
`0xfffffa80022bab30 mspaint.exe            2424    604      6      128      1      0 2019-12-11 14:35:14 UTC+0000`
The PID for mspaint.exe is `2424`. Now i create a memory dump for thid mspaint.exe process 
Then i search for PNG and BMP files using their file headers.
`grep -obaP "BM" dump/2424.dmp` I dont get any match for PNG, but i get for BMP. I want just the header so `grep -obaP "BM" dump/2424.dmp | head` and i get the offsets.
I search the offsets' HEX to search for the proper .bmp file. `xxd -s <offset> -l 64 dump/2424.dmp`. Most offsets do not give anything, but the offset `8383447` seems like a probable .bmp file. So i carve it and save it as stage2.bmp...but it is not opening.
I try changing the header in case that is the problem, but that is not helping either
I backtrack cause stuff isnt working out. I decide to use binwalk. `binwalk -e dumped/2424.dmp` This gives me 2 images. 


- For stage 3: I first dump to find archives. I search for .7z, .zip or .rar files. This gives me 
```
0x000000003fa3ebc0      1      0 R--r-- \Device\HarddiskVolume2\Users\Alissa Simpson\Documents\Important.rar
0x000000003fac3bc0      1      0 R--r-- \Device\HarddiskVolume2\Users\Alissa Simpson\Documents\Important.rar
0x000000003fb48bc0      1      0 R--r-- \Device\HarddiskVolume2\Users\Alissa Simpson\Documents\Important.rar
```
I want to extract (to extract folder) so i do `vol.py -f MemoryDump_Lab1.raw --profile=Win7SP1x64 dumpfiles -Q 0x000000003fa3ebc0 -D extracted/` to extract that rar and find a `flag3.png` file. I try to open, but it is password protected. 
<img width="493" height="61" alt="image" src="https://github.com/user-attachments/assets/3165f738-78d8-47a8-b190-af156ac9989a" />
So now i need to find the password. I research on what NTLM is (it is NT LAN Manager which is used for security purposes for authentication and stuff) 
I use `hivelist` to see all the registers and their addresses 
<img width="1430" height="500" alt="image" src="https://github.com/user-attachments/assets/0cf405a0-d550-4d9e-874e-5dd64cafb9b5" />
I need the hash of these so i do `vol.py -f MemoryDump_Lab1.raw --profile=Win7SP1x64 hashdump -y 0xfffff8a000024010 -s 0xfffff8a0014e9010` and get some wierd letters and numbers
```bash
$ vol.py -f MemoryDump_Lab1.raw --profile=Win7SP1x64 hashdump -y 0xfffff8a000024010 -s 0xfffff8a0014e9010
Volatility Foundation Volatility Framework 2.6.1
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
SmartNet:1001:aad3b435b51404eeaad3b435b51404ee:4943abb39473a6f32c11301f4987e7e0:::
HomeGroupUser$:1002:aad3b435b51404eeaad3b435b51404ee:f0fc3d257814e08fea06e63c5762ebd5:::
Alissa Simpson:1003:aad3b435b51404eeaad3b435b51404ee:f4ff64c8baac57d22f22edc681055ba6:::
```
This hash gives me the rar password which is `f4ff64c8baac57d22f22edc681055ba6` which i can thus use to open the .png file given before. 
<img width="754" height="678" alt="image" src="https://github.com/user-attachments/assets/9692ebac-0b2c-4332-9947-b69c621f9cc9" />
Thus i get the third stage flag 




**Flag:** `flag{th1s_1s_th3_1st_st4g3!!}`, `flag{w3ll_3rd_stage_was_easy}`

### What I learnt:
- Volitility 2 is used to analyse the rar dumps, it extracts data and stuff to find out what is wrong with it.
- pslist plugin: lists running processes and shows if some process is hidden
- hivelist plugin: It analyses hives (registerys) to see its mechanisms and workings
  

