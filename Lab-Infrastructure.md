# Lab Diagram:

<img width="1238" height="1144" alt="image" src="https://github.com/user-attachments/assets/c1ff89cf-cd06-45a3-9508-01d847e4ffdb" />

## Overview

### ⚪️ Host Machine 
This computer does none of the hosting. It is the computer from which I log into my Proxmox Server (accessed via web browser over port 8006) and conduct all of the management from.

### ⚫️ Proxmox Server
This is a repurposed old HP Envy laptop. Windows OS has been wiped from it and replaced with Proxmox as a type 1 hypervisor. All the virtual machines created in this lab are using the physical hardware of the proxmox server. 

### 🔵 Ubuntu 26.04 Server VM
This is a console based virtual machine which I am using to run the Wazuh Server, Dashboard, and Indexer. See [01-wazuh-install.md](01-wazuh-install.md) for definitions of these Wazuh features. 
)
