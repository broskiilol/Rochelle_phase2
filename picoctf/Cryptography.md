# 1. Custom Encryption

> Can you get sense of this code file and write the function that will decode the given encrypted file content.

## Solution:

- We are given a pythin code.
```py
from random import randint
import sys


def generator(g, x, p):
    return pow(g, x) % p


def encrypt(plaintext, key):
    cipher = []
    for char in plaintext:
        cipher.append(((ord(char) * key*311)))
    return cipher


def is_prime(p):
    v = 0
    for i in range(2, p + 1):
        if p % i == 0:
            v = v + 1
    if v > 1:
        return False
    else:
        return True


def dynamic_xor_encrypt(plaintext, text_key):
    cipher_text = ""
    key_length = len(text_key)
    for i, char in enumerate(plaintext[::-1]):
        key_char = text_key[i % key_length]
        encrypted_char = chr(ord(char) ^ ord(key_char))
        cipher_text += encrypted_char
    return cipher_text


def test(plain_text, text_key):
    p = 97
    g = 31
    if not is_prime(p) and not is_prime(g):
        print("Enter prime numbers")
        return
    a = randint(p-10, p)
    b = randint(g-10, g)
    print(f"a = {a}")
    print(f"b = {b}")
    u = generator(g, a, p)
    v = generator(g, b, p)
    key = generator(v, a, p)
    b_key = generator(u, b, p)
    shared_key = None
    if key == b_key:
        shared_key = key
    else:
        print("Invalid key")
        return
    semi_cipher = dynamic_xor_encrypt(plain_text, text_key)
    cipher = encrypt(semi_cipher, shared_key)
    print(f'cipher is: {cipher}')


if __name__ == "__main__":
    message = sys.argv[1]
    test(message, "trudeau")
```
- The code tells us that a XOR encryption is used with a shared key. *REFER TO DIFFE-HELLMAN FORMULA*
- The XOR key will take the message and will take the word `trudeau` and form its own word. It is using a multiplicative cipher. So each letter in the messege will be multiplied with a number. This "number" is formed used the mentioned diffe-hellamn formula.
- Now we go in the opposite direction of this. We first divide the shared key by 311 (`311` number we got from code)
- Then we reverse the XOR encryption using the `trudeau` message (again gotten from code)
Now we create a code to solve this
```py
def decrypt(cipher, key):
  plaintext = "" #initialising this
  for num in cipher:
      # Reverse the multiplicative encryption. Before-- cipher = (char * key * 311)
      if num == 0:
          plaintext += chr(0)
      else:
          char_val = num // (key * 311)
          plaintext += chr(char_val)
  return plaintext

def dynamic_xor_decrypt(cipher_text, text_key):
  plain_text = ""
  key_length = len(text_key)
  for i, char in enumerate(cipher_text):
      key_char = text_key[i % key_length]
      decrypted_char = chr(ord(char) ^ ord(key_char))
      plain_text += decrypted_char
  return plain_text[::-1]  # Reverse at the end since encryption was done in reverse

def generator(g, x, p):
  return pow(g, x) % p

def decode_flag(cipher):
  p = 97
  g = 31
  a = 97 #the a and b values given in enc_flag
  b = 22
  
  # Regenerate the shared key
  u = generator(g, a, p)
  v = generator(g, b, p)
  key = generator(v, a, p)
  
  # First decrypt the multiplicative cipher
  semi_cipher = decrypt(cipher, key)
  
  # Then decrypt the XOR cipher
  plaintext = dynamic_xor_decrypt(semi_cipher, "trudeau")
  
  return plaintext

#The cipher from the file
cipher = [151146, 1158786, 1276344, 1360314, 1427490, 1377108, 1074816, 1074816, 386262, 705348, 0, 1393902, 352674, 83970, 1141992, 0, 369468, 1444284, 16794, 1041228, 403056, 453438, 100764, 100764, 285498, 100764, 436644, 856494, 537408, 822906, 436644, 117558, 201528, 285498]

#Decode the flag
flag = decode_flag(cipher)
print(f"Decoded flag: {flag}")
```

## Flag:

```
picoCTF{custom_d2cr0pt6d_e4530597}
```
## Concepts learnt:
- Mixing that XOR does
- Forming a reverse python code...very VERY tiring
- Diffe-Hellman formula: It is basically when two people are there, and they need to talk to each other in a code word without the other people in the room understanding it. But they havent previously decided what code language they are using, but it is still possible...(if that makes sense)
- So there are public parameters and private keys. The two people agree on using a public parameter (which is very large prime number, p and a base, g). They also choose separate private keys (numbers, say a and b). They compute their public keys separately using (A= g raised to a mod p) and (B= g raised to b mod p) and sent it to each other. Then they compute each others secret using (A raised to b mod p) and (B raised to a mod p). Thus they arrive at the same key

