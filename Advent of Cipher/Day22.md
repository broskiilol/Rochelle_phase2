# Day 22: C2 Detection - Command & Carol
> The TBFC is very wary since the last series of attacks by the underlings of King Malhare. They are on full alert for anything happening. But they are getting restless; it is too quiet. Sir Elfo of the TBFC takes the initiative and proposes to hunt for Command and Control traffic using the meticulously collected network traffic. A majority of the TBFC elves object, we don't have the time to go through so much network traffic! Sir Elfo chuckles, don't fret, for I have a powerful tool to assist us! I present to you RITA, Real Intelligence Threat Analytics. We just need to convert our PCAP file to Zeek logs, and RITA will do the rest! Anyone can do it, just follow today's tasks.
## Solution:
- RITA only accepts network traffic input as Zeek logs so we need to first convert the .pcap file to ZEEK so i did `zeek readpcap ~/pcaps/rita_challenge.pcap zeek_logs/pikabot`
- Then i did `rita import --logs ~/zeek_logs/pikabot/ --database pikabot` and opened rita using `rita view pikabot` to get:
- <img width="1881" height="675" alt="image" src="https://github.com/user-attachments/assets/5ea18028-4a44-4ba9-bfd8-108ecd87a7da" />

- Here we can see that there are 6 sources connecting to the network. We can see that the maximum number of connection is 40
- Port 80 is used port by the host `10.0.0.13`
  <img width="1893" height="536" alt="image" src="https://github.com/user-attachments/assets/61acd865-7419-4146-9f65-0f88319065e3" />
- using `dst:rabbithole.malhare.net beacon:>=70 sort:duration-desc` search we can search for all entries that communicate to rabbithole.malhare.net with a beacon score greater than 70% and sorted by connection duration (descending)
- Threat modifier `Prevalence` tells us about the number of hosts communicating to a destination
- Thus i completed the challenge.

## Notes:
- Packet capture (PCAP) is a networking practice involving the interception of data packets travelling over a network. The inspection of these packets allows IT teams to identify issues and solve network problems affecting daily operations.
- RITA - Real Intelligence Threat Analysis- detect command and control (C2) communication by analyzing network traffic captures and logs.
- Zeek is an open-source network security monitoring (NSM) tool
- Threat Modifiers: criteria to determine the severity and likelihood of a potential threat. modifiers:
     - MIME type/URI mismatch: Flags connections where the MIME type reported in the HTTP header doesn't match the URI. This can indicate an attacker is trying to trick the browser or a security tool.
     - Rare signature: Points to unusual patterns that attackers might overlook, such as a unique user agent string that is not seen in any other connections on the network.
     - Prevalence: Analyzes the number of internal hosts communicating with a specific external host. A low percentage of internal hosts communicating with an external one can be suspicious.
     - First Seen: Checks the date an external host was first observed on the network. A new host on the network is more likely to be a potential threat.
     - Missing host header: Identifies HTTP connections that are missing the host header, which is often an oversight by attackers or a sign of a misconfigured system.
     - Large amount of outgoing data: Flags connections that send a very large amount of data out from the network.
     - No direct connections: Flags connections that don't have any direct connections, which can be a sign of a more complex or hidden command and control communication.
