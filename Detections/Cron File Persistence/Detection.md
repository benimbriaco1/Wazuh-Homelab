## Attack Scenario
On Linux systems, there is a daemon known as cron which is used to run periodic scheduled tasks known as cron jobs. Crontabs, short for cron tables, are the files where cron jobs are stored. Attackers who have write access to a system's crontab files can abuse their functionality by inserting their own cron jobs or modifying existing ones. This attack allows attackers to accomplish a variety of goals, such as persistence/execution (routinely executing an attacker's payload, C2 callbacks, reverse shells etc) and privilege escalation (modifying cron tabs which are executed by high privileged accounts)

**Crontab files (for Ubuntu):**   
`/var/spool/cron/crontabs/<username>`    - contains user-specific crontab file (managed via crontab -e)  
`/etc/crontab`                           - contains system-wide crontabs, running under specific user accounts  
`/etc/cron.d/`                           - similar to /etc/crontab, this is a directory where can you can create separate system-wide config files which run under a certain user  
`/etc/cron.hourly/`                      - system-wide cronjobs executed hourly  
`/etc/cron.daily/`                       - system-wide cronjobs executed daily (there is also weekly and monthly)  

## Simulated Attack  
This attack scenario is loosely based off of https://attack.mitre.org/campaigns/C0064/. I configured my Linux host to only accept public key ssh connection attempts by editing /etc/ssh/sshd_config. I get the resulting error when I attempt to ssh from my kali VM to the linux client VM:  
<img width="786" height="180" alt="image" src="https://github.com/user-attachments/assets/872ffcfb-d49b-47bf-8e86-99778454d000" />

For this scenario, an attacker goes after the cronjobs for 'linuxuser' in /var/spool/cron/crontabs/linuxuser. An example of a benign crontab file for the linuxuser would be something like  
<img width="418" height="324" alt="image" src="https://github.com/user-attachments/assets/9b27b6a6-6f4f-451c-b8af-75de5455f5be" />  

Which does:  
<img width="714" height="82" alt="image" src="https://github.com/user-attachments/assets/51d1476f-3416-4e30-b76b-fec7ba70ee76" />

An example malicious script would be:  
<img width="1650" height="136" alt="image" src="https://github.com/user-attachments/assets/7f2aee3f-8e38-4491-9dba-3e3ee14338e1" />  

This script lists all existing cronjobs for the user with -l (and suppresses error output) to standard output, then it prints the new malicious cronjob to standard output as well, and the combination of both is passed as standard input to 'crontab -' to create the new the crontab file for the user. 'crontab -' overwrites the existing crontab file for the current user completely, so using crontab -l before adding the new cronjob allowed me to just append the new cronjob and raise less suspicion. The resulting cronjob file looks like:   
<img width="1592" height="314" alt="image" src="https://github.com/user-attachments/assets/01843e0f-215f-4dc8-a210-3123cc2ed87e" />  

As for the actual cronjob itself, it runs every minute (denoted by 5 *) to add the public key of the Kali linux box into the target linux host's authorized_keys file:
<img width="1042" height="376" alt="image" src="https://github.com/user-attachments/assets/9c6688d6-858d-4990-8fb7-111cbbaada08" />  

Now when I try to ssh from the kali linux VM into the target linux VM, it succeeds with no password prompt:
<img width="864" height="428" alt="image" src="https://github.com/user-attachments/assets/60043a02-9a8a-4bbe-9ac2-64aac540cb4e" />

## Detection  
To detect this activity, I set up FIM


## Analyst Investigation

## Limitations
