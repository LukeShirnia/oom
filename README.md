# Out-Of-Memory Investigation .py


The following python script can be used to calculate the estimated RSS (RAM) value of each service at the time a kernel invoked OOM killer. At the time of an OOM incident, the system logs the estimated RSS value of each service in its system log, this script will calculate how much RAM the services were "theorietically" trying to use, the total RAM value of all services and how much RAM your system actually has to offer these services. 


The script looks in /var/log/messages or /var/log/syslog and takes the values recorded by the system just before the incident occurs. 






There are currently 2 usage methods. 


### Method 1
The first and most straight foward is simply running the command below:

```
curl -s https://raw.githubusercontent.com/LukeShirnia/out-of-memory-investigation.py/master/oom.py | python
```
### Method 2
You can also specify a specific log file if you are looking into historical data, or the logs have been rotated. 

Note: This WILL work with compressed log files!

Replace `/var/log/messages.1` with the log file you wish to analyse. 
```
curl -s https://raw.githubusercontent.com/LukeShirnia/out-of-memory-investigation.py/master/oom.py | python - /var/log/messages.1
```


The script currently works on the following OS:

..*  RedHat 6,7

..*  CentOS 5,6,7

***  Redhat/CentOS 5 - Only works on some devices, the system does not log the same and you need to specify python2.6 or 2.6 

..*  Ubuntu 14.04LTS

Testing on Ubuntu and Debian ongoing. 





###Example Output

```
----------------------------------------

      _____ _____ _____
     |     |     |     |
     |  |  |  |  | | | |
     |_____|_____|_|_|_|

     Out Of Memory Analyser

----------------------------------------
**************************************************
         !!!!OOM ISSUE!!!!
This device HAS run out of memory recently
**************************************************

----------------------------------------
Dates OOM occured:    Mar, 9, 10:58:55
Sytem RAM:            987 MB
Estimated RAM at OOM: 1509 MB
Services Killed:      mysqld, apache
----------------------------------------
Top 5 RAM Consumers at time of OOM:
----------------------------------------
Service: apache2               1370 MB
Service: mysqld                251 MB
Service: 4                     59 MB
Service: driveclient           12 MB
Service: fail2ban-server       10 MB
----------------------------------------


----------------------------------------
Dates OOM occured:    Mar, 9, 10:59:57
Sytem RAM:            987 MB
Estimated RAM at OOM: 1555 MB
Services Killed:      mysqld
----------------------------------------
Top 5 RAM Consumers at time of OOM:
----------------------------------------
Service: apache2               1160 MB
Service: mysqld                230 MB
Service: driveclient           16 MB
Service: sshd                  16 MB
Service: rackspace-monit       15 MB
----------------------------------------


----------------------------------------
Dates OOM occured:    Mar, 9, 11:37:32
Sytem RAM:            987 MB
Estimated RAM at OOM: 1369 MB
Services Killed:      mysqld
----------------------------------------
----------------------------------------
Top 5 RAM Consumers at time of OOM:
----------------------------------------
Service: apache2               1041 MB
Service: mysqld                197 MB
Service: sshd                  24 MB
Service: driveclient           16 MB
Service: rackspace-monit       15 MB
----------------------------------------


----------------------------------------
Dates OOM occured:    Mar, 9, 11:49:23
Sytem RAM:            987 MB
Estimated RAM at OOM: 1792 MB
Services Killed:      mysqld
----------------------------------------
----------------------------------------
Top 5 RAM Consumers at time of OOM:
----------------------------------------
Service: apache2               1480 MB
Service: mysqld                207 MB
Service: sshd                  10 MB
Service: fail2ban-server       9 MB
Service: polkitd               8 MB
----------------------------------------
```
