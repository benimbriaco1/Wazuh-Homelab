# Objective: Deploy Windows 11 Endpoint to Proxmox 

A Windows 11 endpoint in my lab can help represent a standard workstation in an enterprise environment, and thus provides use for detecting common attack vectors against Windows 11 machines. 

### Installation Steps:

### 1. Download ISO file and upload to Proxmox
This also requires the use of the VirtIO driver 

### 2. Setup Windows via GUI
This takes a long time to complete. The important part is rather than signing in with a MS account, I selected "Domain join instead"

### 3. Set Static IP

### 4. Verify connectivity
I ensured my network connectivity is working by reaching my DC with ping
<img width="900" height="600" alt="image" src="https://github.com/user-attachments/assets/e24ef219-4d7f-4cca-ad9e-a33256824b67" />

### 5. Join computer to domain
This step should've been straight forward, instead it presented a troubleshooting opportunity. After following the above steps, my Windows client could ping the DC and public IPs like 8.8.8.8 however the client could not resolve the homelab.local domain when attempting to join the computer to the domain. Nslookup commands failed, and the DC couldn't ping the Windows client back.

❌ The problem: the Windows 11 firewall.  
✅ The fix: temporarily disabling the Windows 11 VM's public firewall profile  

This allowed the client to successfully join:
<img width="2522" height="1570" alt="image" src="https://github.com/user-attachments/assets/29f15ddb-be70-47b3-97da-0eaee2bb9faa" />

And I verified this success from the Windows server:
<img width="1484" height="1032" alt="image" src="https://github.com/user-attachments/assets/86ef484e-dd99-4625-9c2a-0f421f370f5d" />

### 📋 Next Steps
My next step is to deploy a linux virtual machine and finalize my infrastructure setp.

