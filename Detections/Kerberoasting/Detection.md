## Attack Description

In a normal Kerberos flow, a user is granted a Ticket Granting Ticket (TGT) from the Key Distribution Center (KDC). When a user wishes to access a specific service, they present their TGT to the Ticket Granting Service (TGS), which in response hands back a service ticket. The service ticket that is issued is encrypted with the password hash of the target service account, that way when the service ticket is presented to the service itself, it can use its own password hash to decrypt the service ticket and verify you are able to access the service.

Kerberoasting refers to Attackers abusing this functionality by, after initial access to an AD domain, requesting service tickets and then attempting to crack the associated password hashes offline. If successful, attackers uncover the plaintext password of a service account and may be able to login to that account. This means they can impersonate a service account and operate at the level of privilege granted to that account.

## Attack Scenario

To simulate this attack, I first created 3 accounts on my Windows Server 2025 VM that could be tied to Service Principal Names later, which are unique identifiers used to locate where a specific service runs and who runs it in a network. I created 3 different accounts with:
`New-ADUser -Name "<account_name>" -AccountPassword (ConvertTo-SecureString "<password>" -AsPlainText -Force) -Enabled $true`

This image shows the 3 new accounts that were made:
<img width="1474" height="502" alt="image" src="https://github.com/user-attachments/assets/cf85e2d3-043a-48c3-aa6a-07c5ce00d6e1" />

Then I used the `setspn` command to register the above accounts to be responsible for the corresponding service. For example: 
<img width="1956" height="288" alt="image" src="https://github.com/user-attachments/assets/57016ae9-4038-4f33-93a6-878199655212" />

What this does is use -A to add a new SPN, followed by the SPN itself in ServiceClass/Hostname:Port format. In this case the SPN is the MSSQLSvc service, running on Windows Server 2025 host, over port 1433 which is what MSSQL uses by default. Lastly it includes the account for which to configure the SPN. Then I repeated this process with slight variation for the other 2 test service accounts. This image shows the new SPNs created in my domain:
<img width="970" height="250" alt="image" src="https://github.com/user-attachments/assets/5603ce1a-a1d5-4d69-93c6-231402cf7596" />
  
Then I created a low privilege local user to prove this attack can be carried out with limited permissions. I did this via GUI but it can be done via CLI.        
<img width="872" height="58" alt="image" src="https://github.com/user-attachments/assets/41b0a46b-addb-40cb-ad95-bcc2b89000f8" />

Now on the attacking box, kali linux, I can see these same SPNs with the credentials of the local user created earlier
<img width="2056" height="580" alt="image" src="https://github.com/user-attachments/assets/14078117-7ccc-4952-98bd-77528a1e1be8" />

In order to simulate requesting tickets from these accounts, I used impacket-GetUserSPNs with the -request parameter to request tickets from all service accounts in the target AD domain. The main error I was encountering during this process was an unsupported encryption type error being thrown, which I fixed by specifying -no-rc4, so that impacket wouldn't try to request a service ticket encrypted with rc4. Windows was likely rejecting that request because rc4 has been deemed outdated and insecure. However, it is very important to note that a lot of kerberoasting attacks happen with the attacker requesting rc4 tickets  
<img width="2556" height="1542" alt="image" src="https://github.com/user-attachments/assets/e71954c6-9b16-468e-823e-da3e0467bcd5" />   

And because these service tickets are encrypted with the password of the service account, after obtaining the ticket an attacker can then try to obtain the plaintext passwords. To demonstrate this, I performed a dictionary attack with hashcat using a text file containing the password for the service accounts via `hashcat -m 19700 -a 0 /hash1.txt /test_password_list.txt` where hash1.txt was the output of impacket-GetUSerSPNs -request, and test_password_list was a text file containing the correct password.  
<img width="1754" height="1096" alt="image" src="https://github.com/user-attachments/assets/16f2fecc-f36e-45d6-9732-18eed774b0ea" />  
In a normal scenario, the attacker wouldn't know the password and would use a utility like hashcat alongside a password list containing potentially millions of entries.

## Detection
Testing caused multiple logs with Event ID 4769 - A Kerberos service ticket was requested - to populate.   
<img width="2448" height="1832" alt="image" src="https://github.com/user-attachments/assets/4f245672-3d21-450b-adaa-c052832b7f15" />   

Detecting this activity is a multiple step effort in Wazuh:  
1. Edit /var/ossec/ruleset/rules/0580-win-security_rules.xml rule ID 60106 to not include event ID 4769
Explanation: This rule is the generic Wazuh built in rule for Windows Logon Success, but by default it included event ID 4769. For my detection to work, I needed to stop this rule from getting those event IDs.

2. Add this detection to /var/ossec/etc/rules/local_rules.xml:  

```
<group name="windows, windows_security, kerberos">
  <rule id="100003" level="3">
    <if_sid>60103</if_sid>
    <field name="win.system.eventID">^4769$</field>
    <field name="win.eventdata.serviceName" type="pcre2" negate="yes">\$$|^krbtgt$</field>
    <options>no_full_log</options>
    <description>Ben - Kerberos Service Ticket was requested</description>
  </rule>
</group>
```
   
Explanation: this rule matches off of Wazuh rule ID 60103, which is the generic Windows Audit Success event. It looks for event ID 4769, and uses the pcre2 library for regex which filters out machine accounts and the krbtgt account. Without excluding those, in an enterprise environment this would be unnecessarily noisy. The no_full_log option is used to reduce storage volume.  

3. Add this detection to /var/ossec/etc/rules/local_rules.xml:  

```
<group name="kerberos, windows, windows_security, authentication_success">
  <rule id="100004" level="10" frequency="3" timeframe="5">
    <if_matched_sid>100003</if_matched_sid>
    <description>Ben - Multiple 4769 requests within 5 seconds from: $(win.eventdata.targetUserName)</description>
    <different_field>win.eventdata.serviceName</different_field>
    <same_field>win.eventdata.targetUserName</same_field>
  </rule>
</group>
```    

Explanation: this rule matches off of the custom rule I built above, which is just service ticket requests for non machine or krbtgt accounts. It looks for at least 3 requests under 5 seconds, which is indicative of an attacker attempting to perform kerberoasting by requesting a high volume of service tickets in a short time, to give them different hashes to try and crack. The rule also ensures that 4769 requests were coming from the same user and for different services, in order to prevent false positives.

Now when I rerun the attack with impacket-GetUserSPNs -request, my alerts fire:  
<img width="3402" height="1136" alt="image" src="https://github.com/user-attachments/assets/4de17ece-9417-4d5c-94cf-09c41a4268b8" />

## Analyst Investigation  
If an analyst were to see this alert (rule ID 100004, 100003 is not suspicious on its own), they should immediately investigate the user who requested the service tickets, and whether or not the activity aligns with their job role. For instance, someone from the HR department is unlikely to request service tickets at such a rate, whereas a cloud engineer might be. Logon attempts for the requesting user should be monitored to see if there were any successful logons via unusual IP addresses, indicating account compromise. Logon attempts for the service accounts should also be monitored in case the attacker successfully cracks their password hash.

## False Positives  

Potential false positives include authorized admins/tools enumerating services or high privileged users who have access to many services within an organization

## Limitations  
The thresholds on this rule are tailored to my environment. In a more realistic enterprise environment, the thresholds would be adjusted to something more along the lines of 25 requests in 5 minutes for instance. A different method of detecting kerberoasting attacks is looking for 4769 event IDs where the user tries to request legacy encryption methods like RC4 which are easier to crack 
