# Analyzing Filtering and Searching Event Log and Syslog Output
<h3>Objectives</h3>

- Analyze data as part of security monitoring activities
#

**Security Information and Event Management System (SIEM)**

A SIEM is used to respond to incidents in real-time and also to perform threat hunting for incidents that might not have been detected. Both these use cases will depend on effective queries, filters, and visualizations so that analysts are presented with useful information and not overloaded by false-positive alerts.
#
<h3>Analyzing a SIEM Dashboard</h3>

While an SIEM is used to manage and categorize alerts and escalate or dismiss them as appropriate, an SIEM dashboard gives an overview of the current status of the network.
- The dashboard is meant to be easier to understand and can be used to develop status reports and present them to a non-technical audience.

The SIEM dashboard is called Squert, it's an extension of Squil which is the SIEM

![image](https://github.com/user-attachments/assets/d1202045-c877-4661-b885-a87140815e17)

After logging in these are the alerts on the dashboard, specifically from March 16th, 2020
- This kind of dashboard is designed to provide overall threat status reporting. The default view shows only queued events that have not yet been analyzed and categorized by an incident handler, such as a security analyst.

![image](https://github.com/user-attachments/assets/945b0018-1aa5-47ff-a8ba-5e88e5cd7cee)
- The alerts shown on the dashboard are from Snort, an Intrusion Detection System (IDS) as a result of sample packet captures that were replayed through the sensor. There are also some other alerts from the OSSEC agent installed on Security Onion.
- The SC and DC columns show the number of source and destination IPs involved for each event signature
- The activity column shows the number of events of that signature per hour

Now on the **Summary** tab, this shows information about which signatures, IP addresses, and ports are most active.\
- It also displays location information for each public IP and summarizes connections by source and destination IPs and countries.
![image](https://github.com/user-attachments/assets/c5134036-5726-4661-aa0c-320d11b38414)
![image](https://github.com/user-attachments/assets/e20467a1-182a-4b14-aa0f-80a61e4789ad)
![image](https://github.com/user-attachments/assets/5ada893b-d91d-42c9-b2eb-76b673de2bb4)
![image](https://github.com/user-attachments/assets/0bd72ffb-14d8-4025-a6f5-962994ff9d02)

Back on the **Events** tab, by clicking on the queue box (red box with #24) for this event, I can see more information about the event, such as the flag that triggered it, and the source and destination IPs
![image](https://github.com/user-attachments/assets/4b844e62-2415-44ec-9eea-b4e43705d259)

Clicking the second queue box will expand and show all 24 events that correspond to the alert on the dashboard.
![image](https://github.com/user-attachments/assets/5bdac9ea-0eb2-435d-a0a6-1bc017c2743d)

Clicking on the event ID of the first event (3.47) will open a new tab to view the packet capture of that event.
![image](https://github.com/user-attachments/assets/2cf5484f-b164-4e99-8e66-e30b74d599ab)
Investigating further I see that this is a GET request for a weirdly named named php file, with unknown functionality. Since this is an old threat, there may be more information in the threat database. If there isn't or if this were a newer threat 
with no information to be found in the DB, then this would be the moment to isolate the host and use a sandbox to try to determine the function of the code.

Back on Squert, I can look up more information on the source IP by clicking it and choosing one of the many IP lookup options. In this case, I will look up the address with Kibana.
- Setting a time range to search for information from the last 5 years, yields the following results
- I can see how many alerts the IP is associated with and how many times it appears as a source and destination address.

![image](https://github.com/user-attachments/assets/700f0ba4-e216-4302-9fee-ba98a7d76f94)
![image](https://github.com/user-attachments/assets/249e5495-2b72-4ffa-a744-a08a7a20c47e)
#
<h3>Manual Log Analysis Using grep</h3>

In a terminal window, I input 'cd /var/log && ls' which changes the directory to the logging directory, /var/log, and then lists its contents
![image](https://github.com/user-attachments/assets/a6fe22ec-c876-4812-96cb-5061aa54344b)

- /var/log is the primary log folder for Linux, some of these log files are processed by Syslog, and others are written by the applications themselves, in subdirectories. For example, an Apache web server could write its logs to /var/log/apache2 or /var/log/httpd.

**Using grep**

grep can be used to search directories and files:

Here I used grep to search the Syslog subdirectory, for the word/string 'root'. Looking at these logs most were created by the CRON service.
![image](https://github.com/user-attachments/assets/e2dc8760-c0a9-43b0-9ab2-42503991357d)

- Doing 'sudo grep “root” syslog*' will search for the word 'root' for all files that start with 'syslog', like syslog.1 which is a backup of syslog. syslog.2.gz gzips older logs, so those won't be read.

Doing 'sudo grep -i “error” syslog*' will search for instances of the word 'error' the -i flag makes it case insensitive, so that it will return the word error no matter how it was written.
![image](https://github.com/user-attachments/assets/5d8b440b-8f8e-4fe7-9c11-01df2ece024c)

- If wanted to exclude a string I would use the -v flag:  'grep -v “error” syslog' this search will exclude any instances of the word 'error'.
![image](https://github.com/user-attachments/assets/db126937-c319-49d2-b013-73beb1a6405a)
- However, the string 'Error' would be in the results because 'error' and 'Error' are two different strings
![image](https://github.com/user-attachments/assets/25da652c-6294-4b85-9e15-19f817b9fba5)

- To exclude both 'error' and 'Error' I could combine the flags: -iv and now they both will not appear in the output
![image](https://github.com/user-attachments/assets/f370d63f-ce77-4d4f-9353-02f8557f7523)
#
<h3>Formating Queries</h3>

Searching through multiple log files or a lot of results can be time time-consuming and make things harder. I can use commands to search logs faster.

**Cut Command**
- cut -c1-32 syslog
  - This command displays the first 32 characters of each log item in the file. In this case, it allows me to see the date and time, the process name, and the process ID of the log item.

![image](https://github.com/user-attachments/assets/d4bc1b01-7595-40bb-ac53-32d200b93312)

- cut -c31- syslog
 - This command displays from character 31 to the end of the line. Depending on what I may be searching for I would rather look at the output of the log item.
![image](https://github.com/user-attachments/assets/86d66907-8309-4914-b521-822f5e5c1323)

**Cat Command**

The cat command can be used to read/view file contents, create and write to a file, and more. In this case, I'm using to output of the contents of the file auth.log to the command line.
- cat auth.log
![image](https://github.com/user-attachments/assets/135da565-6538-47fc-a089-e3a77df858cf)

I can use the 'less' command to view the contents but not output the command line, this may be necessary if the file is too big and all the lines won't output to the command line. Or if I want to keep the command line clean.
- cat auth.log | less (click 'q; or 'ctrl + z' to go back to the command line)
![image](https://github.com/user-attachments/assets/11c58238-0b08-4e19-9d0b-a031097f9cfe)
![image](https://github.com/user-attachments/assets/2b008742-1e63-4284-8bef-1fbc8e93e9e2)

**Head and Tail Command**
The Head and Tail commands are used to view the beginning or end of the file contents respectively. 
- Head -2 auth.log
  - Displays the first two lines of the auth.log file
  - By default it's the first 10 lines
![image](https://github.com/user-attachments/assets/5bd5aceb-b9b7-4350-a4b8-7e0613899982)
- Tail -2 auth.log
  - Displays the last 2 lines of the auth.log file
  - By default it's the last 10 lines
![image](https://github.com/user-attachments/assets/8d57a2e9-6c6c-4251-b2e7-c626773ccdd5)
#

**Summary: A SIEM is a centralized log repository, with the ability to correlate logs to allow for faster detection, analysis, and response to security events and potential threats. The dashboard provides a GUI to take advantage of the SIEMs features in an easier-to-understand format, allowing response to be quicker. The dashboard allows an incident responder to more quickly and effectively evaluate possible threats to the network allowing them to remediate any potential impact sooner.**



