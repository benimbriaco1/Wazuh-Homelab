# Objective: Deploy Kali Linux VM to Proxmox

Kali linux offers a suite of pre-installed tools that can be used to carry out simulated attacks against my lab environment. This VM will not be joined to the Active Directory domain as that would be unrealistic. 

### Installation Steps

### 1. Download Kali Linux ISO and upload to Proxmox

https://www.kali.org/get-kali/#kali-platforms

### 2. Create Kali Linux Virtual Machine in Proxmox 
This is relatively straightforward

### 3. Set static IP and Networking Configuration
As with the other VMs, this VM was given a static IP as DHCP is not being utilized in my lab

### 4. Verify Connectivity  
The last step to setting up the Kali Linux VM is ensuring it can reach all other hosts in the lab

