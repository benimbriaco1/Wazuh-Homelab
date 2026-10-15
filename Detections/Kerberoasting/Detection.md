## Attack Description

In a normal Kerberos flow, a user is granted a Ticket Granting Ticket (TGT) from the Key Distribution Center (KDC). When a user wishes to access a specific service, they present their TGT to the Ticket Granting Service (TGS), which in response hands back a service ticket. The service ticket that is issued is encrypted with the password hash of the target service account, that way when the service ticket is presented to the service itself, it can use its own password hash to decrypt the service ticket and verify you are able to access the service.

Kerberoasting refers to Attackers abusing this functionality by, after initial access to an AD domain, requesting service tickets and then attempting to crack the associated password hashes offline. If successful, attackers uncover the plaintext password of a service account and may be able to login to that account. This means they can impersonate a service account and operate at the level of privilege granted to that account.

## Attack Scenario



## Detection

## Analyst Investigation

## Limitations
