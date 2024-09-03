### Security Engineer Training
- [Security Engineer](https://tryhackme.com/r/path-action/security-engineer-training/join)
- [Introduction to Security Engineering](https://tryhackme.com/r/module/introduction-to-security-engineering)

## Security Principles

When discussing security principles, it's crucial to understand how different models and frameworks guide the development and implementation of secure systems. Here are some of the fundamental models and principles:

### Bell-LaPadula Model
The Bell-LaPadula (BLP) model is a formal security model used to enforce access control in government and military applications. It is primarily concerned with maintaining data confidentiality and is based on two main rules:
1. **Simple Security Property ("No Read Up")**: A subject at a lower security level cannot read data at a higher security level.
2. ***-Property ("No Write Down")**: A subject at a higher security level cannot write data to a lower security level.

This model is focused on preventing information leakage between different levels of classified information, ensuring that data flows only in a way that preserves confidentiality.

### Biba Model
The Biba model is the counterpart to Bell-LaPadula but focuses on data integrity rather than confidentiality. It is based on the idea that higher integrity levels should not be contaminated by lower integrity levels:
1. **Simple Integrity Property ("No Write Up")**: A subject at a lower integrity level cannot write to an object at a higher integrity level.
2. *** Integrity Property ("No Read Down")**: A subject at a higher integrity level cannot read data from a lower integrity level.

The Biba model is used in environments where maintaining the accuracy and reliability of data is critical.

### Clark-Wilson Model
The Clark-Wilson model focuses on ensuring data integrity through well-formed transactions and separation of duties. It defines mechanisms that enforce security policies by:
1. **Well-formed transactions**: Ensuring that only legitimate transactions occur.
2. **Separation of duties**: Requiring that different roles or users must be involved in different parts of a transaction, minimizing the risk of fraud or error.

The Clark-Wilson model is particularly applicable in commercial environments where data integrity and accuracy are vital, such as in financial systems.

### Zero Trust
Zero Trust is a security concept that assumes no user, system, or network inside or outside an organization's perimeter should be trusted by default. Trust is treated as a vulnerability, particularly in the context of insider threats, where users with legitimate access can potentially compromise systems. The core principle of Zero Trust is:
1. **Never Trust, Always Verify**: Every access request is fully authenticated, authorized, and encrypted before granting access, regardless of the user's location within the network.

Zero Trust architecture typically includes:
- **Micro-segmentation**: Dividing the network into smaller zones to maintain granular control over access.
- **Least Privilege Access**: Granting users the minimum access necessary to perform their tasks.
- **Continuous Monitoring**: Constantly monitoring and validating user behavior to detect and respond to anomalies.

### Trust but Verify
"Trust but Verify" is a security principle that aligns closely with the Zero Trust model. Originally a Russian proverb popularized in the context of nuclear arms control, it means that while trust can be extended to parties, it should always be accompanied by verification to ensure that the trust is justified. In cybersecurity, this principle translates to:
1. **Initial Trust**: You may trust a user, device, or application based on credentials, but you don't let down your guard.
2. **Ongoing Verification**: Continuously verify the user's or system's behavior to detect any signs of compromise or malicious intent.

In the context of Zero Trust, "Trust but Verify" reinforces the idea that even after trust has been established, systems must continuously monitor and validate that trust to maintain security. It recognizes that threats can arise from any user or system, and the only way to mitigate these risks is through constant vigilance and verification.

---

## ISO/IEC 19249:2017 Security Architecture Principles

The ISO/IEC 19249:2017 standard provides a catalog of architectural principles that guide the design and implementation of secure information systems. Here are some key principles:

### Domain Separation
Domain separation involves dividing the system into distinct domains, each with its own set of rules and policies. This separation ensures that an issue in one domain does not affect others, enhancing overall system security. Domain separation is critical in multi-tenant environments or systems that handle highly sensitive data, where isolation between users or processes is essential.

### Layering
Layering is the practice of structuring a system in layers, where each layer provides a set of services to the layer above it and receives services from the layer below. This principle helps to limit the impact of vulnerabilities because if one layer is compromised, it does not necessarily compromise the entire system. Layering also allows for more manageable and modular security measures.

### Encapsulation
Encapsulation involves hiding the internal workings of a system or component from the outside world, exposing only what is necessary. By restricting access to internal functions and data, encapsulation helps to prevent unauthorized access and manipulation. This principle is fundamental in object-oriented programming and is essential for maintaining the integrity and security of systems.

### Redundancy
Redundancy involves duplicating critical components or functions of a system to increase reliability and availability. In security, redundancy can ensure that if one security mechanism fails, another is in place to provide protection. Redundancy is often implemented in the form of backup systems, failover solutions, or multiple layers of defense.

### Virtualization
Virtualization is the creation of virtual versions of physical resources, such as servers, storage devices, or networks. It allows for greater flexibility, isolation, and resource utilization. Virtualization also plays a significant role in security by enabling the creation of isolated environments (e.g., virtual machines) that can be used to contain and analyze potentially malicious software without risking the host system.

### ISO/IEC 19249:2017 Five Design Principles

#### Least Privilege
The principle of least privilege states that users, systems, and processes should be granted the minimum level of access necessary to perform their functions. This reduces the risk of accidental or intentional misuse of access rights and limits the potential damage in the event of a compromise.

#### Attack Surface Minimization
Minimizing the attack surface involves reducing the number of entry points that an attacker can exploit. This can be achieved by disabling unnecessary services, removing unused software, and limiting the number of open ports. A smaller attack surface makes it more difficult for attackers to find vulnerabilities.

#### Centralized Parameter Validation
Centralized parameter validation ensures that all input and output are validated at a single, central point rather than at multiple points throughout the system. This reduces the likelihood of input validation errors, which can lead to vulnerabilities such as injection attacks.

#### Centralized General Security Services
Centralized general security services involve consolidating security-related functions (such as authentication, authorization, and logging) into a single, centrally managed service. This approach simplifies management, reduces redundancy, and ensures consistent application of security policies across the system.

#### Preparing for Error and Exception Handling
This principle emphasizes the importance of designing systems to handle errors and exceptions in a secure manner. Proper error handling ensures that the system remains secure even when unexpected conditions occur, preventing attackers from exploiting errors or gaining insights into the system's internals.

---