# Objective: Deploy Linux Virtual Machine

To finish my setup before deploying Wazuh, I am going to add the last virtual machine I need. This VM will help simulate linux-based attacks and subsequent detections

### Installation steps:

### 1. Download ISO file and upload to Proxmox
Grabbed the download from: https://ubuntu.com/download/desktop 

### 2. Set Static IP 
I did this via GUI but can also do via cmdline

### 3. Verify connectivity
<img width="1380" height="882" alt="image" src="https://github.com/user-attachments/assets/4db57b24-9a46-4d53-856b-c49310ec8195" />

### 4. Connect VM to domain
This proved to be even more troublesome that joining the Windows 11 Client to the domain. Performing `nslookup homelab.local` failed time and time again despite adjustments. Joining via the DC Administrator account using adcli or realm resulted in various errors: nsufficient permissions, server not found in kerberos database, etc. 

✅ The fix: installing kinit via `sudo apt install krb5-user -y` and then attempting the join again via `sudo adcli join homelab.local -U Administrator`

Now I can see the Linux Client in Active Directory Users and Computers tool on the DC:
<img width="1474" height="692" alt="image" src="https://github.com/user-attachments/assets/c705473b-a221-4ee9-85f7-21ea824c94ae" />

### 📋 Next Steps
The next step is one of the most important ones - installing the wazuh agent on all of my virtual machines to complete my setup.

