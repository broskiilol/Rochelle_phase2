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
- The RSA algorithm involves four steps: key generation, key distribution, public-key(encryption), and private key(decryption)
- If we take 3 positive integers e, d, and n, [x (0 ≤ x < n)], both (x^e)^d and x have the same remainder when divided by n. If is very difficult to calcuate x if d is not given (which makes it secure for encryption purposes)

## Notes:
- After getting the password, i did not know what to do with it (i didnt see the hint)
- After seeing the hint i didnt understand the hint. I thought openssl was an app needed to be installed and was searching for that for like an hour...
## Resources:
- https://en.wikipedia.org/wiki/RSA_cryptosystem#Operation 
# 3. miniRSA

> Let's decrypt this: ciphertext? Something seems a bit small

## Solution:

- The ciphertext given has the values of c, N and e...which is ideally perfect! But on closer look, the e (=3) is very small, N is very big. 
- From the text we see that ciphertext = plaintext³ N (e=3). So on taking the initial length and dividing it by 3 we get the initial length of the original number. Now we can make a number of that size using <<1
- we write a loop to keep on guessing this number by squaring the number and checking how well many times this number fits into our og number using c//(x*x) and each time the guess becomes more and more accurate. We will stop when the guess becomes worse or equal to the previous guesses. This will finally be converted to bytes and then to plaintext.
- Thus the code will be like
```py
 c=2205316413931134031074603746928247799030155221252519872650080519263755075355825243327515211479747536697517688468095325517209911688684309894900992899707504087647575997847717180766377832435022794675332132906451858990782325436498952049751141
x = 1 << ((c.bit_length() + 2)//3)          
while True:
   y = (2*x + c//(x*x)) // 3
   if y >= x:
       m = x; break
   x = y
plaintext = m.to_bytes((m.bit_length()+7)//8, 'big')
print(plaintext.decode())
```
## Flag:

```
picoCTF{n33d_a_lArg3r_e_d0cd6eae}
```

## Concepts learnt:
- Learnt the functioning of an RSA and how it actually works, the meaning of e, N, c and how to compile it into an algorithm

## Resources:
- https://en.wikipedia.org/wiki/RSA_cryptosystem#Operation

