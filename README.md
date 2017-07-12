# Out-Of-Memory Investigation .py


The following python script can be used to calculate the estimated RSS (RAM) value of each service at the time a kernel invoked OOM killer. At the time of an OOM incident, the system logs the estimated RSS value of each service in its system log, this script will calculate how much RAM the services were "theorietically" trying to use, the total RAM value of all services and how much RAM your system actually has to offer these services. 


The script looks in `/var/log/messages` or `/var/log/syslog` and takes the values recorded by the system just before the incident occurs. 




<br />

There are currently 3 usage methods. 
<br />

<br/>

### Method 1/2 - preferred method
These 2 method are probably the "Safest" method. You can either gitclone or wget/curl the script locally and then run using python command.

<br />

#### Part A
If no argument is parsed, it will default to using the system log.
<br />
Example:

```
wget https://raw.githubusercontent.com/LukeShirnia/out-of-memory/master/oom-investigate.py; python oom-investigate.py
```
or
```
git clone https://github.com/LukeShirnia/out-of-memory.git; cd out-of-memory; python oom-investigate.py
```
<br />

#### Part B
If you wish to specify an old rotated or gziped system log, run `python oom.py /var/log/<oldlog>`, replacing `<oldlog>` with the file:
<br />
Note: This WILL work with compressed log files!
<br />
Example:
```
wget https://raw.githubusercontent.com/LukeShirnia/out-of-memory/master/oom-investigate.py; python oom-investigate.py /var/log/<old_rotated_file>
```
or 
```
git clone https://github.com/LukeShirnia/out-of-memory.git; cd out-of-memory; python oom-investigate.py /var/log/<old_rotated_file>
```
<br />

<br />

### Method 3

#### Part A
Method 2 use curl to pipe into python, which as most system administrators will tell you is not always a safe method. This will use current system log file:
<br />
Example
```
curl -s https://raw.githubusercontent.com/LukeShirnia/out-of-memory/master/oom-investigate.py | python
```
#### Part B
You can also specify a different log file if you are looking into historical data (such as compressed or logs have been rotated):

Note: This WILL work with compressed log files!

Replace `/var/log/messages.1` with the log file you wish to analyse. 
```
curl -s https://raw.githubusercontent.com/LukeShirnia/out-of-memory/master/oom-investigate.py | python - /var/log/messages.1
```
<br />

<br />

## NOTE:

The script currently works on the following OS:

*  RHEL/CentOS 6,7

*  Ubuntu 14.04LTS/16.04LTS

*  Redhat/CentOS 5 - Only works on some devices,AND you need to specify python2.6 or 2.6 
<br />

*** Testing on Ubuntu and Debian ongoing.  ***


<br />

<br />

<br />

## Script Breakdown:

The ouput from this script can be broken down into 4 main sections:

<br />

### Section 1 - Log File Information
This section is a quick overview of the log file used for reference. 
<br />
Example:

```
Log Information
Log File  :  /var/log/messages 
Start date:  Jun, 20, 06:46:37 
End Date  :  Jun, 21, 08:32:24 
```

<br />

### Section 2 - Total Services Killed 
During out-of-memory investigations its not always obvious what service(s) have been killed, especially when most of the entries in the system log shows httpd/apache. This output allows you to quickly discover if a backup agent or mysql was killed at some point in the start/end date of the log file.
<br />
Example:

```
Number of OOM occurances in log file:  x

Service apache       Killed x time(s)
Service perl	     Killed x time(s)
Service php          Killed x time(s)
Service mysqld       Killed x time(s)
Service sh           Killed x time(s)
```

<br />

### Section 3 - Date of OOM Issues
This helps narrow down problamatic times such as; peak traffic times, backup times etc
<br />
Example:

```
KEY
D = Date(s) OOM
H = Hour OOM Occured
O = Number of Occurences in Date/Hour

D      H  O
Jun 20 23 4
Jun 21 00 56
Jun 21 01 114
Jun 21 02 105
Jun 21 03 105
Jun 21 04 87
```

<br />

