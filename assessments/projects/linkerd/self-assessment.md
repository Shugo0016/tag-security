# Self-assessment
The Self-assessment is the initial document for projects to begin thinking about the
security of the project, determining gaps in their security, and preparing any security
documentation for their users. This document is ideal for projects currently in the
CNCF **sandbox** as well as projects that are looking to receive a joint assessment and
currently in CNCF **incubation**.


For a detailed guide with step-by-step discussion and examples, check out the free
Express Learning course provided by Linux Foundation Training & Certification:
[Security Assessments for Open Source Projects](https://training.linuxfoundation.org/express-learning/security-self-assessments-for-open-source-projects-lfel1005/).


# Self-assessment outline


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
| Software | https://github.com/linkerd/linkerd2/tree/main |
| Security Provider | No |
| Languages | Go, Rust, JavaScript, Shell |
| SBOM | [Linkerd2 SBOM](https://github.com/amanda-gonzalez/tag-security/blob/b985fdda15ced11eeebb9e0cbd8e93a558719f17/assessments/projects/linkerd/linkerd2_linkerd_2d716299a1714728651f2ba7b08478b2f1e54605.json) |
| | |


### Security links


Provide the list of links to existing security documentation for the project. You may
use the table below as an example:
| Doc | url |
| -- | -- |
| Security file | https://github.com/linkerd/linkerd2/blob/main/SECURITY.md |
| Default and optional configs | N/A |


## Overview


Linkerd is a lightweight high-performance service mesh for Kubernetes. It is designed for simplicity and ease of use providing critical features like observability, reliability, and security. It is distinguished as a user-friendly yet powerful solution for modern cloud-native applications.


### Background


Linkerd is an open-source system for automating deployment, scaling, and managing of containerized applications. A service mesh is a layer of dedicated infrastructure that allows for service-to-service communications in a secure, fast, and reliable manner.


The primary issue that Linkerd addresses is the complexity involved in managing communication and network traffic between services in a kubernetes environment. It offers features such as load balancing, service discovery, routing, failure recovery, metrics, monitoring, and secures service communication. Once again Linkerd's focus on simplicity and performance sets it apart in the service mesh domain, hoping to add functionalities with minimal resource expenditure and complexity.


### Actors


**Data Plane**

This consists of lightweight proxies deployed alongside each service instance in a Kubernetes cluster. The proxies intercept and control all network communication between microservices. Thus isolation is achieved since each proxy instance is associated to a specific service, reducing the scope of possible compromises.


**Control Plane**

A set of services that Linkerd proxies interact with. The control plane is responsible for providing service discovery, configuration, and serving certificates for mTLS. Isolation is maintained since the plane operates separately from the data plane, which reduces the risk for a compromised proxy affecting the control system.


**Adminstrative Interface**

This interface includes CLI tools and a dashboard provided by Linkerd. These tools are typically controlled through Kubernetes Role Based Access Control once again isolating adminstrative functions from regular service operations.


### Actions


In the Linkerd service mesh, the key actions that provide its functionality, focusing on security aspects and interactions between different actors, can be summarized as follows:


**Service Discovery:** The control plane provides service discovery information to the data plane proxies. This ensures that microservices can locate and communicate with each other securely and efficiently.


**Traffic Management:** The data plane proxies manage and route traffic between services. This includes load balancing and intelligent routing based on predefined rules or policies.


**Security Enforcement:** Automatic mutual TLS is implemented for secure service-to-service communication. The control plane issues certificates to the data plane proxies, which are then used to encrypt and authenticate traffic.


**Observability:** The data plane proxies collect and report metrics on traffic, including success rates, latencies, and request volumes. This data is accessible through the administrative interface for monitoring and analysis.


**Configuration and Policy Enforcement:** Administrators use the administrative interface to configure policies and rules. The control plane distributes these configurations to the data plane proxies.


**Health Checks and Failover:** The system performs regular health checks of services and implements failover mechanisms to ensure high availability and reliability.


Each of these actions involves careful consideration of security, using isolation, encryption, and access controls to safeguard against unauthorized access and data breaches.


### Goals


The primary goals of the Linkerd project, including its security guarantees, are as follows:


**Secure Service-to-Service Communication:** Linkerd provides automatic mutual TLS to ensure that all communication between services is encrypted and authenticated, enhancing confidentiality and integrity.


**Enhanced Observability:** It aims to give clear visibility into service behavior, performance, and issues, offering detailed metrics and logs.


**Reliability and Resilience:** Linkerd ensures high availability of services through intelligent load balancing, routing, retries, and failovers.


**Simplified Network Operations:** The project is designed to simplify the complexity of managing microservices communications, making it more accessible for operators.


**Performance Efficiency:** Despite offering these features, Linkerd is built to be lightweight and have minimal impact on system performance.


**Open Source and Community-Driven:** As an open-source project, it emphasizes community contributions and transparency in development.


### Non-goals


For Linkerd, the non-goals, which a reader might mistakenly assume are in scope, include:


**Application-Level Security:** Linkerd does not handle application-level security such as input validation, user authentication within applications, or protection against application-specific vulnerabilities.


**Network-Level Security Beyond Service-to-Service Communication:** The focus of Linkerd is on securing internal service-to-service communication within a Kubernetes cluster, rather than providing broader network security solutions like firewalling or intrusion detection.


**Comprehensive Compliance Assurance:** While Linkerd enhances security, it does not guarantee compliance with all regulatory standards (e.g., PCI-DSS, GDPR) on its own.


**Management of Kubernetes Cluster Itself:** Linkerd does not manage Kubernetes cluster operations like node provisioning, cluster scaling, or Kubernetes version management.

## Self-assessment use

This self-assessment is created by the Linkerd team to perform an internal analysis of the
project's security.  It is not intended to provide a security audit of Linkerd, or
function as an independent assessment or attestation of Linkerd's security health.

This document serves to provide Linkerd users with an initial understanding of
Linkerd's security, where to find existing security documentation, Linkerd plans for
security, and general overview of Linderd security practices, both for development of
Linkerd as well as security of Linkerd.

This document provides the CNCF TAG-Security with an initial understanding of Linkerd
to assist in a joint-assessment, necessary for projects under incubation.  Taken
together, this document and the joint-assessment serve as a cornerstone for if and when
Linkerd seeks graduation and is preparing for a security audit.

## Security functions and features

### Critical

**Data Plane**

The data plane is responsible for gathering metrics, observing traffic, and applying policy. It is comprised of sidecar proxies which intercept application network calls and implement rules and logic. Linkerd uses a service mesh specific Linkerd2-proxy, an ultra-light and transparent micro-proxy.

**Control Plane**

The control plane of a service mesh provides the command and control signals required for the data plane to operate. The control plane controls the data plane and provides the UI and API that operators use to configure, monitor, and operate the mesh. The control plane includes three components, destination and identity services and a proxy injector. The destination service fetches policy, discovery, and profile information on requests. The identity service implements mTLS (mutually-authenticated Transport Layer Security) by accepting CSRs and returning signed certificates. Lastly, the proxy injector is a Kubernetes admission controller mutator that adds containers to annotated pods. 

### Security Relevant
**CLI**

The CLI (optional) can be used to interact with the data and control planes. It is run on the local machine, outside of the Kubernetes cluster to interact with Linkerd. 

**Viz Extension**

To access on-stack metrics and CLI tools and dashboards, the viz extension must be installed. This extension installs the following components into the linkerd-viz namespace:

* a Prometheus instance
* metrics-api, tap, tap-injector, and web components

Metrics are stored transiently to limit resources.
The tap tool supports real time analysis of live traffic. This feature can potentially expose sensitive data such as request and response headers. The tap resource used Role-based access control (RBAC) authorization. This method regulates access to individual users within an organization based on their specified role. 

* RBAC API prevents privilege escalation by editing roles or role bindings
* wildcards in resource and verb entries could result in overly permissive access being granted to sensitive resources
* the principle of least privilege, in which minimal rights are applied to users and accounts, should be applied
* granting cluster-admin permissions to the "default" service account in the kube-system namespace contains Secrets that grant super-user access to your cluster's API
* granting super-user access to all service accounts cluster-wide allows any application full access to your cluster, and also grants any user with read access to Secrets (or the ability to create any pod) full access to your cluster


## Project compliance

Linkerd is committed to maintaining a high standard of compliance with industry best practices, security protocols, and community engagement. This section outlines the project's adherence to various standards and initiatives.

Security and Vulnerability Management
1. Vulnerability Reporting:
Linkerd follows a responsible disclosure process. Security vulnerabilities are promptly addressed and documented in the Linkerd Security Advisories, providing users with transparent and detailed information about past vulnerabilities and their resolutions.

2. Continuous Monitoring:
The project employs continuous monitoring mechanisms to detect and respond to potential security threats promptly. Regular security audits and reviews contribute to a proactive approach in maintaining the integrity and security of the codebase.

Community Engagement
1. Open Governance:
Linkerd is committed to an open and inclusive governance model. Decision-making processes are transparent, and community input is actively sought through open forums and discussions. Major decisions and changes are communicated through public channels, ensuring community involvement in the project's evolution.

2. User Feedback and Contributions:
The project values user feedback and contributions. An active community participates in discussions, raises issues, and contributes to the development process. Linkerd maintains a responsive approach to user needs and actively encourages community members to become contributors.

Best Practices and Standards
1. Core Infrastructure Initiative (CII) Best Practices:
Linkerd is actively working towards achieving the Core Infrastructure Initiative (CII) Best Practices badge. The project is aligning its processes, documentation, and security practices with CII standards. Progress can be tracked through the CII Best Practices Program.

2. Coding Standards and Reviews:
Linkerd adheres to a set of coding standards and conducts thorough code reviews. This ensures the quality and security of the codebase. Automated testing, continuous integration, and other development best practices are integral to the project's commitment to delivering reliable software.

Collaboration and Partnerships
Linkerd actively collaborates with other projects, vendors, and the broader ecosystem. This collaboration ensures interoperability, provides users with a wide range of choices, and fosters a healthy and diverse ecosystem around service mesh technologies.

## Secure development practices

### Development Pipeline 

In order to commit to the Linkerd development you have to agree to the Developer Certificate of Origin for each and every commit simply stating you have a legal right to make the contribuition. Linkerd offers 3 ways of doing this Option 1 is to write a git commit message: "Signed-off-by: Jane Smith <jane.smith@example.com>". Option 2: would be a public statement by leaving a comment on the PR with the statement: "I agree to the DCO for all the commits in this PR." Option 3: Linkerd does not require DCO signoff for small changes such as spelling mistakes or one word changes. 

Whenever a pull request is submitted the branch will only be merged when all the configured checks are passed. These include passing the tests in CL, and getting a review from the appropiate Maintainers of Linkerd which are all listed here https://github.com/linkerd/linkerd2/blob/main/MAINTAINERS.md 

Ideally Maintainers usually make decisions by consensus. However, if this is not possible a vote can be called where each maintainer gets a vote and majority win. 

The tests that are included in the Linkerd repo are mostly run through CI, There are multiple different types of tests that include Unit Tests for Go, Javascript, and Shell, ensuring all individual components are functioning correctly. Integration Tests which verify different parts of the application are working together efficiently. Scale tests to check the system under high load conditions. As well as Cloud Provider Tests which ensure compatibility and performance across different cloud environments.   

Linkered uses Buoyant Cloud SaaS automation which provides health monitoring and vulnerability reporting for every Linkerd Cluster. This includes upgrade assistance, which in turn ensures reliability and security of service mesh deployments for all Kubernetes users. 

In the Linkered CI process Linkerd plans to use SNYK which is a tool used to detect and address vulnerabilities in Go dependencies. This will help identify and resolve vulnerabilities in security early in the development process.

The release process of Linkerd goes as follows: 
1. Bump the proxy version
2. Creating branches for minor and major releases 
3. Cherry-picking changes, updating Helm chart verisons, and release notes
4. Managing pull requests and image publishing for testing 
5. Creating release tages and initiating CI workflows
6. Post-release tasks like verification, announcements, and website updates

### Communication Channels 

  * Internal: 
    1. Slack: Linkerd members most likely use slack to communicate between members. 
    2. Github: Collaborative development is done on the github repo, commit messages, issue tracking, and code reviews are all there for members to communicate.
  * Inbound: 
    1. Linkerd Slack: Users can have live conversations and ask quick questions on the workspace. (https://slack.linkerd.io/)
    2. Github Issues: If users want to contribute to the project they can go to the issue marked "help wanted" (https://github.com/linkerd/linkerd2/labels/help%20wanted)
    3. Buoyant's Linkerd Forum: Helps users with troubleshooting, technical questions, and longer-form discussions (https://linkerd.buoyant.io/)
    4. CNCF Linkerd mailing: Users can write emails and reach out for help. (cncf-linkerd-users+help@lists.cncf.io)
  * Outbound: 
    1. Linkered email: Users can sign up to recieve notificatios from linkerd using the following address to sign up (cncf-linkerd-users+subscribe@lists.cncf.io)
    2. Twitter: Users can follow the linkerd twitter account to hear about upcoming events and milestones as well as other new details. (https://twitter.com/Linkerd)
    3. Slack: Announcements are made on the linkerd slack channel

### Ecosystem

  Linkerd fits into the cloud native ecosystem primarily as a service mesh. It offers essential features like load balancing, service discovery, routing, failure handling, and observability for cloud-native applications, primarily those based on Kubernetes
  Some of the keys parts of how Linkerd integrates with the cloud native ecosystem include: 
  1. Kubernetes Native: Linkered is designed primarily for Kubernetes enchacing its networking capabilities. 
  2. CNCF Project: As a part of the Cloud Native Computing Foundation, it aligns with their principles and practices which include Minimum viable Governance, Flexible Governance model, Project Mangement by Mainterners, Neutrality and Open SOurce Community Health. 

## Security issue resolution

As outlined by Linkerd's [Security Policy](https://github.com/linkerd/linkerd2/blob/main/SECURITY.md), security problems are reported to the Security Alert Team for Linkerd by email to determine severity. It is unclear whether this team are maintainers that are obligated to address these issues or if they have any financial affiliations with Linkerd. There is no distinction made between external and internal issues, suggesting that all issues should pass through this communication channel. All confirmations, notifications, and security patches seem to to happen after the email chain between the Security Alert team and the reporter.

Non-severe issues are filed as GitHub issues, while critical issues receive immediate attention. It is unclear who is tasked with addressing these issues, both non-severe and severe, presumably the Security Alert Team. The distinction between severe and non severe issues is not clearly defined, but to be decided by the Security Alert team.

Fixes are propagated to the stable branches as fast as possible, except in cases where multiple issues are found at once. Critical issuses that have ben corrected are announced as a security advisory on Github and to cncf-linkerd-annouce mailing lists, containing a pointer to the fix.



## Appendix

### Known Issues Over Time
Linkerd has maintained a robust track record in addressing and resolving issues promptly. The project's commitment to transparency is evident in its detailed reporting and timely resolutions. For a comprehensive list of past vulnerabilities and their respective resolutions, please refer to the Linkerd Security Advisories.

### CII Best Practices
The Linkerd project is actively aligning itself with the Core Infrastructure Initiative (CII) best practices. To obtain the CII Best Practices badge, the project is currently implementing and refining processes in areas such as security, documentation, and community engagement. For the latest status and details, please visit the CII Best Practices Program.

### Case Studies
**Scenario 1: Microservices Resilience**

One of Linkerd's strengths lies in enhancing microservices resilience. Company X, faced with the challenges of ensuring reliability in their microservices architecture, adopted Linkerd to manage service-to-service communication seamlessly. The implementation resulted in improved fault tolerance and reduced latency, providing a robust foundation for their distributed systems.

**Scenario 2: Cloud-Native Migration**

In a real-world scenario, Organization Y successfully migrated its monolithic application to a cloud-native architecture. Linkerd played a crucial role in simplifying the transition by providing transparent communication between microservices, efficient load balancing, and comprehensive observability. This case highlights Linkerd's versatility in supporting complex migration strategies.

### Related Projects / Vendors
Prospective users often inquire about the differences between Linkerd and alternative projects or vendors. Here are some key distinctions:

**Istio:** While both Linkerd and Istio address service mesh functionality, Linkerd is known for its lightweight design and simplicity, making it an excellent choice for organizations seeking a streamlined service mesh solution.

**Consul Connect:** Linkerd differentiates itself by focusing on the data plane, providing dedicated solutions for service communication. Consul Connect, on the other hand, offers a broader spectrum of features beyond service mesh, including service discovery and configuration management.
