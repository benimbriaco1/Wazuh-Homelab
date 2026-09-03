# Lab Diagram:

<img width="1238" height="1144" alt="image" src="https://github.com/user-attachments/assets/c1ff89cf-cd06-45a3-9508-01d847e4ffdb" />

## Overview

### ⚪️ Host Machine 
This computer does none of the hosting. It is the computer from which I log into my Proxmox Server (accessed via web browser over port 8006) and conduct all of the management from.

### ⚫️ Proxmox Server
This is a repurposed old HP Envy laptop. Windows OS has been wiped from it and replaced with Proxmox as a type 1 hypervisor. All the virtual machines created in this lab are being hosted here, using the physical hardware of the Proxmox server. 

### 🔴 Ubuntu 26.04 Server VM
This is a console based virtual machine which I am using to run the Wazuh Server, Dashboard, and Indexer. See [01-wazuh-install.md](01-wazuh-install.md) for definitions of these Wazuh features. 
)

### 🔴 Ubuntu 26.04 Desktop VM
This is an Ubuntu VM with a GUI, representing a linux endpoint which can be attacked.

### 🔵 Windows Server 2025 VM
This virtual machine is hosting Active Directory, which has the Windows 11 and Ubuntu 26.04 Desktop VMs as part of the AD domain. AD provides different attack surfaces which can be used to build out detections

### 🔵 Windows 11 VM
This virtual machine represents a standard enterprise machine, which can gives us a very realistic set of attacks to simulate.

### 🟣 Kali Linux VM
Kali Linux is a linux distro designed for and recognized for it's red teaming and penetration testing capabilities. Because of this, I spun up this VM to simulate attacks in the virtualized network. 
