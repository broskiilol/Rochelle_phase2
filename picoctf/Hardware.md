# 1. IQ test

> let your input x = 30478191278.

## Solution:

- converted it to binary which becomes `011100011000101001000100101010101110`
- Put these numbers as x0,x1,x2...x35, and got the y0,y1,y2...y10
  <img width="900" height="1600" alt="image" src="https://github.com/user-attachments/assets/ec449355-13ee-427f-8c32-35f227d96158" />

- i got binary as `100010011000` which is the flag

## Flag:

```
nite{100010011000}
```

***

# 2. I like logic

> i like logic and i like files, apparently, they have something in common, what should my next step be

## Solution:

- They have given a .sal file. On researching what that means, it told me to download logic2 software, so ig i'll do that.
- I opened the logic2 software and opened the .sal file into it.
   ![i like logic](https://github.com/user-attachments/assets/898f479c-5226-47d9-9737-b1af77670566)

- Only channel 3 has a square file.
- I changed the setteings to Async serial and selected channel 3 and got some data in hex code
  ![i like logic2](https://github.com/user-attachments/assets/b06fe112-4b63-4d67-94e0-252dbe7499a0)

- I changed the format from hex to ascii and got a readable data characters which sort of makes sense. But this is EXTREMELY LONG (like 100 chacters plus)
- So i try to search for the flag using a `{` (cause i am hoping the flag should be having that...right?)
  ![ilikelogic3](https://github.com/user-attachments/assets/eca85a49-a775-4418-a1e5-cb0d147c8025)

- I get something like `{b1dee4eeadf6c4e60aeb142b0b486344e64b12b40d1046de95c89ba5e23a9925}`
## Flag:

```
FCSC{b1dee4eeadf6c4e60aeb142b0b486344e64b12b40d1046de95c89ba5e23a9925}
```
## Concepts learnt:
- .sal files store the results of a hardware signal analysis
- How to operate the Logic 2 software 

## Resources:
- https://www.youtube.com/watch?v=Ak9R4yxQPhs 

# 3. Bare Metal Alchemist

> my friend recommended me this anime but i think i've heard a wrong name.

## Solution:

- First i installed avr 
`sudo apt install binutils-avr avr-libc`
- 
## Flag:

```
TFCCTF{Th1s_1s_som3_s1mpl3_4rdu1no_f1rmw4re} 
```

***
