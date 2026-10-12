The International Organization for Standardization (ISO) and the International Electrotechnical Commission (IEC). ISO/IEC 19249:2017 _Information technology - Security techniques - Catalogue of architectural and design principles for secure products, systems and applications_.

## Five Architectural Principles
1. **Domain Separation:**
   Every set of related components is grouped as a single entity. Components can be applications, data or other resources. Each entity will have its own domain and be assigned a common set of security attributes. For example: x86 processor privilege levels. The OS kernel can run in ring 0, while user-mode apps can run in ring 3. Domain separation is included in the Goguen-Meseguer Model.
2. **Layering:**
   Structuring a system into many abstract levels or layers -> becomes possible to impose security policies at each levels. Also would be feasible to validate the operation. Example is the seven layers of OSI mode. Each layer provides specific services to the layer above it. Another example is how a programmer uses a high level programming language, which hides the low-level system calls and presents them as more user-friendly methods.
3. **Redundancy:**
   Ensures availability and integrity. Example is having two power supply for a machine/system. If one fails, system still continues to function. RAID 5 configuration allows data to remain available when one of the dies.
4. **Virtualization:**
   Sharing a single set of hardware among multiple operating systems. Provides sandboxing capabilities that improve security boundaries, secure detonation, and observance of malicious programs.

## Five Design Principles
1. **Least Privilege:**
   "need-to basis" or "need-to-know basis" to answer the question, "who can access what?". Giving the least amount of permissions possible for someone to carry out their task **and nothing more.** Example is only give a user a read permission to a document.
2. **Attack Surface Minimization:**
   Not all vulnerabilities are known. Vulnerabilities represent risks that we should aim to minimize. For example is when hardening a Linux system, we would disable any service we don't need.
3. **Centralized Parameter Validation:**
   Validate parameters in a centralized library or system.
4. **Centralized General Security Services:**
   Security authentication in a centralized server. Still have to take proper measure to ensure availability and prevent creating a single point of failure.
5. **Preparing for Error and Exception Handling:**
   When building a system, errors and exceptions do and will occur. This principle teaches that the system should be designed to fail safe. Example is if a firewall crashes, it should block all traffic instead of allowing all traffic. We should also be careful that error messages don't leak any confidential information, such as dumping memory content that contains information related to other customers.