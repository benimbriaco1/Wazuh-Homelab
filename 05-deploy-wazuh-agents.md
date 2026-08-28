# Objective: Deploy Wazuh Agent to all devices in homelab

Agents are small pieces of software installed on endpoints which can collect and forward security data to a SIEM or central location for monitoring and analysis. In order for my Wazuh server to have appropriate visibility into the health and security of my endpoints, I need to install the Wazuh agent on each host.

### Installation steps:

### 1. Install agent onto Windows Server 2025 and Windows 11 Virtual Machines

❌ blocker: pasting into windows virtual machine   
✅ solution: similar to how I used ssh to paste commands into my Linux VMs, I used RDP to remote into my Windows virtual machines from my host machine

This is what it looks like to add the wazuh agent:
<img width="2230" height="336" alt="image" src="https://github.com/user-attachments/assets/1c9ecd5b-c87a-47c3-9710-3d434445c326" />

After adding the agents to the Windows host, the agents agents are visible from the Wazuh dashboard:
<img width="2648" height="1374" alt="image" src="https://github.com/user-attachments/assets/1aefeeba-9934-43f3-bbf5-ae1846073b8d" />

### 2. Install agent onto Linux VM
For the Windows hosts, all that was needed  to deploy the Wazuh agent was to run 2 commands in an admin Powershell window. For the linux client, I had to follow a few more steps as outlined in: https://documentation.wazuh.com/current/installation-guide/wazuh-agent/wazuh-agent-package-linux.html 

<img width="2636" height="1266" alt="image" src="https://github.com/user-attachments/assets/4cd08da2-4757-483d-ac8d-97505cab643b" />

### 📋 Next Steps

Now that all of our agents have been to deployed to all our hosts, the next step is to start writing detections
