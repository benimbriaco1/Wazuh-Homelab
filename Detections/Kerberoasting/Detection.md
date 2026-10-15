## Attack Description

In a normal Kerberos flow, a user is granted a Ticket Granting Ticket (TGT) from the Key Distribution Center (KDC). When a user wishes to access a specific service, they present their TGT to the Ticket Granting Service (TGS), which in response hands back a service ticket. The service ticket that is issued is encrypted with the password hash of the target service account, that way when the service ticket is presented to the service itself, it can use its own password hash to decrypt the service ticket and verify you are able to access the service.

Kerberoasting refers to Attackers abusing this functionality by, after initial access to an AD domain, requesting service tickets and then attempting to crack the associated password hashes offline. If successful, attackers uncover the plaintext password of a service account and may be able to login to that account. This means they can impersonate a service account and operate at the level of privilege granted to that account.

## Attack Scenario

To simulate this attack, I first created 3 accounts that could be tied to Service Principal Names later, which are unique identifiers used to locate where a specific service runs and who runs it in a network. I created 3 different accounts with:
`New-ADUser -Name "<account_name>" -AccountPassword (ConvertTo-SecureString "<password>" -AsPlainText -Force) -Enabled $true`

This image shows the 3 new accounts that were made:
<img width="1474" height="502" alt="image" src="https://github.com/user-attachments/assets/cf85e2d3-043a-48c3-aa6a-07c5ce00d6e1" />

Then I used the `setspn` command to register the above accounts to be responsible for the corresponding service. For example: 
<img width="1956" height="288" alt="image" src="https://github.com/user-attachments/assets/57016ae9-4038-4f33-93a6-878199655212" />
What this does is use -A to add a new SPN, followed by the SPN itself in ServiceClass/Hostname:Port format. In this case the SPN is the MSSQLSvc service, running on Windows Server 2025 host, over port 1433 which is what MSSQL uses by default. Lastly it includes the account for which to configure the SPN. Then I repeated this process with slight variation for the other 2 test servie accounts.


## Detection

## Analyst Investigation

## Limitations
