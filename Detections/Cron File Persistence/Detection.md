## Attack Scenario
On Linux systems, there is a daemon known as cron which is used to run periodic scheduled tasks known as cron jobs. Crontabs, short for cron tables, are the files where cron jobs are stored. Attackers who have write access to a system's crontab files can abuse their functionality by inserting their own cron jobs or modifying existing ones. This attack allows attackers to accomplish a variety of goals, such as persistence/execution (routinely executing an attacker's payload, C2 callbacks, reverse shells etc) and privilege escalation (modifying cron tabs which are executed by high privileged accounts)

**Crontab files (for Ubuntu):**   
`/var/spool/cron/crontabs/<username>`    - contains user-specific crontab file (managed via crontab -e)  
`/etc/crontab`                           - contains system-wide crontabs, running under specific user accounts  
`/etc/cron.d/`                           - similar to /etc/crontab, this is a directory where can you can create separate system-wide config files which run under a certain user  
`/etc/cron.hourly/`                      - system-wide cronjobs executed hourly
`/etc/cron.daily/`                       - system-wide cronjobs executed daily (there is also weekly and monthly)

## Simulated Attack

## Detection

## Analyst Investigation

## Limitations
