## John Tabelisma
### Scenario Overview from BTLO:
	The SOC received an alert in their SIEM for 'Local to Local Port Scanning' where an internal private IP began scanning another internal system. Can you investigate and determine if this activity is malicious or not? You have been provided a PCAP, investigate using any tools you wish.
### Objectives:
- Analyze network traffic for suspicious activity and any IOCs
- Detect attacker behavior
- Identify exploitation techniques
- Reconstruct attack chain
### Tools Used:
- Wireshark
- Burp Suite Community ed.
<div style="page-break-after: always;"></div>
### Notes:
#### PCAP properties:
![[Pasted image 20260327144722.png]]
	Capture was 15 minutes and took place between 11:31 UTC and 11:46 UTC on July 2, 2021*.
	\*Note: PCAP Properties page shows time in UTC, while the following screenshots show time in local (five hours ahead)
#### IPv4 Conversations:
![[Pasted image 20260327151144.png]]
	Conversations to note: 10.251.96.4 -> 10.251.96.5 and 172.20.10.5 -> 172.20.10.2 transferred the largest amounts of bytes. The margin is significantly larger compared to the rest of the conversations.
	

#### TCP Conversations:
- **10.251.96.4 -> 10.251.96.5** had over 50 TCP convos of 118 bytes each one over a different port.
	- The only two conversations that had different amount of bytes (184 bytes) were ports 80 (HTTP) and 22 (SSH) meaning that **10.251.96.5** had those ports open
	- What follows is a lot of activity over port 80 with varying amounts of bytes being exchanged.
- Two unique IPs being contacted from IPs **172.20.10.2** and **10.251.96.4** over port 80
	- **34.122.121.32**
	- **35.224.170.84**
	- Possible C&C servers

#### Inspecting Packets
Packet #38 **172.20.10.5 -> 172.20.10.2**
- **172.20.10.5** ID'd as a Ubuntu Web Server
- **172.20.10.2** ID'd as a Windows PC
- Discovered credentials 
	- username: admin
	- password: Admin@1234
![[Pasted image 20260331145308.png]]
___
**10.251.96.4**'s TCP SYN port scan of **10.251.96.5** initiated at 16:33
___
Packet #2215 **10.251.96.4 -> 10.251.96.5** GET request
- **10.251.96.4**'s user-agent is *gobuster/3.0.1*.
- *gobuster* is a Linux tool used for scanning directories
___
Packet #7725 **10.251.96.5 -> 10.251.96.4** GET response
- 10.251.96.4 GET requested /info.php
- Server responded with PHP server info, including version of PHP running on server (7.2.24)
___
[16:34:05] **10.251.96.4**  ran a *gobuster* scan of **10.251.96.5** 
___
[16:36:17] **10.251.96.4** initiated an automated sql attack using *sqlmap 1.4.7* on **10.251.96.5**
___
![[Pasted image 20260402134930.png]]
	SQL injection by **10.251.96.4**
![[Pasted image 20260402152604.png]]
	Attack appears to access **10.251.96.5**'s cmd shell to access /etc/passwd
___
[16:40:39] **10.251.96.4** successfully uploaded a suspicious file called *dbfunctions.php* to **10.251.96.5**.
![[Pasted image 20260402153305.png]]
___
[16:42:35] **10.251.96.4** runs GET request to run python via *dbfunctions.php* to establish a connection to **10.251.96.5**. This enables **10.251.96.4** to gain keyboard access to the victim machine's cmd shell.
	Attacker ran commands "bash -i", "whoami", "cd", "ls", "python", and "rm db"
___
[16:45:56] Last activity from **10.251.96.4**
### Conclusion
The investigation confirmed a **successful compromise of an internal web server**. The attacker leveraged common web exploitation techniques, culminating in **web shell deployment and remote command execution**.