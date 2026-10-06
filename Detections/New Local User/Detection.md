## Attack Scenario

After establishing initial access, attacker's may create a new local user on a compromised host with commands like `New-LocalUser <Username>`. A new local user can serve as a persistence mechanism, giving attacker's renewed access to a system via credentials they set themselves. A common flow is something like Initial Access > Privilege Escalation > New Local User Created > Persistence Established
## Simulated Attack

To demonstrate an attacker using this method to establish persistence, I opened an administrative PowerShell window on the Windows 11 Virtual Machine. This is assuming an attacker has established initial access and escalated their privileges and now has access to an elevated shell. Adding a new local user is as simple as:  
<img width="646" height="332" alt="image" src="https://github.com/user-attachments/assets/c43556ee-6207-4bcb-b9a1-d16b683896e8" />

Now there is another user that can logon to the Windows machine.  
<img width="2322" height="1114" alt="image" src="https://github.com/user-attachments/assets/572a93ff-b756-4d97-8b5c-69cbcc94e394" />

## Detection

Wazuh now shows telemetry regarding a 4720 Windows Event ID, which is "user account was created". The data.win.eventdata.targetUserName shows the "hacker" username, highlighted in yellow

<img width="3394" height="1156" alt="image" src="https://github.com/user-attachments/assets/6fd38c37-96a3-4131-a5e7-3c04bf7d7134" />

To add my own detection rule, I appended this logic onto the /var/ossec/etc/rules/local_rules.xml file:

```
<group name="windows, windows_security, adduser">
  <rule id="100101" level="8">
    <field name="win.system.eventID">^4720$</field>
    <description>Ben - New user created: $(win.eventdata.targetUserName) by: $(win.eventdata.subjectUserName)</description>
    <mitre>
      <id>T1136</id>
    </mitre>
  </rule>
</group>
```
It searches for Windows Event IDs of 4720, and creates a description of what happened. This attack is mapped to T1136: Create Account. Specifically, sub-technique T1136.001: Local Account

Now when an user is added on the Windows 11 host:
<img width="912" height="428" alt="image" src="https://github.com/user-attachments/assets/6b5c5351-0390-4c32-a834-1c357c8e317a" />
The rule catches this activity and notes in the description the acting user:
<img width="3444" height="1260" alt="image" src="https://github.com/user-attachments/assets/aedf1cb3-8f87-4016-b4f9-5fa3459c2d20" />

Something important I learned when creating this detection: in Wazuh, when you look at alert details some of them have the data. prefix before them. ex: data.win.eventdata.targetUserName. However, when you want to filter off this field in the rule, you omit the "data." prefix.

## Analyst Investigation

If an analyst were to receive this alert, they should first identify whether the created user is expected, and/or if it was created by an expected user (ex: IT admin). If the new user created seems suspicious, this should be escalated and the account should be promptly contained and deleted, with commands like `Remove-LocalUser <username>`. The creating account should also be investigated to ensure it has not been compromised and used to establish persistence for the attacker. 

## False Positives

False positives include new hire onboarding and creation of legitimate accounts for testing/experimentation

## Limitations

A limitation of this detection is that it could potentially generate a lot of false positives, especially if it is used in an organization with lots of employees. In addition, it could be further improved by adding correlation of a privileged group addition.
