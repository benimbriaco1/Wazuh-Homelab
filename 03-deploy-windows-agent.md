# Objective: Deploy Windows 11 Endpoint to Proxmox 

A Windows 11 endpoint in my lab can help represent a standard workstation in an enterprise environment, and thus provides use for detecting common attack vectors against Windows 11 machines. 

### Installation Steps:

### 1. Download ISO file and upload to Proxmox
This also requires the use of the VirtIO driver 

### 2. Setup Windows via GUI
This takes a long time to complete. The important part is rather than signing in with a MS account, I selected "Domain join instead"

### 3. Set Static IP

### 4. Verify connectivity
I ensured my setup is working by testing DNS and seeing if I could reach my DC via ping utility
<img width="900" height="600" alt="image" src="https://github.com/user-attachments/assets/e24ef219-4d7f-4cca-ad9e-a33256824b67" />

### 5. Join computer to domain


