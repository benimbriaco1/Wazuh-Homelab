## Objective: Deploy Wazuh Server onto Ubuntu Server

Proxmox VE was deployed as the foundation of my cybersecurity homelab environment. The goal of this hypervisor is to host multiple virtual machines used for security monitoring, 
endpoint simulation, and detection engineering. After installation and deployment of an Ubuntu 26.04 LTS virtual machine, the next step is to install Wazuh Server.

### Installation steps:

### 1. Edit /etc/netplan/00-installer-config.yaml file to set a static IP Address to the Ubuntu VM
   
This is important in order to ensure reliable network connections and consistency

### 2. Follow installation steps according to https://documentation.wazuh.com/current/quickstart.html

One issue I encountered was not being able to paste commands inside the virtual machine. Resolution was to SSH into the VM from my host machine and paste installation commands that way.

For the sake of my homelab, I am downloading the Wazuh server, indexer, and dashboard all on one host. Their functions are as follows:

**Wazuh Server:** Processes security data from endpoints, applies detection rules, and generates security alerts.    
**Wazuh Dashboard:** Provides a web-based interface for viewing alerts, logs, dashboards, and security analysis.    
**Wazuh Indexer:** Stores and indexes collected security data so it can be quickly searched and analyzed.    

### 3. Access the Wazuh web interface

After successful installation, I was able to navigate to the web interface via https://\<wazuh-dashboard-ip>\:443 and the credentials supplied during installation

<img width="3416" height="2070" alt="image" src="https://github.com/user-attachments/assets/7fe07286-7a14-46ad-9c98-52c879228365" />
