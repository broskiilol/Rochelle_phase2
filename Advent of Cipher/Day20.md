# Day 20: Race Conditions - Toy to The World
## Soultion:
- First i confirgured firefox. On the browser i opened FoxyProxy and made it so that all browser requests are sent to Burp
- Then i opened burp suite Community edition. i use the Proxy and Repeater option to exploit the race condition.
- I turn off the intercept so that Burp Suite no longer holds your browser requests and allows them to pass through normally
- Then in firefox i open `http://10.49.163.97` to get
- <img width="1389" height="530" alt="image" src="https://github.com/user-attachments/assets/17ef406d-1606-4886-842d-b4cce7ae8a59" />
- I  enter the credentials, username: attacker and password: attacker@123 to get
- <img width="1443" height="518" alt="image" src="https://github.com/user-attachments/assets/3506b9ef-3821-4d46-bdbd-13fbf8fb59be" />
- First, for SleighToy, i make a legimate purchase by adding to cart and checking out. Now i go back to the burp suite in `Proxy > HTTP history`
- <img width="1887" height="150" alt="image" src="https://github.com/user-attachments/assets/eeeaa04d-ad10-4af5-9359-18d061060237" />
- at the `process_checkout`, i send it to the Repeater which will copy the exact HTTP request (headers, cookies, body) into Burp’s Repeater tool.
- In the repeater tab, i make a group of the request sent and duplicate the tab 15 times. then i Send group in parallel (last-byte sync), which launches all copies at once and waits for the final byte from each response, maximising the timing overlap to trigger race conditions.
- The server will attempt to handle them simultaneously, which will cause the timing bug to appear (due to multiple orders being processed at once). I am placing 15 orders at once, so the order in stock will be in negative...
- <img width="1755" height="520" alt="image" src="https://github.com/user-attachments/assets/c4d6da73-6f01-4d66-bf4b-457951ba49c6" />

I do the same for the Bunny Plush (Blue).
<img width="1784" height="507" alt="image" src="https://github.com/user-attachments/assets/abb4f9f9-dfa5-4c71-a3d5-16b0e6909d55" />


## Flag: 
- `THM{WINNER_OF_R@CE007}`
- `THM{WINNER_OF_Bunny_R@ce}`

## Notes:
- A race condition happens when two or more actions occur at the same time, and the system’s outcome depends on order in which they finish.
- Types of Race Conditions: - Time-of-Check to Time-of-Use (TOCTOU)
                            - Shared resource
                            - Atomicity violation
