# Day 5: IDOR- Santa's Little IDOR
> The elves of Wareville are on high alert since McSkidy went missing. Recently, the support team has been receiving many calls from parents who can't activate vouchers on the TryPresentMe website. They also mentioned they are receiving many targeted phishing emails containing information that is not public. The support team is wary and has enlisted the help of the TBFC staff. When looking into this peculiar case, they discovered a suspiciously named account named Sir Carrotbane, which has many vouchers assigned to it. For now, they have deleted the account and retrieved the vouchers. But something is going on. Can you help the TBFC staff investigate the TryPresentMe website and fix the vulnerabilities?
## Task 1: Introduction
I connect to the VM and start attack box
## Task 2: IDOR On the Shelf
### Solution:
- We have been given website `http://10.48.175.178`. I login to it using the username given
- By changing the `user_id` (seen in the networks > response) we can become different users.
- The question asks which parents (or user) has 10 children, by trial and error I go up to user_id= 15 and get the 10 children.

### Notes:
- IDOR stands for Insecure Direct Object Reference
- Every website has two things it does
  - Authentication:
    - There is something called session information which basically is a cookie/token . When it expires, the website takes you back to the login page
  - Authorisation: `Authorization cannot happen before authentication. If the application doesn't know who you are, it cannot verify what permissions your user has. `
- Privelage escalation types:
  - Vertical privilege escalation: gain access to more features. (you may be a normal user on the application, but can perform actions that should be restricted for an administrator)
  - Horizontal privilege escalation: use a feature you are authorized to use, but gain access to data that you are not allowed to access (you should only be able to see your accounts, not someone else's accounts)
IDOR IS HORIZONTAL
