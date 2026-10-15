## What is it?
is an Authorised security assessment that is performed on systems, applications, or networks. It is carried out with **explicit permission** from the system owner within agreed boundaries.

### Pentesting vs. Malicious Hacking
|Core Factor|Penetration Tester|Malicious Attacker|
|---|---|---|
|Authorisation|Penetration testers operate with explicit written consent from the system owner.|Attackers operate without consent and violate the rights and security of the organisation.|
|Scope|Penetration testers operate within a clearly defined scope that is set by the organisation in order to protect critical systems.|Attackers are not restricted by scope and will target anything that helps them achieve their goal.|
|Coverage|Penetration testers aim for broad coverage and assess multiple areas of a system.|Attackers focus on the quickest or most effective path to success, and often target systems and data that can provide financial benefit.|
|Responsibility|Penetration testers are accountable for their actions and the impact of their work, and are expected to act professionally at all times.|Attackers do not feel accountable for their actions and do not take responsibility for the damage that they cause.|
## Focus Areas
1. **Web App**
   Focuses on finding gaps and weaknesses in a web app. Commonly performed from a user perspective by interacting with the application's user interface and its APIs. The goal is to assess how the application handles user input, authentication, authorisation, sessions, and data processing.
	1. **Authentication:** Evaluation of weaknesses in the credential handling, password policy, account lockout mechanisms, multi-factor authentication (MFA) implementation, protection against automated attacks such as brute-force and credential stuffing, password-reset flow and overall authentication-related logics.
	2. **Authorisation:** Evaluation of weaknesses in the app's access control mechanism, ensuring users can only access resources and perform actions permitted by their role, and protection against vertical and horizontal privilege escalation attacks.
	3. **Session management:** Evaluation of weaknesses in how sessions are created, maintained, and invalidated. This includes session fixation risks, session invalidation after logout, idle timeout enforcement, secure cookie ttributess, and protection against cross-site request forgery (CSRF).
	4. **Input and output validation:** Evaluation of weaknesses in the app's data handling controls, including injection attacks, data type validation, and output handling.
	5. **Security configuration:** Evaluation of gaps in the server and app configs, including security headers, error handling behavior, rate-limiting controls, cryptographic configuration, and exposure of unnecessary services or features.
2. **Network Penetration Testing**
   Focuses on finding vulnerabilities in the underlying infrastructure that connects systems together. Testing can be from external or internal user perspective.
	1. **Authenticatio mechanisms:** Evaluation of weaknesses in network-level authentication controls such as password policy, multi-factor authentication (MFA) enforcement, credential reuse, use of default credentials,  and protection against attacks on services such as admin portals, VPN, SSH, or RDP.
	2. **Authorisation and access controls:** Evaluation of weaknesses in network access control mechanisms, ensuring users and systems can only access resources permitted by their role or trust level.
	3. **Network segmentation and trust relationships:** Evaluation of weaknesses in user and system trust relationships, firewall rules, and isolation controls.
	4. **Configuration and patch management:** Evaluation of gaps in device configs and services, such as use of outdated software, insecure default configuration, and weak encryption protocols.

## Risk Management
- **Identification:** Identifying assets, vulnerabilities, and potential threats that could affect the org.
- **Analysis:** Evaluating risks to determine the potential impact and likelihood of exploitation, which translates to the severity of each risk and helps plan remediation efforts.
- **Mitigation:** Reducing or controlling identified risks. Includes applying security patches, strengthening access controls, improving configurations, implementing monitoring tools, or redesigning insecure processes.
- **Monitoring:** Continuous monitoring of risks to ensure that controls remain effective, new vulnerabilities are detected, and emerging threats are addressed promptly.

