## Attack Scenario
On Linux systems, there is a daemon known as cron which is used to run periodic scheduled tasks known as cron jobs. Crontabs, short for cron tables, are the files where cron jobs are stored. Attackers who have write access to a system's crontab files can abuse their functionality by inserting their own cron jobs or modifying existing ones. This attack allows attackers to accomplish a variety of goals, such as persistence/execution (routinely executing an attacker's payload, C2 callbacks, reverse shells etc) and privilege escalation (modifying cron tabs which are executed by high privileged accounts)

**Crontab files:**   
`/var/spool/cron/`       
`/var/spool/cron/crontabs/`     
`/etc/crontab`  
`/etc/cron.d`  
`/etc/cron.hourly/`  
`/etc/cron.daily/`  

## Simulated Attack

## Detection

## Analyst Investigation

## Limitations
