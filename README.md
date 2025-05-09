Chapter 4: Botnets and Scan Detection

Book: Ethical Hacking: A Hands-On Introduction to Breaking In

Author: Daniel G. Graham

Lab Report by: luis aka Lucien Vale

Date: 5/8/2025
⸻

Overview

This chapter was focused on the fundamentals of botnets, command-and-control infrastructure, custom TCP scanning, and real-time network threat detection using Python and the Scapy library. The entire process was completed manually — every script was typed, debugged, and executed without shortcuts.

My lab consisted of a Kali Linux attacker machine and a Metasploitable2 victim, both running inside a virtualized environment. Throughout the chapter, I built:
	
 • A simple botnet using HTTP
	
 • A command delivery system
	
 • A custom TCP SYN scanner
	
 • A real-time Xmas scan detector

⸻

1. Botnet Server and Bot Client

http-server.py

This script serves as the command-and-control (C2) server, delivering whatever command is inside commands.sh to every bot that connects. It listens on port 8000 and returns the contents of that file using Python’s built-in HTTP server.

commands.sh

This file contains shell instructions the bots will execute. For example:

bash: 

ping 172.217.3.206

This was used to simulate a DDoS attack by instructing all bots to simultaneously flood a remote server with ICMP requests.

bot.py

The bot connects to the C2 server every 10 seconds, fetches commands.sh, and executes its contents using os.system(). This simulates a real-world zombie client that can be updated remotely by simply editing the server’s command file.

⸻

Troubleshooting & Lessons Learned
	
 • I initially forgot to save my Python script and had to rewrite everything manually — reinforcing the discipline of file management.
	
 • Learned how to manually transfer files to Metasploitable using scp, as well as how to execute bots on remote machines.

 • Understood how primitive a botnet really is — and how deadly it can be, even with just a few lines of Python.

 • Learned that Python’s os.system() can be used to execute any shell command, making it dangerously powerful in the wrong hands.
<img width="1280" alt="Screenshot 2025-05-09 at 12 39 10 AM" src="https://github.com/user-attachments/assets/a7be1ed1-fde9-4082-9070-41410d48efe1" />

2. SYN Port Scanner

synscan.py

This script was built entirely with Scapy to perform TCP SYN scans — replicating what Nmap does under the hood with -sS.

Features:
	
 • Sends a single SYN packet to a specified IP and port
	
 • Detects SYN-ACK flags (0x12) as confirmation the port is open
	
 • Optionally includes an ICMP probe to check if the host is online

Key Takeaways
	
 •Learned to interpret raw TCP flags
	
 • Gained insight into packet structure: IP()/TCP() layering in Scapy
	
 • Confirmed port status by analyzing responses with .show() and .flags
	
 • Switched from ICMP-based filtering to direct TCP probing when the ping failed due to firewall or VM config

 Troubleshooting
	
 • ICMP probes failed initially due to VM isolation settings — resolved by skipping the ping and scanning directly
	
 • Mixed tabs and spaces caused TabError in Python — taught me to stick with one indentation style
	
 • Validated results using real Metasploitable ports — confirmed with open responses

<img width="1280" alt="Screenshot 2025-05-09 at 12 41 49 AM" src="https://github.com/user-attachments/assets/2d2013c4-5c60-4325-8bc9-de187e362ac7" />

3. Xmas Scan Detection

detectXmas.py

This was a custom-built intrusion detection tool that sniffs for TCP packets with flags FIN, PSH, and URG set — the signature of an Xmas scan (0x29).

Using Scapy’s sniff() function, the script inspects every TCP packet and prints an alert when a scan is detected.
<img width="1280" alt="Screenshot 2025-05-09 at 12 44 36 AM" src="https://github.com/user-attachments/assets/97826a37-0da9-4e3e-b475-db55337374b5" />

What I Learned
	
 • Understood how stealth scans work by abusing TCP flag behavior

 • Identified how attackers evade basic firewall detection

 • Practiced live packet capture with Scapy (a lightweight alternative to Wireshark)

 • Created a Python-based IDS that flags threats in real time

⸻

Results

Launched a Xmas scan from Metasploitable using:
Bash
nmap -sX 192.168.1.79

The detector lit up — showing every incoming scan attempt with source, destination, and port.
<img width="1280" alt="Screenshot 2025-05-09 at 12 47 17 AM" src="https://github.com/user-attachments/assets/366ef022-28c2-4946-9352-6b54a36ccdc6" />

Conclusion

This chapter wasn’t just about copying code — it was about thinking like a real hacker. I built everything by hand. I made mistakes. I debugged them. I learned to look at TCP packets like blueprints, understood how command-and-control logic works, and wrote tools that can:
	
 • Scan networks
	
 • Detect attacks
	
 • Simulate distributed threats

Everything documented here was created manually using Kali Linux, Python 3, and the Scapy library. The knowledge gained goes beyond the surface — it touches the protocol level, the mindset of the adversary, and the mechanics of real-world cybersecurity.

Authored by: Lucien Vale

