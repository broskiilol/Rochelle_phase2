# 1. GDB baby step 1

> Can you figure out what is in the eax register at the end of the main function? Put your answer in the picoCTF flag format: picoCTF{n} where n is the contents of the eax register in the decimal number base. If the answer was 0x11 your flag would be picoCTF{17}.

## Solution:

- opened the elf file in command prompt
```bash
rochelle@DESKTOP-IF15GUC:/mnt/c/Users/Rochelle/Downloads$ readelf -h debugger0_a
ELF Header:
  Magic:   7f 45 4c 46 02 01 01 00 00 00 00 00 00 00 00 00
  Class:                             ELF64
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0
  Type:                              DYN (Position-Independent Executable file)
  Machine:                           Advanced Micro Devices X86-64
  Version:                           0x1
  Entry point address:               0x1040
  Start of program headers:          64 (bytes into file)
  Start of section headers:          14616 (bytes into file)
  Flags:                             0x0
  Size of this header:               64 (bytes)
  Size of program headers:           56 (bytes)
  Number of program headers:         13
  Size of section headers:           64 (bytes)
  Number of section headers:         29
  Section header string table index: 28
```
- Ran the file in gdb
```bash
rochelle@DESKTOP-IF15GUC:/mnt/c/Users/Rochelle/Downloads$ gdb debugger0_a
GNU gdb (Ubuntu 12.1-0ubuntu1~22.04.2) 12.1
Copyright (C) 2022 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
Type "show copying" and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<https://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
    <http://www.gnu.org/software/gdb/documentation/>.

For help, type "help".
Type "apropos word" to search for commands related to "word"...
Reading symbols from debugger0_a...
(No debugging symbols found in debugger0_a)
```
- I opened all the functions of the program (cause we need to access the main function) using `info functions`
```bash
(gdb) info functions
All defined functions:

Non-debugging symbols:
0x0000000000001000  _init
0x0000000000001030  __cxa_finalize@plt
0x0000000000001040  _start
0x0000000000001070  deregister_tm_clones
0x00000000000010a0  register_tm_clones
0x00000000000010e0  __do_global_dtors_aux
0x0000000000001120  frame_dummy
0x0000000000001129  main
0x0000000000001140  __libc_csu_init
0x00000000000011b0  __libc_csu_fini
0x00000000000011b8  _fini
```

- We can see the main function in this. We disassemble it (or take a deeper look into what the function is doing) using `disassemble main`
```bash
(gdb) disassemble main
Dump of assembler code for function main:
   0x0000000000001129 <+0>:     endbr64
   0x000000000000112d <+4>:     push   %rbp
   0x000000000000112e <+5>:     mov    %rsp,%rbp
   0x0000000000001131 <+8>:     mov    %edi,-0x4(%rbp)
   0x0000000000001134 <+11>:    mov    %rsi,-0x10(%rbp)
   0x0000000000001138 <+15>:    mov    $0x86342,%eax
   0x000000000000113d <+20>:    pop    %rbp
   0x000000000000113e <+21>:    ret
End of assembler dump.
```
Here we get the `eax` register number having constant `0x86342` 

- We convert this number to decimal, getting `549698`
## Flag:

```
picoCTF{549698}
```

## Concepts learnt:

- GDB debugger is used to debug programs on command prompt
- eax register stores the return values of a function
- We can open elf files on command prompt using `readelf`
- learnt various gdb functions like `info functions` (to list functions), `disassmble <function name>` (to look deeper at what a function does)
- an assembler code depends on which system is being used. (In this case it was a 64 bit machine). The assembler dump means displaying the program in assembly level language way. This helps us in debugging and finding the error.

## Notes:
- At first i thought we needed its error status (which came out to 66 and i was confused for a while) but we needed the register (which is different from the error number)

***

# 2. vault door 3

> This vault uses for-loops and byte arrays. The source code for this vault is here: VaultDoor3.java

## Solution:

