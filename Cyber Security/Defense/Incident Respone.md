## What is?

Incidents Response is a guideline that handles an incident from its start to end.
- Incidents: Events that turns out as a true positive security events.
- Alert: Triggered after an event or group of events point to a harmful activity.
- False Positive: Alerts that turns out to not be a harmful activities.
- True Positive: Alerts that the security solution correctly identifies as a harmful activities.

## Types of Incidents

- Malware Infections: A system that infected with malicious programs. Mostly caused by files that can be text, docs, execs, etc...
- Security Breaches: When an unaouthorized person gets access to condfidential data or systems.
- Data Leaks: When confidential information gets exposed to unauthorized entites. Can be from human errors or misconfigurations.
- Insider Attacks: Attacks from someone inside of the organization.
- Denial of Service Attacks (DoS): Incidents wwhere the attacker floods a system/network/application with false request, eventually making it unavaulable to legitimate users.

## Incident Response Process

### SANS
Has 6 phases, which can be called 'PICERL' to remember easily.

| Phase           | Explanation                                                                                                                                                                         | Example                                                                                                                            |
| --------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| Preperation     | Building necessary resources to handle an incident. Including developing Incidents Response Teams, proper incident response plan in place, and deploy neccesary security solutions. | Conducting awareness training for employees on phising emails.                                                                     |
| Identification  | Using security solutions and techniques to monitor abnormal events.                                                                                                                 | Security team notices huge amount of data being sent from one of the hosts -> finds the host was compromised from phising email.   |
| Containment     | Isolate problems to minimize the impact of an attack. Usually by isolating the victime's machine by disabling the user acc, etc.                                                    | Security team isolates host from the network to deny the attacker to jump to other systems.                                        |
| Eradication     | Removing threat from the attacked environment.                                                                                                                                      | A deep malware scan on a system to remove malicious software from the host.                                                        |
| Recovery        | Recovering the affected systems from backup or rebuilding them. After that the system still need to be tested again.                                                                | A compromised host was re-configured, and the exfiltrated data was restored from the backup.                                       |
| Lessons Learned | Gaps in the detection and analysis of an incident are identified and documented, helping to improve the overall process in future incidents.                                        | Conducting a post-incident review meeting to analyze the incident's root cause and improve the security to prevent future attacks. |
### NIST
Similar to SANS framework, but reduced the phases into four.
- Preperation
- Detection and Analysis
- Containment, Eradication, and Recovery
- Post-Incident Activity
![[Pasted image 20261009235159.png]]

## Incident Response Plan
Is the formal incident response document. This structured document underlines the approach during any incident. It is formally approved by senior management and consists of the procedures to be followed before, during, and after an incident has been completed.

Key components include (not limited to):
- Roles and Responsibilities
- Incident Response methodology
- Communication plan with stakeholders, including law enforcement
- Escalation path to be followed

## Incident Response Techniques
Monitoring abnormal events and identify incidents manually is hard. Some security solutions have the capability to respond to the incidents and execute the other phases of the lifecycle (containment, eradication, etc...).
- **SIEM (The Security Information and Event Management Solution):** collects all important logs in one centralized location and correlates them to identify incidents.
- **AV (AntiVirus):** Detects known malicious programs in a system and perform scans regularly.
- **EDR (Endpoint Detection and Response):** Protection for advance level threats. Deployed on every system and can also contain and eradicate the threat.
### Playbook
**Guidelines** for a comprehensive incident response. These are procedures that must be followed, including investigating the extent of the attack, taking necessary actions to prevent further damage and eliminate it from the root. May be different for different kinds of incidents.
Example for a Phising Email Playbook:
1. Notify all the stakeholders of the phising email incident
2. Determine if the email was malicious by conducting header and body analysis of the email
3. Look for any attachments with the email and analyze them
4. Determine if anybody opened the attachments
5. Isolate the infected systems from the network
6. Block the email sender
### Rulebooks
**Execution Steps**.  Detailed, step-by-step execution of specific steps during different incidents. My vary depending on the resources available for investigation.

