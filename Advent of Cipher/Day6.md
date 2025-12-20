# Day 6: Malware Analysis - Egg-xecutable
## Task 1: Introduction
Started the virtual machine
## Task 2: Malware analysis using Sandboxes
### Soltuion:
- Static analysis:
  - I open PeStudio and open HopHelper.exe file in it.
    <img width="1773" height="710" alt="image" src="https://github.com/user-attachments/assets/a9e5e5ec-8c9a-45fd-a3ba-9ebe958bee3d" />
  - I see the `file > sha256` property which is: `F29C270068F865EF4A747E2683BFA07667BF64E768B38FBB9A2750A3D879CA33`
  - Now i see the `strings (count > 4194)`
    <img width="1334" height="240" alt="image" src="https://github.com/user-attachments/assets/5e4a2dbb-9055-4ef3-9fad-3305754612c1" />
  - The flag to be found is `THM{STRINGS_FOUND}`

- Dynamic analysis
   - I open Regshot and take the first shot of the desktop and second shot of the hopper.exe file and compare them.



### Notes:
- Malware analysis is the process of examining a malicious file to understand its functionality, operation, and methods for defence against it. By analysing a malicious file or application, we can see how it operates and know how to prevent it.
- There are two main branches of malware analysis:
   - Static analysis focuses on inspecting a file without executing
   - dynamic analysis involves execution.
- golden rule in malware analysis: never run dangerous applications on devices you care about. Thus we use sandboxes. sandboxes are safe environments where malicious applications can perform their actions without risking sensitive data or impacting other systems
- PeStudio: Helps statically analyse malware.
- `Regshot` is a widely used utility, especially when analysing malware on Windows. It works by creating two "snapshots" of the registry—one before the malware is run and another afterwards. The results are then compared to identify any changes.
- `strings` are sequences of readable characters present within an executable like IP addresses etc.
