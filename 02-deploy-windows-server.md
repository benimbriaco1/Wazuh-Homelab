## Objective: deploy windows server 2025 virtual machine

For my homelab setup, I plan to add a Windows Server to act as a domain controller. That way I can detect different AD attacks (Kerberoasting, DCSync, Pass-the-hash, etc), but also have a means of managing my endpoints and identities

### Windows Server 2025

The only reliable way I could find to download a windows server iso image was from: https://www.microsoft.com/en-US/evalcenter/download-windows-server-2025. This lease only lasts 180 days, but should be sufficient for my lab purposes.

However, in order for this virtual machine to function properly, we also have to install a VirtIO (virtual input output) driver iso to allow this VM to talk with my host machine's hardware. A guide for downloading can be found here: https://pve.proxmox.com/wiki/Windows_VirtIO_Drivers

Then you add this driver in here during setup:  
<img width="600" height="300" alt="image" src="https://github.com/user-attachments/assets/a13a34ff-2883-4cf6-b0c3-d8b72c451e5d" />

