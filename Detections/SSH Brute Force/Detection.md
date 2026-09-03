## Attack Scenario 
An SSH brute force attack is an attack vector where a threat actor attempts to gain unauthorized access to a system by repeatedly attempting to authenticate via SSH using different username and password combinations. A large number of failed SSH attempts from a single source IP or within a short time frame can indicate a brute-force attack. If successful, this attack can grants the attacker remote access to a system which would likely be devastating.

## Simulated Attack
 🔴 The attack: an outside attacker attempting a SSH Brute Force attack against a machine.

This attack was simulated by having the Kali Linux VM attack the Ubuntu Desktop VM using [hydra](https://www.kali.org/tools/hydra/), a built in Kali Linux password cracking utility.

To start, I ran: `sudo apt install wordlists` which downloads a compressed rockyou.txt file, containing millions of passwords exposed in the RockYou 2009 Data Breach. 

Usernames are sometimes easy to find. Especially in enterprises, user names commonly follow patterns like first + last name or first initial plus last name, and so on. Usernames can also be found via OSINT, data breaches, and more. For this attack simulation, we will assume the attacker has found a username on their target system to attack. 

For the purposes of my project, I didn't use the millions of passwords in rockyou.txt. Rather I made passwords.txt, and then did `head -n 1000 rockyou.txt > passwords.txt` and then used that file to perform the brute force. Here is the outcome of running the brute force attack on the attacker's side:
<img width="1772" height="266" alt="image" src="https://github.com/user-attachments/assets/6cd84cd7-5a00-43bc-8da0-b08cb53c90c4" />

## Detection 
Looking in Wazuh Dashboard > Threat Hunting, we see that these SSH failures were logged:
<img width="3436" height="1760" alt="image" src="https://github.com/user-attachments/assets/f860beba-e5bd-461d-b6a0-e76c21ad4fc7" />


include detection name, mitre mapping, etc

### Analyst Investigation

### False Positives

