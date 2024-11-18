# HoneyPot-TPot

## Description 

Launched an AWS EC2 instance VM to simulate a honey pot for analysis. The VM runs on Debian 11 with an instance type of c4.2xlarge, which has 16 GB of RAM and 40 GB of storage. The honeypot uses T-POT which is an all-in-one solution for a honeypot platform and analysis on attacks againsts the VM. T-pot Hive contains ElasticSearch, Cyberchef, a real-time Attack Map, and Kibana that are integrated and used for this project.

All setup and configurations for the honeypot are within:
https://github.com/telekom-security/tpotce

## Project

After initilalizing the VM and leaving it for 9 days, Elasticsearch recorded 120,000+ attacks on the vulnerable machine. 


## Analysis
