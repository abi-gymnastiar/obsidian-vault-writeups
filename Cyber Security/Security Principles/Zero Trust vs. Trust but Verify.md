## Trust but Verify
Teaches to always verify even when we trust an entity and its behavior. Verify usually requires setting up proper logging mechanisms; verifying indicates going through the logs to ensure everything is normal. This requires automated security mechanisms, such as proxy, intrusion detection, and intrusion prevention systems.

## Zero Trust
Treats trust as a vulnerability, as it caters to insider-related threats. Zero trust tries to eliminate trust. Every entity is considered adversarial until proven otherwise. Zero trust does not grant trust to a device based on its location or ownership, even within internal networks or enterprise-owned devices. Authentication and authorization are required before accessing any resource. As a result, if any breach occurs, the damage would be more contained if a zero trust architecture had been implemented.

## Microsegmentation
One of the implementations used for Zero Trust. It refers to the design where a network segment can be as small as a single host. Moreover, communication between segments requires authentication, access control list checks, and other security requirements.

Note:
There is a limit to how much we can apply zero trust without negatively impacting a business; however, this does not mean that we should not apply it as long as it is feasible.