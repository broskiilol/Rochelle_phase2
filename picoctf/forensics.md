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

- Include the resources you've referred to with links. [example hyperlink](https://google.com)


***
