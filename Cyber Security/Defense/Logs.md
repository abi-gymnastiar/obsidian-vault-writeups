## Use Cases
- **Security Events Monitoring:** Logs help detect anomalous behavior when real-time monitoring is used.
- **Incident Investigation and Forensics:** Log are the traces of every kind of activity. Offers detailed information on what happened during the incident. Can be utilized to perform root cause analysis of incidents.
- **Troubleshooting:** Logs record errors in systems or applications.
- **Performance Monitoring:** Logs can provide valuable insight into performance of an application.
- **Auditing and Compliance:** Logs can make it easier to establish a trail of different kind of activities.

## Types of Logs

| Log Type         | Usage                                                                                                                                  | Example                                                                                                                      |
| ---------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| System Logs      | OS Layer. Provide info on various OS's activities                                                                                      | System startup & shutdown, Driver loading events, system error events, hardware events.                                      |
| Security Logs    | Provide info on the security-related activities                                                                                        | Authentication & authorization events, security policy changes events, user account changes events, abnormal activity events |
| Application Logs | Any interactive or non-interactive activity happening inside the application.                                                          | User interaction events, application changes events, update events, error events.                                            |
| Audit Logs       | Detailed info on the system changes and user events. Helpful for compliance requirements and security monitoring.                      | Data access events, system change events, user activity events, policy enforcement events                                    |
| Network Logs     | Info on network's outgoing and incoming traffic. Important for troubleshooting network issues and handy during incident investigation. | Incoming & Outgoing network traffic events, network connection logs, network firewall logs.                                  |
| Access Logs      | Info about the access to different resources                                                                                           | Webserver access logs, database access logs, application access logs, API access logs                                        |
## Windows' System Logs
Stored in segregated log files, each with a specific log category. Some of the logs stored are Application, System, Security.
#### Event Viewer
A GUI tool inside windows to view and search for anything in the logs. To open, Start button -> type event viewer. Some important Event IDs in Windows OS:

| Event ID | Description                                        |
| -------- | -------------------------------------------------- |
| 4624     | A user account successfully logged in              |
| 4625     | A user account failed to login                     |
| 4634     | A user account successfully logged off             |
| 4720     | A user account was created                         |
| 4724     | An attempt was made to reset an account's password |
| 4722     | A user account was enabled                         |
| 4725     | A user account was disabled                        |
| 4726     | A user account was deleted                         |
## Web Server Access Logs Analysis
Some of the important fields are logged, like:
- IP Address
- Timestamp
- Request
	- HTTP Method
	- URL
- Status Code
- User-Agent
