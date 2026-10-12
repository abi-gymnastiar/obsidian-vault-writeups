IAA : Identity, Authentication, Authorsation, and Accountability.
- **Identity:** The unique account (user ID, email, etc...) that represents a person or service.
- **Authentication:** Proving that identity (passwords, OTP, passkeys).
- **Authorisation:** What that identity is allowed to do.
- **Accountability:** Recording and alerting on who did what, when, and from where.

## A01: Broken Access Control
Happens when the server doesn't properly enforce **who can access what** on every request. A common occurance of this is **IDOR** (Insecure Direct Object Reference), if changing an ID (like `?id=7 -> ?id=6`) lets you see or edit someone else's data, access control is broken.

Can shows up as horizontal privilege escalation or vertical privilege escalation because the application trusts the client too much.

How to fix?
- Implement proper authentication and authorization checks
- Verify that the authenticated user has permission to access the requested resource
- Use indirect references or encrypted tokens instead of predictable IDs
- Log and monitor access attempts to sensitive resources

## A02: Security Misconfigurations
Happen when systems, servers, or apps are deployed with unsafe defaults, incomplete settings, or exposed services. A small misconfigurations can expose sensitive deata, enable privilege escalation, or give attackers a foothold into the system.

**Example:** in 2017, Uber exposed a backup AWS S3 bucket with sensitive user data, including driver and rider information, because the bucket was publicly accessible.

Common Patterns:
- Default credentials or weak passwords left unchanged
- Unnecessary services or endpoints exposed to the internet
- Misconfigured cloud storage or permissions (S3, Azure Blob, GCP buckets)
- Unrestricted API access or missing authentication/authorisation
- Verbose error messages exposing stack traces or system details
- Outdated software, frameworks, or containers with known vulnerabilities
- Exposed AI/ML endpoints without proper access controls

How to fix / prevent?
- Harden default configs and remove unused features or services
- Enforce strong authentication and least privilege across all systems
- Limit network exposure and segment sensitive resources
- Keep software, frameworks, and containers up to date with patches
- Hide stack traces and system info from error messages
- Audit cloud configurations and permission regularly
- Secure AI endpoints and automation services with proper access controls and monitoring
- Integrate configuration reviews and automated security checks into your deployment pipeline

## A03: Software Supply Chain Failures
Happen when applications rely on components, libraries, services, or models that are compromised, outdated, or improperly verivied. Modern apps are built from many third-party packages, APIs, and AI models. One compromised dependency can compromise you entire syustem.

**Example:** In 2021, the SolarWinds Orion compromise showed the danger of supply chain attacks. Attackers inserted malicious code into a trusted update, affecting thousans of organisations that automatically installed it.

With AI, we can observe this when using unverified third-party models or fine-tuned datasets that can embed hidden behaviours, backdoors, or biased outputs, compromising systems or leaking data.

Common Patterns
- Using unverified or unmaintained libraries and dependencies
- Automatically installing updates without verification
- Over-reliance on third-party AI models without monitoring or auditing
- Insecure build pipelines or CI/CD processes that allow tampering
- Poor license or provenance tracking for components
- Lack of monitoring for vulnerabilities in dependencies after deployment
How to fix / prevent?
- Verify all third-party components, libraries, and AI models before use
- Monitor and patch dependencies regularly
- Sign, verify, and audit software updates and packages
- Lock down CI/CD pipelines and build processes to prevent tampering
- Track provenance and licensing for all dependencies
- Implement runtime monitoring for unusual behaviour from dependencies or AI components
- Integrate supply chain threat modelling into the SDLC, including testing, deployment, and update workflows

## A04: Cryptographic Failures
Happen when encryption is used incorrectly or not at all. This includes weak algorithms, hard-coded keys, poor key handling, or unencrypted sensitive data. Web apps rely on cryptography everywhere: protecting network traffic, securing stored data, verifying identities, and safeguarding secrets. When these controls fail, sensitive data such as passwords, tokens, or personal information can be exposed, leading to account takeovers or full-scale breaches.

Attackers can Exploit these flaws through man-in-the-middle attacks, brute-force attacks on weak keys, or by simply discovering secrets that were never properly protected.

Common patterns
- Using deprecated or weak algorithms like MD5, SHA-1, or ECB mode
- Hard-coded secrets in code or configuration
- Poor key rotation or management practices
- Lack of encryption for sensitive data at rest or in transit
- Self-signed or invalid TLS certificates
- Using AI/ML systems without proper secret handling for model parameters or sensitive inputs
How to fix / prevent?
- Use strong, modern algorighms such as AES-GCM, ChaCha20-Poly1305, or enforce TLS 1.3 with valid certificates
- Use secure key management service like Azure Key Vault, AWS KMS, or HashiCorp Vault
- Rotate secrets and keys regularly, following defined crypto periods
- Document and enforce policies and standard operating procedures for key lifecycle management
- Maintain a complete inventory of certificates, keys, and their owners
- Ensure AI models and automation agents never expose unencrypted secrets or sensitive data

## A05: Injection
Occurs when an app takes user input and mishandles it. Instead of processing the input, the app passes it directly into a system that can execute commands or queries, such as database, a shell, a templating engine or API.
Classic examples of injection:
- SQL Injection
- Command Injection
- AI Prompts
- Server Side Template Injection (SSTI)

## A07: Authentication Failures
Happen when an app can't reliably verivy or bind a user's identity. Common issues include:
- username enumeration
- weak/guessable passwords with no lockout/rate limits
- logic flaws in the login/registration flow
- insecure session or cookie handling

## A08: Software or Data Integrity Failures
Happens when an app relies on code, updates, or data it assumes are safe, without verifying their authenticity, integrity, or origin. This includes trusting software updates without verification, loading scripts or configuration files from untrusted sources, failing to validate data that impacts app logic, or accepting data such as binaries, templates, or JSON files without confirming whether it has been altered.

How to avoid?
Begins with establishing trust boundaries. Apps should never assume that code, updates, or key pieces of data are legitimate and automatically trusted. Their integrity must be verified. This includes doing checksums.
## A06: Insecure Design
Happens when flawed logic or architecture is built into a system from the start. These flaws stem from skipped threat modelling, no design requirements or reviews, or accidental errors. With AI assistants, AI systems exacerbate insecure design.

It's important to note that you can't patch an insecure design, as it is built into the workflow logic, and trust boundaries. Fixing it means rethinking how systems, and now AI, make decisions.

**Example:** Clubhouse. Its early design assumed users would onlyinteract through the mobile app, but the backend API had no proper authentication. Anyone could query user data, room info, and even private conversations directly. When researchers tested it, the entire private conversation premise fell apart.

Common patterns (in 2025):
 - Weak business logic controls, like recovery or approval flows
 - Flawed assumptions abut user or model behaviour
 - AI components with unchecked authority or access
 - Missing guardrails for LLMs and automation agents
 - Test or debug bypasses left in production
 - No consistent abuse-case review or AI threat modelling
How to fix / prevent?
- Treat every model as untrusted until proven otherwise
- Validate and filter all model inputs and outputs to ensure accuracy and integrity
- Separate system prompts from user content
- Keep sensitive data out of prompts unless absolutely needed and protect it with strict controls
- Require human review for high-risk AI actions
## A10: Logging & Alerting Failures
Without record or alert on security-relevant events, defender can't detect or investigate attacks. Good logging underpins **accountability**. Failures include:
- Missing authentication events
- Vague error logs
- No alerting on brute-force or privilege changes
- Short retention
- Logs stored where attackers can tamper with them