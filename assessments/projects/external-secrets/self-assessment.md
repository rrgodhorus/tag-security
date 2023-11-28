# Self-assessment for External Secrets Operator

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

A table at the top for quick reference information, later used for indexing.


| Software | [A link to the External-Secret's repository.] (https://github.com/external-secrets/external-secrets) |
| Security Provider | No - Facilitator of security |
| Languages | Go, HCL, Makefile, Shell, Smarty, Dockerfile |
| SBOM | SBOM generated using **FOSSA-cli** tool on the latest code base. [Link to SBOM](./docs/REPORT_external-secrets_2023-11-27_193318345Z.spdx.json) |
| | |

### Security links

Provide the list of links to existing security documentation for the project. You may
use the table below as an example:
| Doc | url |
| -- | -- |
| Security file | [Security.md](https://github.com/external-secrets/external-secrets/blob/main/SECURITY.md) |
| Default and optional configs | https://external-secrets.io/latest/guides/security-best-practices|

## Overview
The External Secrets Operator seamlessly bridges Kubernetes with advanced external secret management systems, providing an automated, secure pipeline for syncing sensitive data into cluster environments. It stands out as a key enabler for cloud-native security, transforming complex secrets management into a streamlined, reliable process.

<p align="center"><img width="600" alt="image" src="https://github.com/rrgodhorus/tag-security/blob/ab10047/assessments/projects/external-secrets/docs/overview.png"></p>


### Background
The External Secrets Operator (ESO) is a tool designed for Kubernetes, a widely-used system for automating the deployment, scaling, and management of containerized applications. ESO addresses a key challenge in this domain: secure and efficient management of sensitive configuration data, known as "secrets" (like passwords, API keys, etc.). Typically, managing these secrets within Kubernetes can be complex and risky if not handled properly. ESO simplifies this by integrating Kubernetes with external secret management services (such as AWS Secrets Manager or HashiCorp Vault), which specialize in securely storing and managing these secrets. This integration not only enhances security but also streamlines the process of injecting these secrets into Kubernetes applications.

### Actors

#### External Secret Management Systems
* Examples: AWS Secrets Manager, HashiCorp Vault.
* Isolation: Network boundaries and authentication mechanisms separate these from the Kubernetes cluster.
* Security Note: A breach in these systems does not directly compromise the Kubernetes cluster.

#### Kubernetes Cluster (including ESO)
* Role: ESO integrates Kubernetes and external secret systems.
* Isolation: Kubernetes Role-Based Access Control (RBAC) limits potential lateral movement in case of a compromise.

#### Kubernetes Secrets
* Function: Store synchronized secrets within the cluster via ESO.
* Isolation: Kubernetes namespaces and access policies provide compartmentalization.
* Security Note: A compromise in one namespace doesn’t necessarily expose secrets in another.

  ##### Isolation Mechanisms Overview
  - The isolation between these actors relies on network security, access control mechanisms, and the principle of least privilege.
  - This architecture ensures limited scope of breach, even if one part is compromised.

### Actions
For the External Secrets Operator (ESO), the steps it performs to synchronize secrets from external sources into Kubernetes can be outlined focusing on security checks, data handling, and interactions:

#### Client Request to External Secret Management System
ESO, acting as a client, sends a request to an external secret management system (like AWS Secrets Manager). This request includes authentication and authorization checks to ensure that ESO is permitted to access the requested secrets.
#### Retrieval of Secrets
Upon successful authentication and authorization, the external system sends the requested secrets to ESO. These secrets are transmitted over secure, encrypted channels to ensure their confidentiality and integrity.
#### Validation and Transformation
ESO validates the format and integrity of the received secrets. If necessary, it transforms the data to a format compatible with Kubernetes Secrets.
#### Synchronization to Kubernetes Secrets
ESO then creates or updates Kubernetes Secret objects with the retrieved data. This step involves interacting with the Kubernetes API server, which includes RBAC checks to ensure that ESO has the necessary permissions to perform these operations.
#### Use by Kubernetes Workloads
Applications or workloads running in Kubernetes can then access these secrets. Access to these secrets within Kubernetes is controlled by namespace-specific policies and RBAC, ensuring that only authorized workloads can retrieve them.

Overall, ESO's goal is to provide a secure, efficient, and reliable way of managing secrets in Kubernetes environments, ensuring that only authorized entities have access to sensitive data and that this data is handled securely at all times.
### Goals

#### Secret Management
* ESO is dedicated to the secure management of secrets within Kubernetes, making use of external secret management systems. Kubernetes is able to safeguard sensitive information like passwords, and tokens

#### Access Control
* ESO's access control mechanisms are inherited through the integration of their external secret management systems.

### Non-goals

#### Secret Encryption
* While ESO manages encrypted secrets, it does not provide encryption services itself. The actual encryption and decryption are handled by the external secret management systems.

#### 2. Intrusion Detection or Prevention
ESO does not function as an intrusion detection or prevention system. It does not monitor or protect against unauthorized access within the Kubernetes cluster or the external secret systems.

#### 3. Full Lifecycle Management of Secrets
The primary role of ESO is to synchronize secrets from external systems to Kubernetes. It does not manage the full lifecycle (like creation, rotation, and deletion) of secrets within the external systems.

#### 4. Direct Security Auditing or Compliance Assurance
ESO does not perform security auditing or provide direct compliance assurance. It relies on the security and compliance features of the external secret management systems it integrates with.

## Self-assessment use

This self-assessment is created by the external-secrets team to perform an internal analysis of the project's security. It is not intended to provide a security audit of external-secrets, or function as an independent assessment or attestation of external-secrets's security health.

This document serves to provide external-secrets users with an initial understanding of external-secrets's security, where to find existing security documentation, external-secrets plans for security, and general overview of external-secrets security practices, both for development of external-secrets as well as security of external-secrets.

This document provides the CNCF TAG-Security with an initial understanding of external-secrets to assist in a joint-assessment, necessary for projects under incubation. Taken together, this document and the joint-assessment serve as a cornerstone for if and when external-secrets seeks graduation and is preparing for a security audit.


## Security functions and features

* Critical.  A listing critical security components of the project with a brief
  description of their importance.  It is recommended these be used for threat modeling.
  These are considered critical design elements that make the product itself secure and
  are not configurable.  Projects are encouraged to track these as primary impact items
  for changes to the project.
* Security Relevant.  A listing of security relevant components of the project with
  brief description.  These are considered important to enhance the overall security of
  the project, such as deployment configurations, settings, etc.  These should also be
  included in threat modeling.

## Project compliance

* Compliance.  List any security standards or subsections the project is
  already documented as meeting (PCI-DSS, COBIT, ISO, GDPR, etc.).

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
    * [Contributing Process](https://external-secrets.io/latest/contributing/process/)
    * [Github Discussions](https://github.com/external-secrets/external-secrets/discussions)
    * [Github Issues](https://github.com/external-secrets/external-secrets/issues)
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
* ESO uses GitHub Security Alerts and Dependabot Dependency Updates to learn about critical software updates and security threats.
### Vulnerability Response Process
* No specific information on this is provided in the security file currently.
* Recommendation:
  * Dedicated email for reporting security bugs/vulnerabilities that is monitored 24x7.
  * Acknowledgement email to reporter within a reasonable time span once the process it started.
  * Information on maintainers who are charged with responding to security events (in case an immediate response is required).
  * Include specific timeline/procedures to assign resources and fix a reported vulnerability.
### Incident Response
* Since this project does not directly handle any customer data, any users who experience an incident will report a vulnerability.


## Appendix

* Known Issues Over Time. List or summarize statistics of past vulnerabilities
  with links. If none have been reported, provide data, if any, about your track
  record in catching issues in code review or automated testing.
* [CII Best Practices](https://www.coreinfrastructure.org/programs/best-practices-program/).
  Best Practices. A brief discussion of where the project is at
  with respect to CII best practices and what it would need to
  achieve the badge.
* Case Studies. Provide context for reviewers by detailing 2-3 scenarios of
  real-world use cases.
* Related Projects / Vendors. Reflect on times prospective users have asked
  about the differences between your project and projectX. Reviewers will have
  the same question.