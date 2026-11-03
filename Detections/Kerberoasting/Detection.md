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

In order to simulate requesting tickets from these accounts, I used impacket-GetUserSPNs with the -request parameter to request tickets from all service accounts in the target AD domain. The main error I was encountering during this process was an unsupported encryption type error being thrown, which I fixed by specifying -no-rc4, so that impacket wouldn't try to request a service ticket encrypted with rc4. Windows was likely rejecting that request because rc4 has been deemd outdated and insecure. 
<img width="2556" height="1542" alt="image" src="https://github.com/user-attachments/assets/e71954c6-9b16-468e-823e-da3e0467bcd5" />   

And because these service tickets are encrypted with the password of the service account, after obtaining the ticket an attacker can then try to obtain the plaintext passwords. To demonstrate this, I performed a dictionary attack with hashcat using a text file containing the password for the service accounts via `hashcat -m 19700 -a 0 /hash1.txt /test_password_list.txt` where hash1.txt was the output of impacket-GetUSerSPNs -request, and test_password_list was a text file containing the correct password.  
<img width="1754" height="1096" alt="image" src="https://github.com/user-attachments/assets/16f2fecc-f36e-45d6-9732-18eed774b0ea" />  
In a normal scenario, the attacker wouldn't know the password and would use a utility like hashcat alongside a password list containing potentially millions of entries.

## Detection

## Analyst Investigation

## Limitations
