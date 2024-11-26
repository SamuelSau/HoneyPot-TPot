# HoneyPot-TPot

![Edited_T-PotDashboard](https://github.com/user-attachments/assets/dbba343a-cc21-4f35-83c9-f48e33704a83)

## Description 

This project aims to analyze real-world threats directed to a honey pot for analysis through the creation of an AWS EC2 instance VM that leverages the T-Pot framework. After initilalizing the VM and leaving it for 9 days, Elasticsearch recorded 120,000+ attacks on the honeypot.

## Setup

![Screenshot 2024-11-17 203629](https://github.com/user-attachments/assets/8b61764f-c91e-4fac-ab55-937165cc6ec4)

The VM runs on Debian 11 with an AWS instance type of c4.2xlarge, which has 16 GB of RAM and 40 GB of storage.

![Screenshot 2024-11-17 203716](https://github.com/user-attachments/assets/09c4365f-9d3b-47f4-b4ff-a4085754b608)

The security rule that was configured for this AWS EC2 instance only allowed certain ports. Since T-Pot had specific configuration for the ports, for instance to SSH to the VM it would be on TCP port 64295 instead of 22, I decided for the inbound rules to only allow my IP address to have access. I would access this on the browser and have a login that was already prepared from before for installation to access the T-Pot functionalities.

![tpotsocial](https://github.com/user-attachments/assets/b12c7a4e-5fd5-44c0-9d95-a9d922bda1bf)

T-POT is an all-in-one solution for a honeypot platform that can be used to analyzed on attacks againsts the VM. T-pot Hive contains ElasticSearch, Cyberchef, a real-time Attack Map, Kibana, Spiderfoot, and Suricata that are integrated and used for this project.

All setup and configurations for the honeypot are below:
https://github.com/telekom-security/tpotce

## Analysis

![Screenshot 2024-11-17 193553](https://github.com/user-attachments/assets/23cbb1bd-2fa3-4b61-837d-fd25c12e37b2)

This is an attack map that was running earlier on that was used to pinpoint potential locations in real-time when an attacked occured.

![Screenshot 2024-11-17 193510](https://github.com/user-attachments/assets/9d6528f1-222a-4499-ac62-be00ddc3077a)

Above, this is final result of the T-Pot dashboard that over the 9 day period.

![Screenshot 2024-11-17 193846](https://github.com/user-attachments/assets/f80f0254-c5df-43f1-b4ba-1ca805da2b92)

As seen from the image above, the most targeted honeypot with over 82,000+ attacks was Dionaea. Dionaea is a derivation of nepenthes, which is a low interaction honeypot that emulates known vulnerabilities to collect information about potential attacks. We can see that mostly SMB was targeted.

### Why SMB?

![Screenshot 2024-11-17 193900](https://github.com/user-attachments/assets/29fb85bc-4355-4c38-9292-f6807980bc55)

It seems that most of the attacks are targeting port 445, which is SMB. It would make sense why attacker would try to target SMB since many organizations still run outdated systems, there are vulnerabilities that are present in SMB, for instance like the EternalBlue (MS17-010) exploit. I believe also that frameworks like Metasploit are popular tools with plethora of options that can be used for SMB. Another consideration is that SMB can be considered a "low-hanging fruit" where most of the scripts could be automated for attackers and bots to use, which means if exploited then potentially they can escalate to admin privileges. It is also interesting to note that over 25% of the Operating System from these adversaries are either Windows 7 or 8, which could be a reason why most of the adversary tested SMB since Windows supports SMB.

### Countries of Interest

The top 3 countries attacking the honeypot were China, DR Congo, and the United States. I was rather surprised how the DR Congo was in the top 3, as I expected other countries that were more likely involved in cybercrime due to frequencies in news reports like Russia or North Korea. It really is intersting that both China and the DR Congo almost exclusively only performed SMB attacks, while the United States targeted a diverse set of ports, where majority were 80 and 443, HTTP and HTTPs, respectively.

### Tagclouds

![Screenshot 2024-11-17 193527](https://github.com/user-attachments/assets/60b4e877-3056-4311-8fc4-509ce28b80ec)

![Screenshot 2024-11-17 194457](https://github.com/user-attachments/assets/93390789-eba7-4ef0-a5e3-4cad393822d1)

![Screenshot 2024-11-17 194514](https://github.com/user-attachments/assets/37cf4283-b159-42b1-8048-e8151573f81e)

On the username and password tagclouds shown above, there seems to be a common pattern. Most of the fields seem to try to find default credentials, such as password = 123456, to hopefully gain access to the system. It might be that the adversary and bots are running automated scripts that check for any default or common credentials against the system for access. The technique used must be "password spraying", where they try for common credentials to see if it's easily accessible or not.

### Top attackers

The top attacker with the redacted IP address performed 16,000+ attacks. Of course, all this work had to be done through automation and various tools that tried to test for different vulnerabilities.

![Screenshot 2024-11-17 193803](https://github.com/user-attachments/assets/0b0d078b-b755-404d-bb9e-10edb14e4825)

### Spiderfoot

![Screenshot 2024-11-17 193935](https://github.com/user-attachments/assets/450ff6be-3b60-41b7-b723-c2f59181c374)

Using Spiderfoot, we can use a crawler that checks for all types of information that is redacted for that specific IP address of the top attacker and gain information like email addresses, country codes, and more.

![Screenshot 2024-11-17 194057](https://github.com/user-attachments/assets/26a193a9-0542-4284-a7ef-da7365945cfc)

![Screenshot 2024-11-17 194130](https://github.com/user-attachments/assets/ddcc7e14-1f98-44a5-ab3a-7d9625ff2570)

![Screenshot 2024-11-17 194001](https://github.com/user-attachments/assets/f5b90484-c2f4-4b6c-9909-cf0df183004f)

It's also a good point to note that the IP addresses and information displayed might not always be entirely accurate since adversaries can also use techniques like VPNs to masquerade to masquerade their real IP addresses and other sensitive information.

### Suricata

![Screenshot 2024-11-25 193132](https://github.com/user-attachments/assets/ec8a8eea-2cab-4d90-b3bd-b1a346d776f9)

The top CVE listed by Suricata was CVE-2020-11899 with over 3,700 attempts of using this vulnerability. The vulnerability attempts to take advantage of the Treck TCP/IP stack before 6.0.1.66 with an IPv6 Out-of-bounds Read. Specifically, Treck TCP/IP is a software library often used in embedded systems for enabling internet connectivity and this vulnerability allows an attacker to read memory beyond the intended buffer boundaries. If this was exploited, then the adversary has many different options such as:
* Read sensitive memory data.
* Potentially crash the device or cause a denial of service (DoS).
* Leverage the out-of-bounds read to gain further control, depending on the specific implementation.

More information: 
* https://nvd.nist.gov/vuln/detail/CVE-2020-11899 
* https://www.cisa.gov/news-events/ics-advisories/icsa-20-168-01

With over 370,000 Suricata signatures, the description was a "SURICATA STREAM Packet with broken ack". This alert indicates that the Intrusion Detection System (IDS) has detected an anomaly in the TCP acknowledgment (ACK) sequence of a packet. That means the TCP packets were malformed, either due to packets being corrupted or the adversary trying to craft a payload that modified the packet to avoid detection from the IDS. 

### VM Statistics

![Screenshot 2024-11-17 203658](https://github.com/user-attachments/assets/e2e54427-46df-416b-9318-b58c56de37d7)

![Screenshot 2024-11-17 203906](https://github.com/user-attachments/assets/5a9efc2d-5fa7-48f9-b56b-dc41d24699ea)

![Screenshot 2024-11-17 203825](https://github.com/user-attachments/assets/f5cc9b3d-5bde-4649-8607-60abf58d71d4)

The CPU utilization never spiked more than 30%, which meant even under high load it was not an issue with 16GB. However, I noticed that before when I initially did setup the AWS instance type to a lower spec of RAM, like 8GB, when there was a heavy load of activity from outbound traffic, that the server would crash and meant the instance needed to be restarted. I had created an alert for the EC2 instance to check if the status check failed and would restart the system. Soon after, I learned that T-Pot generally recommends for more than 8GB for the VM to run their services smoothly, in which I changed and the issue was solved. As you can also see for the network in and out, in addition to the read and write latenacy, there is a correlation with these spikes. For instance, on 11/13, there seems to be the tallest spikes, most likely due to heavy load on attackers interacting with the VM. It also happened that the status check failed shows that the instance was down for a while during those time, meaning they most likely tried to commit some type of DDoS or DoS attack, which meant I had to upgrade the instance to handle those loads.

## Conclusion

After monitoring the VM through T-Pot and through the AWS platform, it was interesting to see real-time attacks take place. This project showed me how to set up a honeypot, but also brought awareness to what types of activities that adversaries might try when attempting to compromise a system. For instance, there seems to be a lot of use for automated scripts to enumerate all the information of the VM and try out different attack vectors to hopefully exploit and gain control. As an aspring cybersecurity analyst, investing money and time into this project really gave me great insight and experience to learning more about this field and security as a whole. 