### Section 4 - Top 5 OOM Consumers
This section allows you to narrow down the cause of heaviest memory consumer. This gives you a good starting point to prevent the issue occuring again. 
<br />
Example:

```
 ----------------------------------------
Jun 20 00:00:00 
Sytem RAM:              x MB
Estimated RAM at OOM:   x MB
Services Killed:        php, apache 

Top 5 RAM Consumers at time of OOM:
Service: apache2               x MB 
Service: php5                  x MB 
Service: perl		       x MB 
Service: gunicorn              x MB 
Service: mysqld                x MB 

----------------------------------------
```


<br />

<br />

<br />


### Full Example Output
The following example shows the output of the script when run against a compressed log file in a "non standard" directory:

```
----------------------------------------
      _____ _____ _____ 
     |     |     |     |
     |  |  |  |  | | | |
     |_____|_____|_|_|_|
     Out Of Memory Analyser

Disclaimer:
If system OOMs too viciously, there may be nothing logged!
Do NOT take this script as FACT, investigate further
----------------------------------------

Log Information
Log File  :  oom_logs/syslog.large 
Start date:  Jun, 20, 06:46:37 
End Date  :  Jun, 21, 08:32:24 

######## OOM ISSUE ########

Number of OOM occurences  in log file:  471 

Service apache       Killed 278 time(s)
Service generate_report Killed 190 time(s)
Service php          Killed 1 time(s)
Service mysqld       Killed 1 time(s)
Service sh           Killed 1 time(s)

KEY
D = Date(s) OOM
H = Hour OOM Occured
O = Number of Occurences in Date/Hour

D      H  O
Jun 20 23 4
Jun 21 00 56
Jun 21 01 114
Jun 21 02 105
Jun 21 03 105
Jun 21 04 87


Note: Only Showing: 3 of the 471 occurences
Showing the 1st, 2nd and last
----------------------------------------
Jun 20 23:46:09 
Sytem RAM:              987 MB
Estimated RAM at OOM:   8575 MB
Services Killed:        php 

Top 5 RAM Consumers at time of OOM:
Service: apache2               6382 MB 
Service: php5                  1056 MB 
Service: generate_report       881 MB 
Service: gunicorn              101 MB 
Service: mysqld                49 MB 

----------------------------------------
Jun 20 23:56:27 
Sytem RAM:              987 MB
Estimated RAM at OOM:   8569 MB
Services Killed:        apache 

Top 5 RAM Consumers at time of OOM:
Service: apache2               7235 MB 
Service: generate_report       1074 MB 
Service: gunicorn              101 MB 
Service: mysqld                49 MB 
Service: cron                  26 MB 

----------------------------------------
Jun 21 04:47:35 
Sytem RAM:              987 MB
Estimated RAM at OOM:   8491 MB
Services Killed:        generate_report 

Top 5 RAM Consumers at time of OOM:
Service: generate_report       4184 MB 
Service: apache2               4068 MB 
Service: gunicorn              84 MB 
Service: cron                  58 MB 
Service: sh                    18 MB 

----------------------------------------

```
<br />

<br />

### Example - No OOM in log file
This example shows the output when NO oom has occured in the log file. NO options were passed with the running of this script (Method 1 was used) 

```
----------------------------------------
      _____ _____ _____ 
     |     |     |     |
     |  |  |  |  | | | |
     |_____|_____|_|_|_|
     Out Of Memory Analyser

Disclaimer:
If system OOMs too viciously, there may be nothing logged!
Do NOT take this script as FACT, investigate further
----------------------------------------

Log Information
Log File  :  /var/log/messages 
Start date:  May, 7, 08:08:18 
End Date  :  May, 8, 14:30:01 


----------------------------------------
OOM has NOT occured in specified log file!
----------------------------------------

Other Logs worth checking:
/var/log/messages-20170416 - Occurences: 1
/var/log/messages-20170424 - Occurences: 0
/var/log/messages-20170430 - Occurences: 5
/var/log/messages-20170507 - Occurences: 0

----------------------------------------
```
