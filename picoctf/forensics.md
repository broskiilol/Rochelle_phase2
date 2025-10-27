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
- 


## Notes:

- Include any alternate tangents you went on while solving the challenge, including mistakes & other solutions you found.
- 

## Resources:

- Include the resources you've referred to with links. [example hyperlink](https://google.com)


***

# 2. Trivial flag transfer protocol

> Figure out how they moved the flag
## Solution:

- frame 1 has instructions.txt, 3790 has picture2.bmp, 567 has picture1, request in frame 22, source file is program.deb
- bmp== bitmap image file, 19 has plan, 146683 pic3

- <img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/2cb2eebf-208f-45dd-a8c1-8be7905962f3" />
 I exported the files in it, and i got 3 pictures of sheep, trees and rivers, and 1 .deb program and 2 .txt files with random numbers

## Flag:

```
picoCTF{}
```

## Concepts learnt:

- .pcapng is a newer version of pcap files. pcap basically stores the data of the network traffic of an interface.
- 

## Notes:

- needed to download wireshark and learn how to use it...that was a struggle

## Resources:

- 


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
