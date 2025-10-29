# 1. web gauntlet

> Can you beat the filters?

## Solution:

- I have 2 websites, one asking for username and password, other displaying filter
- for round 1, filter is shown as or (meaning i cannot use OR SQLi). 
- I read on SQLi and learnt about the meanings of various injections
- I first put `admin' --` and put random characters in the password which cracked the round 1. (The filter changed to OR and -- meaning i cannot use the same SQLi later)
  <img width="1918" height="815" alt="image" src="https://github.com/user-attachments/assets/fd532249-1323-4c17-aff5-4f7a775b7e78" />

- For round 2 i used `admin' /*` and random password. filter changed to `or and = like > < --`
  <img width="1920" height="883" alt="image" src="https://github.com/user-attachments/assets/03dcc1fd-2973-49a4-a7dc-91eed06cb87a" />

- Round 3 i used `admin';` and filter changed to `Round4: or and = like > < -- admin` meaning for round 4, i cannot use `admin` in the username. (also notice how `;` was not blocked)
  <img width="1920" height="805" alt="image" src="https://github.com/user-attachments/assets/c707be27-21df-4320-b959-51ac4c750860" />

- I reasearched about it (took quite some time) and learnt about concatenation. So i used `ad'||'min';` and bypassed it. filter changed to `Round5: or and = like > < -- union admin`, meaning i can use the same trick again (since they did not block '||')
  <img width="1920" height="702" alt="image" src="https://github.com/user-attachments/assets/28e75dd8-6688-4573-826d-b214afb5ea2b" />

- So for round 5, i just put `ad'||'min';` again
- <img width="1920" height="728" alt="image" src="https://github.com/user-attachments/assets/1601b723-067a-4761-927d-a8a234eb8771" />
- Checking filter.php we get the backend code behind the website. We also get the flag
  <img width="1900" height="892" alt="image" src="https://github.com/user-attachments/assets/25511ee6-87e4-4b39-af12-0fc6fdbc3aa8" />



## Flag:

```
picoCTF{y0u_m4d3_1t_79a0ddc6}
```

## Concepts learnt:

- When exploiting vulnerabilities (doing the SQLi thing), in the username place we use a valid name (like admin)
- The query we have here is `SELECT * FROM users WHERE username = 'user' AND password = 'user'`

## Notes:

- MISTAKE : THE PAGE KEPT RELOADING AND I KEPT LOSING THE SQLi I ALREADY DID WHICH FRUSTRATED ME A LITTLE BIT
- At first, i used the SQLi as `admin' ;` then `admin' /*` then `admin' --` but it kept getting stuck at round 3. After looking closely at the filters, i started using `admin' --` first, then `admin' /*` and then `;`. i still kept getting stuck on round 3. TURNS OUT, I WAS PUTTING AN UNECCESSARY SPACE BETWEEN "ADMIN" AND ";"

## Resources:

- (https://www.invicti.com/blog/web-security/sql-injection-cheat-sheet/)


***
