# 1. m00nwalk

> Decode this message from the moon.

## Solution:

- The message is given in the form of a .wav audio file, which on listening just gives nonsensical beeps and noises
- The first hint tells us to see "How did pictures from the moon landing get sent back to Earth?" which refers to SSTV
- We would normally need a decoder software like QSSTV but i did it online via https://sstv-decoder.mathieurenaud.fr/ 
- Thus i got the image 
https://sstv-decoder.mathieurenaud.fr/ 


## Flag:

```
picoCTF{beep_boop_im_in_space}
```

## Concepts learnt:

- *SSTV (Slow Scan Television) is a method os sending images which are encoded in audio. So high frequency audios give bright images and low frequency audios give low brightness images*


***

# 2. Trivial flag transfer protocol

> Figure out how they moved the flag
## Solution:

- frame 1 has instructions.txt, 3790 has picture2.bmp, 567 has picture1, request in frame 22, source file is program.deb
- bmp== bitmap image file, 19 has plan, 146683 pic3

- <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/2cb2eebf-208f-45dd-a8c1-8be7905962f3" />
 I exported the files in it, and i got 3 pictures of sheep, trees and rivers, and 1 .deb program and 2 .txt files with random numbers (nothing makes sense)
- I start with the instructions file. "When in doubt, use rot13 code, then use base64 code, then whatever you can find" so i tried rot13 on the file.
```c
#include<stdio.h>
int main()
{
    char str[120]="GSGCQBRFAGRAPELCGBHEGENSSVPFBJRZHFGQVFTHVFRBHESYNTGENAFSRE.SVTHERBHGNJNLGBUVQRGURSYNTNAQVJVYYPURPXONPXSBEGURCYNA";
    char rot[120];
    for(int i=0; str[i]!='\0'; i++)
    {
            if(str[i]<='M'&&str[i]>='A')
            {
                rot[i]=str[i]+ 13;
            }
            else
            rot[i]=str[i]-13;
    }
    puts(rot);
}
```
This gave me: `TFTPDOESNTENCRYPTOURTRAFFICSOWEMUSTDISGUISEOURFLAGTRANSFER!FIGUREOUTAWAYTOHIDETHEFLAGANDIWILLCHECKBACKFORTHEPLAN` which translates to `TFTP DOESNT ENCRYPT OUR TRAFFIC SO WE MUST DISGUISE OUR FLAG TRANSFER! FIGURE OUT A WAY TO HIDE THE FLAG AND I WILL CHECK BACK FOR THE PLAN`
- So let us look at the plan (again gibberish, we put a rot 13 to this too). We get `IUSEDTHEPROGRAMANDHIDITWITH DUEDILIGENCE!CHECKOUTTHEPHOTOS` (putting spaces in between) it gets as `I USED THE PROGRAM AND HID IT WITH DUE DILIGENCE! CHECK OUT THE PHOTOS` So we need to see the program and do something with the photos

-  while running the program, on looking closely, the bash gives a note `Note, selecting 'steghide' instead of './program.deb'`. I didnt understand why it is selecting steghide instead of my intended program (and i thought it was a glitch with my system so i went all around the world trying to fix it) before realising that `steghide` is our inteded place to be.
-  I went on a whole journey of learning how to use steghide. I understood that the flag is hidden inside the .bmp pictures we have gotten. so i need to extract the flag from the pictures.
-  I used `steghide extract -sf picture1.bmp` but now it is asking for a password (which i dont know). After a lot of trial and error, i got the password as "DUEDILIGENCE" from the plan file content. But i was still not getting anything so i thought the password was wrong and wasted a lot of time :)
-  Finally i thought of checking with the other 2 pictures and i got it in the 3rd picture
-  ```bash
   steghide extract -sf picture3.bmp
   Enter passphrase:
   wrote extracted data to "flag.txt".
   cat flag.txt
   picoCTF{h1dd3n_1n_pLa1n_51GHT_18375919}
   ```
  

## Flag:

```
picoCTF{h1dd3n_1n_pLa1n_51GHT_18375919}
```

## Concepts learnt:

- .pcapng is a newer version of pcap files. pcap basically stores the data of the network traffic of an interface.
- rot13 algorithm is basically like caeser cipher, but the shifting is done with a letter 13 positions from it
- steghide is a tool which is used to hide stuff in other files (sort of like steganogrphy)

## Notes:

- needed to download wireshark and learn how to use it...that was a struggle

***
#  3. tunn3l_v1s10n
I made a cool website where you can announce whatever you want! Try it out!

## Solution:

- on checking the file in terminal, it gave the file type as `data`, which didnt answer anything
- I opened the given file in hex editor, the header had "BM" meaning it is a .bmp file
- Saved it as a .bmp file (still didnt open)
- on looking closer at the values in the hex editor, usually the infoheader size needs to be 40 (which means the first 4 bytes in the info header needs to be 28 00 00 00). Here the `ba d0 00 00` does not make sense
 <img width="1021" height="346" alt="image" src="https://github.com/user-attachments/assets/0b1fc289-d0e6-4709-99bf-0b51b5b94c50" />
- So i changed the bytes....annnndddd didnt get the flag :) <img width="1139" height="318" alt="image" src="https://github.com/user-attachments/assets/fd66c9c2-72c7-4f21-8b38-f2ab5d7c4f12" />
So i again take a look into the bytes in the hex editor to see if i can make more changes.
- The data offset shown is `ba d0 00 00` meaning there is 53434 bytes of data in the image (which is not the amount of image we got). This means that the image we have gotten so far is just a part of the actual image.
- Currently the image is 1134x306. We look at the height in the infoheader and try to change it <img width="1008" height="357" alt="image" src="https://github.com/user-attachments/assets/adb15c90-6b27-4f0e-abef-85dd45f71e8c" /> The height given is `32 01`, we increase it to `32 02`AND THE IMAGE SHOWS MORE HEIGHT
- i increase the height again to `32 03`....AND GET THE FULL IMAGE
  <img width="1136" height="830" alt="image" src="https://github.com/user-attachments/assets/c55af0b1-ecc9-452d-9cf8-fda8339ef7bc" />

## Flag:

```
picoCTF{qu1t3_a_v13w_2020}
```

## Concepts learnt:

- Exiftool is a software program which allows us to see the file data and information.
- .bmp file extenstion is for a bitmap image file. This means it is a large image file 
- In a hex editor, we can see various parts of the bitmap file. This includes the header (information about the file structure), the infoheader, and the pixel data (the actual image). The header is offset to 14bytes and infoheader to 40 bytes.

## Notes:

- I learnt to ACTUALLY read the hex editor numbers and get information out of it
- 

## Resources:

- https://www.ece.ualberta.ca/~elliott/ee552/studentAppNotes/2003_w/misc/bmp_file_format/bmp_file_format.htm 


***
