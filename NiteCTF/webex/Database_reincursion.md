# Database Reincursion
> First day as an intern at Citadel Corp and i'm already making strides! Got rid of that bulky unnecessary security system and implemented my own simple solution.
## solution:
- stage 1:
  We have a login page. By trial and error i find that some things are not allowed like `or`, `--` and we can enter maximum 60 characters. So i need to be creative about the SQLi injections
  I can enter the stage 1 using payload  `' BETWEEN 0 AND 1/*`
- stage 2:
  There is a directory where we can search by employee name and it will show department, email address, and a small note. We have a input box to login as admin. We have same restrictions as stage 1.
  `Drake` says `I heard Kiwi from Management has the passcode`. So now we search for `department = 'Management'` and `name = 'Kiwi`. We can use payload `Kiwi' AND department = 'Management' /*`

- stage 3:
  There is a reports directory that shows financial data with a search function. same restrctions as stage 1 and 2. We have a 'metadata registry'. There is a table with all values marked `REDACTED`.
  Using `' UNION SELECT * FROM metadata /*` we can get the redacted table `CITADEL_ARCHIVE_2077` with a column `secrets`
  Thus we use the payload `'UNION SELECT 1,secrets,'x','x' FROM CITADEL_ARCHIVE_2077/*` and get our flag

## Flag: 
`nite{neVeR_9Onn4_57OP_WonDER1N9_1f_175_5ql_oR_5EKWeL}`
