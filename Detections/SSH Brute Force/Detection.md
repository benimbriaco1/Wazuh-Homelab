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
Looking in Wazuh Dashboard > Threat Intelligence > Threat Hunting, we see that these SSH failures were logged:
<img width="3436" height="1760" alt="image" src="https://github.com/user-attachments/assets/f860beba-e5bd-461d-b6a0-e76c21ad4fc7" />

And looking in Wazuh Dashboard > Explore > Discover, we can see the event logs:
<img width="3386" height="1666" alt="image" src="https://github.com/user-attachments/assets/aaa13c8f-4668-4b91-a543-f74766b790ef" />

To add my own detection rule for SSH brute force attacks, I added the following code to /var/ossec/etc/rules/local_rules.xml:

```
<group name="local,sshd, authentication_failed">
  <rule id="5763" level="10" frequency="10" timeframe="60" overwrite="yes">
    <if_matched_sid>5760</if_matched_sid>
    <same_srcip />
    <description>Ben - Possible SSH brute force attack from IP: $(srcip)</description>
    <mitre>
      <id>T1110</id>
    </mitre>
  </rule>
</group>
```
This rule is set to fire when 10 SSH authentication failures occur from the same IP address within 60 seconds. It is built off of event's matching rule id 5760 (Wazuh's built in SSH authentication failure rule), and is mapped to Credential Access technique T1110: brute force. It overwrites the 5763 built in SSH brute force detection, with our own implementation

Now when we launch the same attack from the Kali box, I can see my alert fire:
<img width="2164" height="450" alt="image" src="https://github.com/user-attachments/assets/352715af-3de4-47ce-b829-63dd87e716f4" />
<img width="3448" height="1344" alt="image" src="https://github.com/user-attachments/assets/ce4f9968-7e99-4268-973e-3c07d9ee856d" />



### Analyst Investigation

If analyst an analyst were to receive this alert, they should triage by determining if they recognize the source IP the attempts came from. If they don't, they should confirm no attempts were successful and block the source IP from further inbound connections. If any authentication attempts were successful, the victim's device should be immediately disconnected from the internet and isolated for further investigation and containment.

### False Positives

Potential false positives that could cause an SSH brute force alert to fire are IT personnel running remote access scripts for monitoring, patching, etc
