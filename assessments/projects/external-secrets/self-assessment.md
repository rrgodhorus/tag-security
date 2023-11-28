# Self-assessment for External Secrets Operator (ESO)

## Table of contents

* [Metadata](#metadata)
  * [Security links](#security-links)
* [Overview](#overview)
  * [Actors](#actors)
  * [Actions](#actions)
  * [Background](#background)
  * [Goals](#goals)
  * [Non-goals](#non-goals)
* [Self-assessment use](#self-assessment-use)
* [Security functions and features](#security-functions-and-features)
* [Project compliance](#project-compliance)
* [Secure development practices](#secure-development-practices)
* [Security issue resolution](#security-issue-resolution)
* [Appendix](#appendix)

## Metadata

|   |  |
| -- | -- |
| Software | A link to the External-Secret Operator's repository: [external-secrets](https://github.com/external-secrets/external-secrets) |
| Security Provider | No - ESO does not handle secret creation by itself |
| Languages | Go, HCL, Makefile, Shell, Smarty, Dockerfile |
| SBOM | SBOM generated using **FOSSA-cli** tool on the latest code base. [Link to SBOM](./docs/REPORT_external-secrets_2023-11-27_193318345Z.spdx.json) |
| | |

### Security links

| Doc | url |
| -- | -- |
| Security file | [Security.md](https://github.com/external-secrets/external-secrets/blob/main/SECURITY.md) |
| Default and optional configs | https://external-secrets.io/latest/guides/security-best-practices|

## Overview
External Secrets Operator (ESO) is a Kubernetes operator that integrates external secret management systems like AWS Secrets Manager, HashiCorp Vault, Google Secrets Manager, Azure Key Vault, IBM Cloud Secrets Manager, CyberArk Conjur and many more. The operator reads information from external APIs and automatically injects the values into a Kubernetes Secret.

<p align="center"><img width="600" alt="image" src="docs/overview.png"></p>


### Background
The External Secrets Operator (ESO) is a tool designed for Kubernetes, a widely-used system for automating the deployment, scaling, and management of containerized applications. ESO addresses a key challenge in this domain: secure and efficient management of sensitive configuration data, known as "secrets" (like passwords, API keys, etc.). Typically, managing these secrets within Kubernetes can be complex and risky if not handled properly. ESO simplifies this by integrating Kubernetes with external secret management services (such as AWS Secrets Manager or HashiCorp Vault), which specialize in securely storing and managing these secrets. This integration not only enhances security but also streamlines the process of injecting these secrets into Kubernetes applications.

### Actors

#### 1. External Secret Management Systems
- Examples: AWS Secrets Manager, HashiCorp Vault.
- Isolation: Network boundaries and authentication mechanisms separate these from the Kubernetes cluster.
- Security Note: A breach in these systems does not directly compromise the Kubernetes cluster.

#### 2. Kubernetes Cluster (including ESO)
- Role: ESO bridges Kubernetes and external secret systems.
- Isolation: Kubernetes Role-Based Access Control (RBAC) limits potential lateral movement in case of a compromise.

#### 3. Kubernetes Secrets
- Function: Store synchronized secrets within the cluster via ESO.
- Isolation: Kubernetes namespaces and access policies provide compartmentalization.
- Security Note: A compromise in one namespace doesn’t necessarily expose secrets in another.

  ##### Isolation Mechanisms Overview
  - The isolation between these actors relies on network security, access control mechanisms, and the principle of least privilege.
  - This architecture ensures a limited scope of breach, even if one part is compromised.

### Actions
  For the External Secrets Operator (ESO), the steps it performs to synchronize secrets from external sources into Kubernetes can be outlined focusing on security checks, data handling, and interactions:

  #### 1. Client Request to External Secret Management System
  ESO, acting as a client, sends a request to an external secret management system (like AWS Secrets Manager). This request includes authentication and authorization checks to ensure that ESO is permitted to access the requested secrets.
  #### 2. Retrieval of Secrets
  Upon successful authentication and authorization, the external system sends the requested secrets to ESO. These secrets are transmitted over secure, encrypted channels to ensure their confidentiality and integrity.
  #### 3. Validation and Transformation
  ESO validates the format and integrity of the received secrets. If necessary, it transforms the data to a format compatible with Kubernetes Secrets.
  #### 4. Synchronization to Kubernetes Secrets
  ESO then creates or updates Kubernetes Secret objects with the retrieved data. This step involves interacting with the Kubernetes API server, which includes RBAC checks to ensure that ESO has the necessary permissions to perform these operations.
  #### 5. Use by Kubernetes Workloads
  Applications or workloads running in Kubernetes can then access these secrets. Access to these secrets within Kubernetes is controlled by namespace-specific policies and RBAC, ensuring that only authorized workloads can retrieve them.

  Overall, ESO's goal is to provide a secure, efficient, and reliable way of managing secrets in Kubernetes environments, ensuring that only authorized entities have access to sensitive data and that this data is handled securely at all times.

### Goals
    
  #### 1. Secure Secret Management
  ESO's primary goal is to securely manage secrets within Kubernetes by leveraging external secret management systems. It ensures that sensitive information like API keys, passwords, and tokens are stored and managed in systems specifically designed for this purpose, offering robust security features.

  #### 2. Automated Synchronization
  ESO automates the synchronization of secrets from these external systems into Kubernetes, ensuring that applications always have access to the latest version of each secret.

  #### 3. Access Control
  By integrating with external secret managers, ESO inherits and enforces their access control mechanisms. It guarantees that only authorized parties can retrieve or modify the secrets, both in the external systems and when they are injected into Kubernetes.

  #### 4. Encryption and Integrity
  ESO ensures that the transmission of secrets from external systems to Kubernetes is secure, maintaining the confidentiality and integrity of the data throughout the process.


### Non-goals

  #### 1. Secret Data Encryption
  While ESO manages encrypted secrets, it does not provide encryption services itself. The actual encryption and decryption are handled by the external secret management systems.

  #### 2. Intrusion Detection or Prevention
  ESO does not function as an intrusion detection or prevention system. It does not monitor or protect against unauthorized access within the Kubernetes cluster or the external secret systems.

  #### 3. Full Lifecycle Management of Secrets
  The primary role of ESO is to synchronize secrets from external systems to Kubernetes. It does not manage the full lifecycle (like creation, rotation, and deletion) of secrets within the external systems.

  #### 4. Direct Security Auditing or Compliance Assurance
  ESO does not perform security auditing or provide direct compliance assurance. It relies on the security and compliance features of the external secret management systems it integrates with.

## Self-assessment use

This self-assessment is created by the External Secrets Operator (ESO) team to perform an internal analysis of the project's security. It is not intended to provide a security audit of ESO, or function as an independent assessment or attestation of ESO's security health.

This document serves to provide ESO users with an initial understanding of ESO's security, where to find existing security documentation, ESO plans for security, and a general overview of ESO security practices, both for the development of ESO as well as the security of ESO.

This document provides the CNCF TAG-Security with an initial understanding of ESO to assist in a joint-assessment, necessary for projects under incubation. Taken together, this document and the joint-assessment serve as a cornerstone for if and when ESO seeks graduation and is preparing for a security audit.


## Security functions and features

### Critical Components
* Authentication and Authorization Interface: Connects with external secret management systems; ensures only authenticated and authorized access to secrets.
* Secret Synchronization Mechanism: Securely transfers secrets from external systems to Kubernetes, critical for maintaining the confidentiality and integrity of secret data.
* Kubernetes Secrets Management: Handles the creation and updating of Kubernetes Secrets, a fundamental aspect of ensuring that only authorized Kubernetes workloads can access the synchronized secrets.

### Security Relevant Components.
* Configuration of Secret Stores: Involves setting up SecretStore and ClusterSecretStore resources, impacting how securely ESO interacts with external systems.
* Role-Based Access Control (RBAC) Configuration: Determines what resources within Kubernetes the ESO can access, significantly affecting the isolation and security of the secrets.
* Network Policies: Governs the network traffic to and from ESO, relevant for preventing unauthorized network access.

### [Threat Modeling with STRIDE](#external-secrets-operator-threat-modeling)

## Project compliance

* Compliance - No specific compliance certifications or adherence to standards like PCI-DSS, COBIT, ISO, or GDPR have been documented. 

## Secure development practices

ESO has achieved a "passing" Open Source Security Foundation (OpenSSF) best practices badge
[![OpenSSF Best Practices](https://www.bestpractices.dev/projects/5947/badge)](https://www.bestpractices.dev/projects/5947). The project is working on receiving a silver badge and is in the process of meeting the criteria for it.

### Development Pipeline

* All source code is publicly maintained in [GitHub](https://github.com/external-secrets/external-secrets).
* Code changes are submitted via Pull Requests (PRs) and must be reviewed and approved by at least one maintainer.
* Commits to the `main` branch are merged only when a PR is approved and passes all checks.
* Once a pull request has been opened it will be assigned to a reviewer from external-secrets/maintainers.
* ESO uses the GitHub Bot [Paul the Alien](https://github.com/apps/paul-the-alien) (which is still in Alpha) to store the list of maintainers, to approve/merge PRs and to delete branches after PR merges.
* Raising a PR triggers a series of GitHub actions and workflows whose component checks are broken down below:
  * [Sonar Cloud Quality Gate](https://sonarcloud.io/project/issues?id=external-secrets_external-secrets) check initiated by the sonarcloud bot which checks for bugs, vulnerabilities, security hotspots, code smells, code coverage and duplication
  * Developer Certificate of Origin (DCO) check to verify commits are signed correctly
  * Detect noop ([skip-duplicate-actions](https://github.com/fkirc/skip-duplicate-actions)) 
  * Linting check
  * Diff check
  * Unit tests
  * E2E tests
  * Dependency License Checks (FOSSA)
  * Building the image
  * Image scanning for vulnerabilities using Trivy
  * Create SBOM & provenance files and sign the image
  * Publish signed artifacts to Dockerhub

### Communication Channels
* Referenced in docs under [How to Get Involved](https://external-secrets.io/latest/#how-to-get-involved) and described below:
  * Internal:
    * Bi-weekly Development Meeting every odd week at 8:00 PM Berlin Time on Wednesday ([agenda](https://hackmd.io/GSGEpTVdRZCP6LDxV3FHJA), [jitsi call](https://meet.jit.si/eso-community-meeting))
    * [Kubernetes Slack: #external-secrets channel](https://kubernetes.slack.com/messages/external-secrets)
    * [Github Issues](https://github.com/external-secrets/external-secrets/issues)
    * [Github Discussions](https://github.com/external-secrets/external-secrets/discussions)
  * Inbound:
    * [Github Issues](https://github.com/external-secrets/external-secrets/issues)
    * [Github Discussions](https://github.com/external-secrets/external-secrets/discussions)
    * [Contributing Process](https://external-secrets.io/latest/contributing/process/)
    * Contact Email: contact@external-secrets.io
  * Outbound:
    * [Twitter](https://twitter.com/ExtSecretsOptr)

### Ecosystem
* ESO has replaced the now deprecated and archived kubernetes-external-secrets as detailed in this [issue](https://github.com/external-secrets/kubernetes-external-secrets/issues/864). We can expect the services that used kubernetes-external-secrets to migrate to ESO.
* ESO also maintains an official list of its [Adopters](https://github.com/external-secrets/external-secrets/blob/main/ADOPTERS.md). 

## Security issue resolution
ESO's security policy can be found in [SECURITY.md](https://github.com/external-secrets/external-secrets/blob/main/SECURITY.md).

### Responsible Disclosures Process
* ESO specifies that any security vulnerability found should be sent as a confidential email to contact@external-secrets.io and not be reported as an Issue.
### Vulnerability Management Plans
* ESO uses Github Security Alerts and Dependabot Dependency Updates to learn about critical software updates and security threats.
### Vulnerability Response Process
* No specific information on this is provided in the security file currently.
* Recommendation:
  * Dedicated email for reporting security bugs/vulnerabilities that is monitored 24x7.
  * Acknowledgement email to reporter within a reasonable time span once the process it started.
  * Information on maintainers who are charged with responding to security events (in case an immediate response is required).
  * Include a specific timeline/procedures to assign resources and fix a reported vulnerability.
### Incident Response
* Since this project does not directly handle any customer data, any users who experience an incident will report a vulnerability.


## Appendix

### Known Issues Over Time
* ESO tracks all bugs and issues publicly at external-secrets [Github Issues](https://github.com/external-secrets/external-secrets/issues).
* No major vulnerability has been documented or disclosed to the public.

### [CII Best Practices](https://www.coreinfrastructure.org/programs/best-practices-program/)
*  ESO has achieved a "passing" Open Source Security Foundation (OpenSSF) best practices badge [![OpenSSF Best Practices](https://www.bestpractices.dev/projects/5947/badge)](https://www.bestpractices.dev/projects/5947). The project is working on receiving a silver badge and is in the process of meeting the criteria for it.

### Case Studies
* Use Case 1: Managing secrets for multiple environments

  A company has multiple environments for their applications, such as development, staging, and production. They use different credentials for each environment to access external resources, such as databases and cloud storage. ESO can be used to automatically synchronize these credentials to the appropriate Kubernetes secrets for each environment. This makes it easy to manage secrets and ensures that applications always have access to the correct credentials. 
* Use Case 2: Rotating secrets for security

  It is important to rotate secrets regularly to prevent unauthorized access. However, manually rotating secrets can be time-consuming and error-prone. ESO can be used to automate the rotation of secrets. ESO can be configured to fetch secrets from an external source, such as AWS Secrets Manager or HashiCorp Vault, and then store them in Kubernetes secrets. ESO can also be configured to rotate secrets on a regular basis.
* Use Case 3: Accessing secrets from multiple sources

  A company may use multiple sources for their secrets, such as AWS Secrets Manager, HashiCorp Vault, and a custom secrets store. ESO can be used to access secrets from all of these sources. ESO can be configured to use different secret providers for different types of secrets. For example, ESO could be configured to use AWS Secrets Manager for database credentials and HashiCorp Vault for API keys.

### Related Projects / Vendors
* Kubernetes External Secrets (deprecated): [kubernetes-external-secrets](https://github.com/external-secrets/kubernetes-external-secrets) is the original project kicked off by Godaddy while external-secrets is the newer one that replaced it. According to the team, KES does not have any dedicated or active maintainers at this time and is on limited life support. There were tech debts in the project that were causing issues and some of the dependencies they depended on are no longer maintained. To replace them would have required a sizeable effort. Also, KES was originally written in Javascript and the newer ESO is written in the more Kubernetes-friendly Golang. There is a [tool](https://github.com/external-secrets/kes-to-eso) available to migrate from KES to ESO.

* CSI Secret Store - Integrates secrets stores with Kubernetes via a Container Storage Interface (CSI) volume. The Secrets Store CSI Driver `secrets-store.csi.k8s.io` allows Kubernetes to mount multiple secrets, keys, and certs stored in enterprise-grade external secrets stores into their pods as a volume. Once the Volume is attached, the data in it is mounted into the container’s file system. The differences between ESO and CSI Secret Store them are documented in this issue [comment](https://github.com/external-secrets/external-secrets/issues/478#issuecomment-964413129).

### External Secrets Operator Threat Modeling

ESO already has a threat-model documented at [threat-model](https://external-secrets.io/latest/guides/threat-model/).

We're also providing our own threat modeling using STRIDE below:

#### Spoofing

##### Threat-01-S - Spoofing of External Secrets Operator
A malicious actor might attempt to impersonate the External System Operator (ESO) to illicitly access confidential information stored in external systems.

##### Threat-01-S Recommended Mitigations
* Implementing mutual TLS (Transport Layer Security) ensures that both the ESO and the external secret management system authenticate each other.
* Using robust authentication mechanisms, like using API tokens or OAuth, further secure the connection, preventing impersonation.

##### Threat-02-S - Spoofing of External Secrets
The risk of the External Secrets Operator (ESO) interacting with a fake external secret store represents a significant security threat. In such a scenario, an attacker could set up a counterfeit secret management service, mimicking legitimate services like AWS Secrets Manager or HashiCorp Vault. If ESO is deceived into connecting with this fraudulent service, it could lead to several security breaches, including the leakage of sensitive information, injection of false secrets, or disruption of secret synchronization processes.

##### Threat-02-S Recommended Mitigations
* Certificate Verification: Implement strict SSL/TLS certificate verification for all external secret stores. This ensures that ESO establishes connections only with authenticated and validated services.
* Endpoint Validation: Configure ESO to connect only to known and verified endpoints of secret stores, possibly using allowlists for trusted URLs and IP addresses.
* Regular Auditing: Periodically audit and verify the configurations and connections of ESO to external services to detect any anomalies or changes that could indicate a security breach.

#### Tampering

##### Threat-03-T - Supply Chain Attacks
An attack can infiltrate the ESO container through various attack vectors. The following are some potential entry points, although this is not an exhaustive list:
* Source Threats: Unauthorized changes or inclusion of vulnerable code in ESO through code submissions.
* Build Threats: Creation and distribution of malicious builds of ESO, such as in container registries, Artifact Hub, or Operator Hub.
* Dependency Threats: Introduction of vulnerable code into ESO dependencies.
* Deployment and Runtime Threats: Injection of malicious code through compromised deployment processes.

##### Threat-03-T Recommended Mitigations
* CI/CD Security: Secure the Continuous Integration and Continuous Deployment (CI/CD) pipeline with tools that scan for vulnerabilities and unauthorized changes.
* Dependency Management: Regularly scan and update dependencies to prevent vulnerabilities and use trusted sources for dependencies.
* Container Image Security: Utilize signed and verified container images, and scan these images for vulnerabilities.

##### Threat-04-T - Tampering with ESO configuration files
The threat of tampering with External Secrets Operator (ESO) configuration files involves unauthorized modifications to the ESO's settings, which can lead to security breaches or malfunctioning of the system.

##### Threat-04-T Recommended Mitigations
* File Integrity Monitoring: Implement a system that continuously monitors the ESO configuration files for unauthorized changes. This tool alerts administrators whenever a file is altered, enabling quick detection and response to tampering.
* Access Controls: Restrict access to ESO configuration files using robust access control mechanisms. Ensure that only authorized personnel have the necessary permissions to modify these files, and enforce multi-factor authentication for added security.

#### Repudiation

##### Threat-05-R - Unauthorized Modification Denial
An unauthorized user might modify a secret and subsequently deny their involvement.

##### Threat-05-R Recommended Mitigations
* Implement a robust logging system that captures every interaction with the secrets, including access and modifications. The logs should record user identities, timestamps, and specific changes made to the secrets.
* Use digital signatures or similar mechanisms to ensure the integrity and non-repudiation of the log entries.
* Regular audits of these logs can help in quickly identifying and addressing unauthorized changes, thus holding users accountable for their actions.

##### Threat-06-R - Disputing Synchronization
There could be discrepancies or disputes regarding when a secret was synchronized from the external store to Kubernetes.

##### Threat-06-R Recommended Mitigations
* Maintaining detailed logs with precise timestamps for each synchronization event helps resolve these disputes. These logs should capture when the ESO checks for updates in external secret stores, when it retrieves updates, and when it synchronizes them to Kubernetes Secrets.
* Timestamps in these logs provide clear and indisputable evidence of the timing of each synchronization, aiding in resolving any disputes or confusion over the timing of updates.

#### Information Disclosure

##### Threat-07-I - Unauthorized access to cluster secrets
An attacker can gain unauthorized access to secrets by utilizing the service account token of the ESO core controller Pod or exploiting software vulnerabilities. This unauthorized access allows the attacker to read secrets within the cluster, potentially leading to a cluster takeover.

##### Threat-07-I Recommended Mitigations
* Service Account Security: Secure the service account associated with the ESO core controller Pod. Implement minimal permissions (principle of least privilege) and regularly audit these permissions.
* Vulnerability Management: Regularly update and patch ESO to address known vulnerabilities. Use vulnerability scanning tools to proactively identify and mitigate potential weaknesses.
* Network Policies and Pod Security: Implement stringent network policies and pod security measures to restrict and control the communication to and from the ESO core controller Pod.

#### Denial of Service (DoS)

##### Threat-08-D - Webhook DOS
Currently, ESO generates an X.509 certificate for webhook registration without authenticating the kube-apiserver. Consequently, if an attacker gains network access to the webhook Pod, they can overload the webhook server and initiate a DoS attack. As a result, modifications to ESO resources may fail, and the ESO core controller may be impacted due to the unavailability of the conversion webhook.

##### Threat-08-D Recommended Mitigations
* Authenticate the kube-apiserver: Ensure that the communication between ESO's webhook and the kube-apiserver is authenticated, preventing unauthorized entities from interacting with the webhook.
* Implement Rate Limiting: Introduce rate limiting on the webhook to prevent it from being overwhelmed by excessive requests.
* Network Security: Strengthen network security policies to restrict access to the webhook Pod, ensuring that only authorized traffic can reach it.

##### Threat-09-D - DDoS attacks on external secret stores
DDoS attacks on external secret stores can overwhelm these services, potentially disrupting their availability. This can impact the External Secrets Operator's ability to synchronize secrets, affecting services that rely on these secrets.

##### Threat-09-D Recommended Mitigations
* DDoS Protection Services: Utilizing DDoS protection services offered by cloud providers or third-party security firms can help mitigate the risk. These services typically include traffic filtering, rate limiting, and anomaly detection to identify and block malicious traffic.
* Resilience and Redundancy: Implementing resilience strategies like load balancing and failover systems ensures that even in the event of a DDoS attack, the secret store remains available. This might include distributing the secret storage across multiple data centers or cloud regions.
* Monitoring and Incident Response: Continuous monitoring for unusual traffic patterns and having a robust incident response plan in place ensures that any DDoS attack can be quickly identified and addressed.

#### Threat-10-D - Man-in-the-Middle (MITM) attack
An adversary could launch a Man-in-the-Middle (MITM) attack to hijack the webhook pod, enabling them to manipulate the data of the conversion webhook. This could involve injecting malicious resources or causing a Denial-of-Service (DoS) attack.

##### Threat-10-D Recommended Mitigations
To mitigate this threat, a mutual authentication mechanism should be enforced for the connection between the Kubernetes API server and the webhook service to ensure that only authenticated endpoints can communicate.

#### Elevation of Privilege

##### Threat-11-E - Exploiting Vulnerabilities for Higher Privileges:
Attackers might identify and exploit existing vulnerabilities in the External Secrets Operator (ESO) or the Kubernetes environment. These vulnerabilities could range from software bugs to insecure configurations, allowing attackers to gain unauthorized access or control.

##### Threat-11-E Recommended Mitigations
* Perform regular updates and apply patches to both ESO and Kubernetes, addressing known security issues.
* Continuously monitor for new vulnerabilities and apply security patches as soon as they are released.

##### Threat-12-E -  Insufficiently Restricted User Roles
In this scenario, users or attackers exploit overly permissive or poorly configured RBAC (Role-Based Access Control) policies in Kubernetes. This can lead to unauthorized access elevation, potentially giving attackers control over sensitive operations or data.

##### Threat-12-E Recommended Mitigations
* Implement and enforce strict RBAC policies, ensuring each role is granted only the minimum necessary permissions.
* Regularly review and audit RBAC configurations to detect and correct any excess permissions, ensuring they align with the principle of least privilege.
* Train administrators and users on the importance of secure role configuration and management.