## Common Causes and Resulting Vulnerabilities
|Root Cause|Example Scenario|Resulting Vulnerability|
|---|---|---|
|Human Assumptions|Developer assumes users only upload images to a profile photo field, no validation is in place.|Unrestricted file upload: An attacker uploads a web shell and executes OS commands on the server.|
|Software Bugs|Form data is concatenated into a database query rather than handled through parameterised inputs.|SQL injection: An attacker crafts input to extract or modify sensitive records from the database.|
|System Complexity|Multiple integrated services make it easy to overlook a misconfigured API endpoint.|Exposed admin API: An attacker reaches admin functions that were never meant to be publicly accessible.|
|Over-Customisation|Custom authentication replaces a standard framework with logic that is hard to maintain.|Weak authentication: Outdated hashing and broken session logic expose user accounts to takeover.|
|Technical and Design Flaws|Authenticated session is issued before the MFA step is completed.|MFA bypass: An attacker navigates to authenticated pages without completing the MFA process.|

## Good Mindset in Pentesting
- **Understanding the system:** In most cases, systems behave differently from one another. Having a strong understanding of how the target operates will help testers plan ahead and gain the context to explore potential exploitation opportunities.
- **Attention to detail:** Observation on the app behaviors and noticing small differences in certain scenarios often results in impactful findings.
- **Constant curiosity:** Having that "What if?" mindset.
- **Prioritising critical areas:** Focusing on high-impact functions before lower-risk features ensures that effort is directed towards areas where security issues would cause greater business impact.
- **Thinking in context:** Understanding how a feature is used and what data it handles helps assess risk accurately and allows findings to be evaluated based on real-world consequences rather than technical severity alone.
- **Creative thinking:** Hardened systems usually create an assumption that weaknesses may not exist. Approaching these systems with creativity allows a tester to identify weaknesses that could otherwise be overlooked. This involve linking two vulnerabilities in order to achieve greater impact.

| Effective Mindset                                                      | Ineffective Mindset                                                     |
| ---------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| Understand the system: Study how the target operates before testing    | Rushing to exploitation: Attacking without understanding the system     |
| Attention to detail: Notice subtle behavioural differences             | Ignoring context: Reporting findings without business relevance         |
| Constant curiosity: Ask "what if?" to uncover new opportunities        | Over-reliance on tools: Missing logic flaws that require manual testing |
| Prioritise critical areas: Focus effort on high-impact functions first | Making assumptions: Guessing system behaviour without verification      |
| Think in context: Assess risk by real-world consequence                | Tunnel vision: Fixating on one area at the cost of coverage             |
| Creative thinking: Chain vulnerabilities for greater impact            | Blindly following a checklist: Missing deeper issues outside the list   |

## Good Practices During a Pentest
- **Maintaining Good Notes:** If a vulnerability is discovered early, having detailed notes allows the tester to revisit later without repeating the entire process.
- **Collecting Evidence:** Screenshots, outputs, or logs. Need some sort of validity and proofs to support our findings.
- **Managing Time:** Pentests are performed within fixed timelines.
- **Proactive Communication:** Keep the engagement on track by providing regular progress updates and highlighting blockers early.
- **Staying Professional:** Reinforces the tester's credibility and reliability.

## Ethics
- Respecting the defined scope and avoiding activities outside authorised boundaries
- Avoiding actions that could disrupt systems and services
- Handling sensitive data responsibly and only accessing data that is required to demonstrate impact
- Stopping and reporting unexpected access to highly sensitive or out-of-scope systems
- Redacting sensitive data in reports to prevent unintended disclosure

## Permission
- Obtaining written authorisation before initiating any testing activities
- Defining a clear scope and adhering to the agreed-upon scope of testing
- Confirming testing windows and approved methods before conducting potentially disruptive actions
- Seeking clarification when uncertainty exists about whether a system or action is in scope
- Pausing and notifying the organisation if activities may exceed authorised boundaries

## Trust
- Providing status updates periodically to reassure stakeholders that testing is progressing as planned
- Being transparent about limitations, blockers, and uncertainties during testing in order to receive proper assistance
- Reporting findings accurately and clearly demonstrating business impact
- Providing actionable recommendation that help eleminates or mitigate risks
- Addressing organisational concerns promptly and adapting communication style to technical and non-technical stakeholders