## Notes:

- Took a LOT of time to get diffe hellman and python and understanding both 

***
# 2. RSA Oracle

> Can you abuse the oracle?
An attacker was able to intercept communications between a bank and a fintech company. They managed to get the message (ciphertext) and the password that was used to encrypt the message.
After some intensive reconassainance they found out that the bank has an oracle that was used to encrypt the password and can be found here nc titan.picoctf.net 50049. Decrypt the password and use it to decrypt the message. The oracle can decrypt anything except the password.

## Solution:

- When i used to decrypt anything, i was not getting an output so i used the `e` to encrypt it and i inputed `1` to see what happens
```bash
what should we do for you?
E --> encrypt D --> decrypt.
e
enter text to encrypt (encoded length must be less than keysize): 1
1

encoded cleartext as Hex m: 31

ciphertext (m ^ e mod n) 4374671741411819653095065203638363839705760144524191633605358134684143978321095859047126585649272872908765432040943055399247499744070371810470682366100689
```
- Here, `m` is our input. Converting our input to hex we get 0x31. This has been raised to an exponent `e`and `mod n`. We need to find this e and n now.
- So now, i told it to encrypt 1,2,3 and 4. Using this i plan to find N (i am using e=65537 since it is most common)
```py
 import math

 e = 65537
 data = [
        (49, 4374671741411819653095065203638363839705760144524191633605358134684143978321095859047126585649272872908765432040943055399247499744070371810470682366100689),
        (50, 4707619883686427763240856106433203231481313994680729548861877810439954027216515481620077982254465432294427487895036699854948548980054737181231034760249505),
        (51, 1998517197048216725617978890728205902760633363770165103499700157925986170022682604311921651991344892635565706489644418147980643978563559991322776155635395),
        (52, 3993239489061277327472930109138093827255646312769901312414509207541733524779884801267968848884701166599834406248783129646083261476137481855550108336137485),
    ]

 def getN(stuff, e):
        a, b = stuff[0]
        n_val = b - pow(a, e)
        for x, y in stuff[1:]:
            tmp = y - pow(x, e)
            n_val = math.gcd(tmp, n_val)
        return abs(n_val)

 print(getN(data, e))
```
- I got the N as `5507598452356422225755194020880876452588463543445995226287547479009566151786764261801368190219042978883834809435145954028371516656752643743433517325277971`
- Using logic that c'= c*(2 raised to e) mod N, and using the given password encryption
```py
n = int("5507598452356422225755194020880876452588463543445995226287547479009566151786764261801368190219042978883834809435145954028371516656752643743433517325277971")
e = 65537
C_pass = int(2575135950983117315234568522857995277662113128076071837763492069763989760018604733813265929772245292223046288098298720343542517375538185662305577375746934)   
s = 2
cprime = (C_pass * pow(s, e, n)) % n
print(cprime)
```
Which gave me output as `1497468420772703700530952066367233078175680172113567583445473865853762198698568119742221024802861792369434059769544669252818342459522960042231001657821006`
- Decrypting this in the oracle gave me 
```bash
Enter text to decrypt: 1497468420772703700530952066367233078175680172113567583445473865853762198698568119742221024802861792369434059769544669252818342459522960042231001657821006
decrypted ciphertext as hex (c ^ d mod n): 6468c4c6c4
decrypted ciphertext: dhÄÆÄ
```

- I ran another code using the HEX value to to find m
```py
  n=5507598452356422225755194020880876452588463543445995226287547479009566151786764261801368190219042978883834809435145954028371516656752643743433517325277971

  p2_hex = "6468c4c6c4"
  p2 = int(p2_hex, 16)  


  s = 2
  inv_s = pow(s, -1, n)

  m = (p2 * inv_s) % n

  k = (n.bit_length() + 7) // 8
  m_bytes = m.to_bytes(k, "big")

  print("m (utf-8):", m_bytes.rstrip(b'\x00').decode('utf-8'))
```
This got me `m (utf-8): 24bcb`
- Now i put this in the openssl to get the flag
```bash
openssl enc -aes-256-cbc -d -in secret.enc
enter AES-256-CBC decryption password:
*** WARNING : deprecated key derivation used.
Using -iter or -pbkdf2 would be better.
picoCTF{su((3ss_(r@ck1ng_r3@_24bcbc66}
```

## Flag:

```
picoCTF{su((3ss_(r@ck1ng_r3@_24bcbc66}
```

## Concepts learnt:
- 

## Notes:

- After getting the password, i did not know what to do with it (i didnt see the hint)
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