- I opened the java program and tried to understand the algorithm
```java
 import java.util.*;

class VaultDoor3 {
    public static void main(String args[]) {
        VaultDoor3 vaultDoor = new VaultDoor3();
        Scanner scanner = new Scanner(System.in);
        System.out.print("Enter vault password: ");
        String userInput = scanner.next();
	String input = userInput.substring("picoCTF{".length(),userInput.length()-1);
	if (vaultDoor.checkPassword(input)) {
	    System.out.println("Access granted.");
	} else {
	    System.out.println("Access denied!");
        }
    }

    // Our security monitoring team has noticed some intrusions on some of the
    // less secure doors. Dr. Evil has asked me specifically to build a stronger
    // vault door to protect his Doomsday plans. I just *know* this door will
    // keep all of those nosy agents out of our business. Mwa ha!
    //
    // -Minion #2671
    public boolean checkPassword(String password) {
        if (password.length() != 32) {
            return false;
        }
        char[] buffer = new char[32];
        int i;
        for (i=0; i<8; i++) {
            buffer[i] = password.charAt(i);
        }
        for (; i<16; i++) {
            buffer[i] = password.charAt(23-i);
        }
        for (; i<32; i+=2) {
            buffer[i] = password.charAt(46-i);
        }
        for (i=31; i>=17; i-=2) {
            buffer[i] = password.charAt(i);
        }
        String s = new String(buffer);
        return s.equals("jU5t_a_sna_3lpm18gb41_u_4_mfr340");
    }
}
```
- Basically the algorithm is as follows
  - we need to input a string (which is our actual flag) 
  - our string will go through the various for loops and get edited accordingly
  - The end string (after going through all loops) will be compared with a prewritten string in the program. Only if its true, will we get the flag.
- With this logic understood, we just need to go the opposite direction, editing the string as we go.

## Flag:

```
picoCTF{jU5t_a_s1mpl3_an4gr4m_4_u_1fb380}
```

## Concepts learnt:

- I did this manually so it sharpened my logical skills


***

# 3. ARMssembly

> For what argument does this program print `win` with variables 68, 2 and 3?

## Solution:

- on opening the given file we get a whole bunch of data which makes not sense to me 
```bash
         .arch armv8-a
        .file   "chall_1.c"
        .text
        .align  2
        .global func
        .type   func, %function
func:
        sub     sp, sp, #32
        str     w0, [sp, 12]
        mov     w0, 68
        str     w0, [sp, 16]
        mov     w0, 2
        str     w0, [sp, 20]
        mov     w0, 3
        str     w0, [sp, 24]
        ldr     w0, [sp, 20]
        ldr     w1, [sp, 16]
        lsl     w0, w1, w0
        str     w0, [sp, 28]
        ldr     w1, [sp, 28]
        ldr     w0, [sp, 24]
        sdiv    w0, w1, w0
        str     w0, [sp, 28]
        ldr     w1, [sp, 28]
        ldr     w0, [sp, 12]
        sub     w0, w1, w0
        str     w0, [sp, 28]
        ldr     w0, [sp, 28]
        add     sp, sp, 32
        ret
        .size   func, .-func
        .section        .rodata
        .align  3
.LC0:
        .string "You win!"
        .align  3
.LC1:
        .string "You Lose :("
        .text
        .align  2
        .global main
        .type   main, %function
main:
        stp     x29, x30, [sp, -48]!
        add     x29, sp, 0
        str     w0, [x29, 28]
        str     x1, [x29, 16]
        ldr     x0, [x29, 16]
        add     x0, x0, 8
        ldr     x0, [x0]
        bl      atoi
        str     w0, [x29, 44]
        ldr     w0, [x29, 44]
        bl      func
        cmp     w0, 0
        bne     .L4
        adrp    x0, .LC0
        add     x0, x0, :lo12:.LC0
        bl      puts
        b       .L6
.L4:
        adrp    x0, .LC1
        add     x0, x0, :lo12:.LC1
        bl      puts
.L6:
        nop
        ldp     x29, x30, [sp], 48
        ret
        .size   main, .-main
        .ident  "GCC: (Ubuntu/Linaro 7.5.0-3ubuntu1~18.04) 7.5.0"
        .section        .note.GNU-stack,"",@progbits
```
- This gives us the function. The function tells us the process of how we need to go about solving the equation.
- Inputs are 68, 2 and 3
- We first need to multiply 68 with 2, twice. This gives is 272
- Then we need to divide it by 3 which gives us 90
- 90 in HEX is 5a which is our flag 
## Flag:

```
picoCTF{0000005a}
```

## Concepts learnt:

- .s is a source code file. `.s` means that it does not need to be preprocessed and `.S` means it needs to be preprocessed before being assembled
- ARM means Advanced RISC Machine. We basically need to analyse the programs made for different CPU architectures (in this case assembly language)
- `lsl     w0, w1, w0` means we need to shift w0 bitwise << two times
- `sdiv    w0, w1, w0` means we need to divide by the number at w1 (which is 3) 
## Notes:

- Took me some time to actually understand what it is trying to tell me :))

## Resources:

- https://azeria-labs.com/assembly-basics-cheatsheet/


***
