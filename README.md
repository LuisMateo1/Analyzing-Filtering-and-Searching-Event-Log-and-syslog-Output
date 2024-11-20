# Analyzing Filtering and Searching Event Log and syslog Output
<h3>Objectives</h3>

- Analyze data as part of security monitoring activities
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

