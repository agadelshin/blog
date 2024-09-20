+++
title = 'ISO 27001 Part 1'
date = 2024-09-20T22:53:41+02:00
+++

Security audits are always challenging, and they're even harder if you're doing one for the first time. ISO 27001 requires collaboration between the IT department, HR, the CISO and SRE team (or Platform team). I'd like to focus on technological controls, specifically what can be implemented in cloud infrastructure
and how to make our life a little bit easier.

First, preparing for an audit is a long process of collecting evidence and addressing any gaps we found. It's much easier to pass an audit if 
your organization has documentation and a culture of keeping all the docs up-to-date. You probably will have some sort of spreadsheet with a list of controls,
links to documentation related to this control and links to issues if some improvement is required. Ideally, these documents and issues should also link back to the specific control they implement. This approach provides clear traceability, helping you understand the rationale behind certain processes and implementations.

Ofthen, documentation and well-documented processes are more important than perfect implementation. Even if you have some temporary workaround, as long as it's documented, you'll be in a good position. Perfection isn't the goal, but awareness of what's happening in your infrastructure is crucial.

I've gone through this preparation several times, including creating control spreadsheets from scratch for new infrastructure together with CISO/security departments.
It's no secret we use the same infrastructure components everywhere (e.g., Github, Terraform, AWS/GCP/Azure, Kubernetes, etc), like building blocks. What we can do actually is secure those building blocks by default, avoiding the repetitive task of identifying requirements each time.

I reviewed a list of controls to narrow down the scope, and here is a list of what I'd like to focus on:

* Annex A 8.2: Privileged Access Rights
* Annex A 8.5: Secure Authentication
* Annex A 8.6: Capacity Management
* Annex A 8.7: Protection Against Malware
* Annex A 8.8: Management of Technical Vulnerabilities
* Annex A 8.9: Configuration Management
* Annex A 8.10: Information Deletion
* Annex A 8.11: Data Masking
* Annex A 8.12: Data Leakage Prevention
* Annex A 8.13: Information Backup
* Annex A 8.15: Logging
* Annex A 8.16: Monitoring Activities
* Annex A 8.17: Clock Synchronization
* Annex A 8.18: Use of Privileged Utility Programs
* Annex A 8.19: Installation of Software on Operational Systems
* Annex A 8.20: Networks Security
* Annex A 8.21: Security of Network Services
* Annex A 8.22: Segregation of Networks
* Annex A 8.23: Web filtering
* Annex A 8.24: Use of Cryptography
* Annex A 8.26: Application Security Requirements
* Annex A 8.31: Separation of Development, Test and Production Environments
* Annex A 8.32: Change Management

This is still a large scope, and I plan to continue refining it. Some topics will be addressed in general, while others will include specific examples of what needs to be done to achieve success.
