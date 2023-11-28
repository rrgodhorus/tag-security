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

|   |  |
| -- | -- |
| Software | [external-secrets](https://github.com/external-secrets/external-secrets)  |
| Security Provider | Yes |
| Languages | Go, HCL, Makefile, Shell, Smarty, Dockerfile |
| SBOM | SBOM generated using **FOSSA-cli** tool on the latest code base. [Link to SBOM](./docs/REPORT_external-secrets_2023-11-27_193318345Z.spdx.json) |
| | |

### Security links

Provide the list of links to existing security documentation for the project. You may
use the table below as an example:
| Doc | url |
| -- | -- |
| Security file | [Security.md](https://github.com/external-secrets/external-secrets/blob/main/SECURITY.md) |
| Default and optional configs | TBD |

## Overview

One or two sentences describing the project -- something memorable and accurate
that distinguishes your project to quickly orient readers who may be assessing
multiple projects.

### Background

Provide information for reviewers who may not be familiar with your project's
domain or problem area.

### Actors
These are the individual parts of your system that interact to provide the 
desired functionality.  Actors only need to be separate, if they are isolated
in some way.  For example, if a service has a database and a front-end API, but
if a vulnerability in either one would compromise the other, then the distinction
between the database and front-end is not relevant.

The means by which actors are isolated should also be described, as this is often
what prevents an attacker from moving laterally after a compromise.

### Actions
These are the steps that a project performs in order to provide some service
or functionality.  These steps are performed by different actors in the system.
Note, that an action need not be overly descriptive at the function call level.  
It is sufficient to focus on the security checks performed, use of sensitive 
data, and interactions between actors to perform an action.  

For example, the access server receives the client request, checks the format, 
validates that the request corresponds to a file the client is authorized to 
access, and then returns a token to the client.  The client then transmits that 
token to the file server, which, after confirming its validity, returns the file.

### Goals
The intended goals of the projects including the security guarantees the project
 is meant to provide (e.g., Flibble only allows parties with an authorization
key to change data it stores).

### Non-goals
Non-goals that a reasonable reader of the project’s literature could believe may
be in scope (e.g., Flibble does not intend to stop a party with a key from storing
an arbitrarily large amount of data, possibly incurring financial cost or overwhelming
 the servers)

## Self-assessment use

This self-assessment is created by the [project] team to perform an internal analysis of the
project's security.  It is not intended to provide a security audit of [project], or
function as an independent assessment or attestation of [project]'s security health.

This document serves to provide [project] users with an initial understanding of
[project]'s security, where to find existing security documentation, [project] plans for
security, and general overview of [project] security practices, both for development of
[project] as well as security of [project].

This document provides the CNCF TAG-Security with an initial understanding of [project]
to assist in a joint-assessment, necessary for projects under incubation.  Taken
together, this document and the joint-assessment serve as a cornerstone for if and when
[project] seeks graduation and is preparing for a security audit.

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

* Compliance.  List any security standards or sub-sections the project is
  already documented as meeting (PCI-DSS, COBIT, ISO, GDPR, etc.).

## Secure development practices

ESO has achieved a "passing" Open Source Security Foundation (OpenSSF) best practices badge
[![OpenSSF Best Practices](https://www.bestpractices.dev/projects/5947/badge)](https://www.bestpractices.dev/projects/5947). The project is working on receiving a silver badge and is in the process of meeting the criteria for it.

### Development Pipeline

* All source code is publicly maintained in [GitHub](https://github.com/external-secrets/external-secrets).
* Code changes are submitted via Pull Requests (PRs) and must be reviewed and approved by atleast one maintainer.
* Commits to the `main` branch are merged only when a PR is approved and passes all checks.
* Once a pull request has been opened it will be assigned to a reviewer from external-secrets/maintainers.
* ESO uses the Github Bot [Paul the Alien](https://github.com/apps/paul-the-alien) (which is still in Alpha) to store the list of maintainers, to approve/merge PRs and to delete branches after PR merges.
* Raising a PR triggers a series of github actions and workflows whose component checks are broken down below:
  * [Sonar Cloud Quality Gate](https://sonarcloud.io/project/issues?id=external-secrets_external-secrets) check initiated by the sonarcloud bot which checks for bugs, vulenrabilities, security hotspots, code smells, code coverage and duplication
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
  * Pubish signed artifacts to Dockerhub

### Communication Channels
* Referenced in docs under [How to Get Involved](https://external-secrets.io/latest/#how-to-get-involved) and described below:
  * Bi-weekly Development Meeting every odd week at 8:00 PM Berlin Time on Wednesday ([agenda](https://hackmd.io/GSGEpTVdRZCP6LDxV3FHJA), [jitsi call](https://meet.jit.si/eso-community-meeting))
  * [Kubernetes Slack: #external-secrets channel](https://kubernetes.slack.com/messages/external-secrets)
  * [Contributing Process](https://external-secrets.io/latest/contributing/process/)
  * [Twitter](https://twitter.com/ExtSecretsOptr)
* Relevant but not mentioned in the docs:
  * [Github Discussions](https://github.com/external-secrets/external-secrets/discussions)
  * [Github Issues](https://github.com/external-secrets/external-secrets/issues)
* Technical Support policy detailed in [docs/stability-support](https://external-secrets.io/latest/introduction/stability-support/#technical-support)

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