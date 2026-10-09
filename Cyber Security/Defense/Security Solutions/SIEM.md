**SIEM (Security Information and Event Management system)** is a centralized security solution that fetches logs from different log sources and collect, normalize, and correlate these logs for SOC analysts to use in the operations center.
## Logs Everywhere
Devices within a network continuously generate logs of the activities that occur within them.
1. **Host-Centric Log Sources:** capture events that ocured within or related to the host. Device that generate host-centric logs include Windows, Linux, Servers, etc. Some log examples are:
	1. A user accessing a file
	2. A user attempting to authenticate
	3. A process execution activity
	4. A process adding/editing/deleting a registry key or value
	5. PowerShell execution
2. **Network-Centric Log Sources:** Network-related logs, such as firewalls, IDS/IPS, routers, etc. some log examples:
	1. SSH connection
	2. A file being accessed via FTP
	3. Web traffic
	4. A user accessing the company's resources through VPN
	5. Network file sharing activity
## Answer Nowhere
Some challenges with logs generated are as follows:
- Numerous Log Sources
- No Centralization
- Limited Context
- Limited Analysis
- Format Issues

## SIEM Features
- **Centealized Log Collection:** Logs from different devices are pulled thru lightweight agents or APIs and populated into the SIEM.
- **Normalization of Logs:** Broke down logs into different fields to be presented in one consistent format.
- **Correlation of Logs:** Correlates logs from different sources and finds any relationship between them.
- **Real-time Alerting:** Detects malicious activities based on rules. SIEMs comes with many rules by default, and can be added by analysts.
- **Dashboard and Reporting:** Presents data for analysis after being normalized and ingested. Some of the information includes: Alert highlights, system notification, health alert, failed login attempts, top domain visited, etc.
- And more...

## Log Sources
### Windows Machine
can be viewed from the Event Viewer.
### Linux Machine
- **/var/log/httpd:** Contains HTTP request & response, as well as error logs.
- **/var/log/cron:** Events related to cron jobs.
- **/var/log/auth.log and /var/log/secure:** Stores auth-related logs.
- **/var/log/kern:** Stores kernel-related events.
### Web Server
Depends on the server. For Apache's websites, common locations are **/var/log/apache** or **/var/log/httpd**.

## Log Ingestion
Some ways a SIEM can ingest logs:
- **Agent / Forwarder:** Uses lightweight tool called agent (forwarder in **Splunk**) that gets installed on the Endpoint. Configured to capture and send all important logs to the SIEM server.
- **Syslog:** Widely used protocol to collect data from various systems like web servers, databases, etc., and send real-time data to the centralized destination.
- **Manual Upload:** Some SIEM solutions, like Splunk, ELK, etc., allow users to ingest offline data for quick analysis.
- **Port-Forwarding:** Configured to listen on a certain port, and then the endpoints forward the data to the SIEM instance on the listening port.

## Detection Rules
Rules for SIEM to detect and catch threats. A few examples includes:
- If a user gets five failed Login Attempts in 10 seconds, raise an alert for `Multiple Failed Login Attempts`
- If login is successful after multiple failed login attempts, raise an alert for `Successful Login After multiple Login Attempts`
- A rule is set to alert every time a user plugs in a USB (Useful if USB is restricted as per the company policy)
- If outbound traffic is > 25 MB, raise an alert to potential data exfiltration Attempt (Usually, it depends on the company policy)
#### Detection Rule Creation
- **Use-Case 1:** Adversaries tend to remove the logs during the post-exploitation phase to remove their tracks. A unique Event ID **104** is logged every time a user tries to remove or clear event logs. To create a rule based on this activity, we can set the condition as follows:  **Rule:** If the Log source is WinEventLog **AND** EventID is **104** - Trigger an alert `Event Log Cleared`
- **Use-Case 2:** Adversaries use commands like `whoami` after the exploitation/privilege escalation phase. The following Fields will be helpful to include in the rule.
	- Log source: Identify the log source capturing the event logs
	- Event ID: Which Event ID is associated with Process Execution activity? In this case, Event ID 4688 will be helpful.
	- NewProcessName: Which process name will be helpful to include in the rule?
	- **Rule:** If Log Source is WinEventLog **AND** EventCode is **4688,** and NewProcessName contains **whoami,** then Trigger an ALERT `WHOAMI command Execution DETECTED`
## Alert Investigation
Depends on the alert, Analysts will need to perform actions accordingly.
- Alert is a False Positive. May or may not require tuning the rule to avoid similar false positives from occuring again.
- Alert is a True Positive. PPerform further investigation.
- Contact the asset owner to inquire about activity.
- Suspicious activity is confirmed. Isolate the infected host.
- Block the suspicious IP.
