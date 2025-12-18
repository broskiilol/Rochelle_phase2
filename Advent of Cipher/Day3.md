# Day3
## Task 1: Introduction
> The message comes from King Malhare, the jealous ruler of HopSec Island, who’s tired of Easter being forgotten. He’s sent his Bandit Bunnies to attack TBFC’s systems and turn Christmas into his new holiday, EAST-mas. With McSkidy missing and the network under attack, the TBFC SOC team will utilize Splunk to determine how the ransomware infiltrated the system and prevent King Malhare’s plan from being compromised before Christmas.
### Learnings:
- In this challenge we will be using `Splunk`. It is a platform which is used to store machine data and it has tools which help us analyse this data.
- I need to connect to the Splunk `SIEM` for this, which is the Security Information and Event Management system which allow us to analyze during security monitoring.

I load the machine and complete the task.

## Task 2: Log analysis with splunk
### Solution: 
- For this challenge, the data has already been given. We just need to analyse it.
- I search `index=main` which displays all ingested logs. (changing the timeline to `all time`). I get 18 thousand events
- In the `sourcetype` i get two options. These are the datasets
   - web_traffic: events related to web connections to and from the web server.
   - firewall_logs: firewall logs (traffic allowed or blocked)
- Now we search through each sourcetype so we input query `index=main sourcetype=web_traffic` which gave me 17 thousand events now
- Now to group the events by a daily basis, i use query `index=main sourcetype=web_traffic | timechart span=1d count` which gives me 20 statistics which gives us a timeline of intense activity
- - Back to the events tab, we look thorugh the `User agent` in the `interesting fields` section which tells us details about the user agents.
  - in the `client_ip` field, we get the  IP addresses of the clients accessing the web server (we see 198.51.100.55 ip address standing out)
  - in `path` field we can see the path number
- We filter out the things not important to us (which are normal and not interesting to us) using `index=main sourcetype=web_traffic user_agent!=*Mozilla* user_agent!=*Chrome* user_agent!=*Safari* user_agent!=*Firefox*` which gets us down to 7 thousand events and one `client_ip` as mentioned before
- To calculate the sum of bytes transferred we use query `sourcetype=firewall_logs src_ip="10.10.1.5" AND dest_ip="198.51.100.55" AND action="ALLOWED" | stats sum(bytes_transferred) by src_ip` which gives us answer `126167`
