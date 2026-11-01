## Types
- **Phising:** Sending the same believable messages to many people at once. Aims to get a stolen passwords, card details, or a foothold on a device.
- **Spear Phising:** Targeted attack to a specific person.
- **Whaling:** Similar to spear phising but to the higher-ups (CEOs and CFOs). This concentrates on people whose decision-making power can move money, expose regulated data, or override controls.

## Social Engineering Principles
- **Scarcity**
	- Example: “Only three TryPhones up for grabs.”
- **Urgency**
	- Example: “Your account will be suspended in 12 hours unless you verify your identity through this portal.”
- **Authority**
	- Example: “From: IT Administrator. Action required on your SSO settings.”
- **Fear**
	- Example: “We detected suspicious logins on your account. Secure it immediately here.”
- **Curiosity**
	- Example: “Confidential: Q3 roadmap highlights.”
- **Trust**
	- Example: “Microsoft 365: New security notice available in your portal,” or a message that looks like it’s from a known teammate asking for a quick review.

## Cognitive Biases
A tendency to make decisions based on feelings, assumptions, or past experiences instead of facts.
- **Overconfidence Bias:** Thinking they're too smart to fall for phising scams.
- **Confirmation Bias:** Accepting information that fits their expectation.
- **Authority Bias:** Trust messages from those they see as authority figures without question.

## Techniques
### URL and Domain Manipulation
- **URL Masking:** Displaying `http://legit.com` but then redirecting users to `http://phiser.com`.
- **Homograph Attacks:** Exploiting visual similarities by characters. Example is `google.com` into `go0gle.com`.
- **Typosquating:** Registering domains similar to legitimate ones. Example is `youtbe.com` instead of `youtube.com`.

### Email Spoofing
Exploiting the lack of built-in functions to authenticate email addresses within **SMTP**. Other variant includes using domains similar to legitimate ones, for example, `support@company-secure.com` instead of `support@company.com`, to trick recipients into trusting the email.

Security measures for this:
- SPF (Sender Policy Framework)
- DMARC (Domain-based Message Authentication, Reporting, and Conformance)
- DKIM (DomainKeys Identified Mail)

### Credential Harvesting
Invlolves a login cloning attack, wher the attacker replicates all visual elements of the legitimate website.

### Payload Delivery Mechanisms
Involves something like a MS documents containing a macro. Upon opening the file, the victim encounters a prompt to "Enable Contet" in order to view the document, which serves as a built-in social engineering tactic. Once enabled, the VBA macro runs in the background.
1. Victim receives and open the .docm file
2. MS Word prompts the user to enable macros
3. Victim does that
4. VBA macro executes a hidden command
5. Attacker receives the confirmation of execution

## Tools
- **GoPhish:** we-based framework that makes setting up phishing campaigns simpler.
- **EvilNginx:** For advanced phishing campaigns that bypass MFA. Acts as a reverse proxy between victims and legitimate sites, capturing credentials and session tokens in real time.
- **SET (The Social Engineering Toolkit)**

## Phising Campaign
- Planning & Scoping
- Reconnaissance
- Scenario & Payload Development
- Exploitation and Post-Exploitation
- Reporting and Debriefing

Recomendations Table:

|Metric|What it measures|Benchmark|Suggested Recommendation(s)|
|---|---|---|---|
|Open Rate|% of users who opened the email.|Industry varies; typical phishing open rates ~50–65%|Targeted refresher training|
|Click Rate|% of all users who clicked a link.|8–14% acceptable; >14% high risk|Focused security awareness training|
|Credential Entry Rate|% of all users who entered credentials after clicking.|<2% low risk; 2–5% moderate risk; >5% high risk|Phishing site identification training, MFA implementation|
|Attachment Detonation Rate|% of users who opened/executed an attachment.|No formal benchmark; >5–7% suggests risk|Educate on safe handling of attachments, Sandbox detonation|
|Reporting Rate (24h)|% of users who reported the email within 24h.|>40% strong; 30–40% average; <30% low||
