# Security Posture Document

# Contents

- [Security Posture Document](#security-posture-document)
- [Contents](#contents)
- [List of Changes](#list-of-changes)
- [Introduction](#introduction)
  - [Purpose](#purpose)
  - [Audience](#audience)
  - [Scope](#scope)
  - [Related Documents](#related-documents)
    - [Security Requirements Coverage](#security-requirements-coverage)
- [Security measures](#security-measures)
  - [Encryption](#encryption)
    - [Network encryption/SSL Certificates](#network-encryptionssl-certificates)
    - [Storage Encryption](#storage-encryption)
  - [Security Hardening](#security-hardening)
    - [ESXi Hardening](#esxi-hardening)
    - [vCenter Hardening](#vcenter-hardening)
      - [1. **Security Network Policy Enforcement**](#1-security-network-policy-enforcement)
      - [2. **SSO and Password Policy Management**](#2-sso-and-password-policy-management)
      - [3. **Credential Handling and Secure Execution**](#3-credential-handling-and-secure-execution)
    - [AD Security Hardening](#ad-security-hardening)
  - [Security scanning and remediation](#security-scanning-and-remediation)
    - [Malware Protection](#malware-protection)
    - [Nessus](#nessus)
      - [Nessus Compliance Scan \& Reporting Schedule](#nessus-compliance-scan--reporting-schedule)
    - [Endpoint Detection and Response (CrowdStrike)](#endpoint-detection-and-response-crowdstrike)
    - [IT Controls (Alcatraz/ITRION) Scan](#it-controls-alcatrazitrion-scan)
    - [ORADAD](#oradad)
  - [Compliance](#compliance)
  - [Identity Management/Role Based Access Control](#identity-managementrole-based-access-control)
    - [Access reviews](#access-reviews)
    - [Single Sign On (SSO)](#single-sign-on-sso)
  - [Monitoring and Logging](#monitoring-and-logging)
    - [Monitoring](#monitoring)
    - [Security Monitoring](#security-monitoring)
    - [Logging](#logging)
    - [Reporting](#reporting)
  - [Secret store and rotations](#secret-store-and-rotations)
    - [HashiCorp Vault](#hashicorp-vault)
    - [Native Key Provider (vCenter)](#native-key-provider-vcenter)
    - [Password Rotation](#password-rotation)
  - [Patch and update management](#patch-and-update-management)
    - [Windows Patching](#windows-patching)
    - [Linux Patching](#linux-patching)
    - [Template Patching](#template-patching)
    - [Appliance Patching](#appliance-patching)
  - [Network communication management](#network-communication-management)
    - [Zero-Trust Micro Segmentation Firewall implementation](#zero-trust-micro-segmentation-firewall-implementation)
    - [Internet Access](#internet-access)
  - [Processes and Quality Gates](#processes-and-quality-gates)
    - [Product Release quality gate - Turn over to Service (TOS)](#product-release-quality-gate---turn-over-to-service-tos)
    - [Production RollOut - Turn over to Production (TOP)](#production-rollout---turn-over-to-production-top)
    - [Production Plan](#production-plan)
  - [Tabular Description](#tabular-description)
  - [Reference Links](#reference-links)
  - [Appendix](#appendix)
    - [Appendix A - Abbreviations](#appendix-a---abbreviations)

# List of Changes

The following changes have been made to this document.

| Date       | Change Detail                                      | Author         |
| ---------- | -------------------------------------------------- | -------------- |
| 19-09-2024 | Initial Security Posture document                  | Aswin Arumugam |
| 12-11-2024 | Document restructure, replacement of abbreviations | Oliver Scholle |
| 04-12-2024 | Document update with Proper Product names          | Aswin Arumugam |
| 29-01-2025 | Document update to cover SOXDB users access reviews | Tomasz Korniluk |
| 03-06-2025 | Rework document and add Hardening of vCenter and ESXi for DHC 2.0 onwards | Radoslaw Dabrowski |
| 24-02-2026 | VCS-15538 Add Security Requirements Coverage                               | Przemyslaw Pakula  |
| 05-03-2026 | Add CrowdStrike EDR section and differentiate Nessus deployment models (single-tenant vs multi-tenant) | Radoslaw Dabrowski |

# Introduction

## Purpose

The purpose of this document is to provide an holistic view about the various security related details for different components in the VMware Cloud Services (VCS) product.

## Audience

This is intended to Auditors/Customers for getting better understanding of the security concepts and components contained in the VCS product.

## Scope

The document includes various components like vCenter, Active Directory (AD), etc which are part of the VCS Product and are relevant for security design implementations.

## Related Documents

### Security Requirements Coverage

| Instruction Name | Short Description |
| :----------: | ------- |
| [lldADSecurityEnhancement2024.md](lldADSecurityEnhancement2024.md) | Describes AD vulnerabilities in VCS and the remediation actions for key security findings. |
| [lldDhcRoleBasedAccessControl.md](lldDhcRoleBasedAccessControl.md) | Defines RBAC roles, mappings, and access review principles for VCS components. |
| [lldBreakTheGlass.md](lldBreakTheGlass.md) | Defines emergency access workflows for outage scenarios and recovery procedures. |
| [lldHardening.md](lldHardening.md) | Defines required hardening activities before production handover, including identity, firewall, and compliance controls. |
| [lldHashicorpVault.md](lldHashicorpVault.md) | Describes secure secret-management architecture, authentication methods, and audit logging. |
| [lldVulnerabilityManagement.md](lldVulnerabilityManagement.md) | Defines Nessus-based vulnerability scanning design, scope, and operating model. |
| [lldSecurityPosture.md](lldSecurityPosture.md) | Provides a consolidated overview of VCS security controls across encryption, scanning, RBAC, logging, and patching. |
| [SecurityMeasureExceptions.md](SecurityMeasureExceptions.md) | Lists approved Nessus/Alcatraz exceptions and false positives with rationale and mitigation context. |
| [SiemensCERTExceptions.md](SiemensCERTExceptions.md) | Lists Siemens CERT exceptions/false positives with applicability and risk/mitigation notes. |
| [lldSOXDB.md](lldSOXDB.md) | Describes SOXDB integration security controls, including credential handling, encryption, and RBAC. |
| [lldRemoteConsoleAccess.md](lldRemoteConsoleAccess.md) | Defines secure remote console access controls, including RBAC and certificate handling. |

# Security measures

The product applies common industry best practices to satisfy foundational compliancy to Atos internal Security compliance rules and providing a solid foundation to achieve external security certifications with out of the box delivered configurations. Standard product security level can be further more improved by addon/integration into external security service proved by Atos / Eviden including but not limited to Intrusion Detection/Preventions, Security Information and Event Management (SIEM), regular penetration testing or Ransomware Protection

## Encryption

Strong encryption methods are implemented on storage level to prevent data theft from physical disks used inside the product and for all network communications happening between Management and workload platform components

### Network encryption/SSL Certificates

SSL certificates are crucial for securely delivering content over the web. They ensure that connections, both within components and externally, remain safe at all times. All our appliances and applications in our management stack uses signed certificates for communication.

VCS employs SSL signed certificates in appliances such as vCenter, the Aria Suite (including Aria Automation and Aria Operations), HashiCorp Vault, and NSX to ensure secure transmission and API connections. This ensures that the VCS product deployment automation framework operates smoothly and securely.

Product management stack includes internal Certificate Authority (CA) server for automated creation and rollout of trusted certificates.

Automated monitoring and reporting ensures refresh of certificates before expiration date.

The SSL Certificates are auto-rotated before their time of expiry and is applied to each component.

Certificates of the VMware Cloud Foundation (vCF) components are maintained inside VMware Aria Suite Lifecycle(vRSLCM).

VCS product deployment automation framework helps us in creating, rotating and applying the certificate to the components.

Certificate backups are placed in HashiCorp Vault. Backup will be utilized to safeguard the Vault data. This backup is external and authenticated independently, ensuring that data can be recovered even if issues arise within the VCS itself.

**CEB - Canopy Enterprise Backup** is used.

**Role Based Access Control(RBAC):**

Access to ICA server is restricted and hardened. *Even administrator account login is disabled to make it secure.*

*Only the users having permission to the folder in HashiCorp Vault is allowed to copy the Certicate.*

[**Certificate Renewal**](#certificate-renewal)

### Storage Encryption

Data-at-Rest Encryption (DARE) is enabled by default in VCS to secure stored data on VMware vSAN. This feature, available starting from vSAN 7.0, provides encryption at the per-datastore level. As the name implies, data is encrypted when stored and decrypted upon access. This means DARE does not protect data in transit.

In earlier VCS versions, vSAN encryption relied on an external, third-party Key Management Server (KMS) supporting the Key Management Interoperability Protocol (KMIP) to provide encryption key services.

Starting with VCS 2.0, the external KMS has been replaced by the Native Key Provider (NKP) — a native key management solution integrated directly into vCenter. NKP eliminates the need for an external KMS while maintaining compliance with industry standards and ensuring secure key handling.

vSAN encryption applies to both Management and Workload VMs, securing all cache and capacity devices in the vSAN datastore. In addition, vSAN host core dumps are also encrypted to prevent sensitive data exposure during support operations or crash diagnostics.

vSAN encryption uses the following keys:

1) Key Encryption Key (KEK)
   - Managed by NKP (or KMS in earlier versions)
   - AES-256 key used to encrypt the Data Encryption Keys (DEKs)
   - One KEK per vSAN cluster (per-tenant basis)
2) Data Encryption Key (DEK)
   - XTS-AES-256 key used in the I/O path for encrypting and decrypting data
   - One DEK per disk in each vSAN disk group
3) Host Key
   - AES-256 key used to encrypt vSAN host core dumps
   - Shared across all hosts within a vSAN cluster

By adopting NKP from VCS 2.0 onward, VCS improves its security posture while simplifying key management operations and reducing infrastructure dependencies.

More information can be read in [**Certificate Renewal**](#certificate-renewal).

## Security Hardening

Our VCS product automation framework is designed to harden servers and appliances in accordance with Atos and Broadcom standards. This ensures that all components and layers in our infrastructure are security-hardened by default when deploying a new VCS. By doing so, we ensure our underlying infrastructure is secure and protected from external hackers. The document below covers the design decisions related to hardening VCS.

[**Hardening**](#hardening)

### ESXi Hardening

The VCS product automation framework enforces ESXi hardening based on the Atos TSS security baseline. This is achieved through infrastructure-as-code practices, using Ansible to systematically apply the following configurations:

- **Security Banner Configuration**: Login banners are configured for both SSH and ESXi Shell to enforce legal notice and access disclaimers.

- **Advanced Settings Enforcement**: A predefined set of ESXi advanced settings is applied to all cluster hosts. These settings include account lockout policies, shell timeouts, session timeouts, syslog forwarding configuration, memory management, and MOB access control, all in line with Atos TSS guidelines. Examples include:
  - Security.AccountLockFailures: 3
  - UserVars.ESXiShellTimeOut: 900
  - Syslog.global.logHost: ssl://\<syslog-host-LogInsight\>:1514

- **Security Acceptance Level**: The ESXi host acceptance level is set to PartnerSupported (as required by MeasureID VE800018). This ensures only certified VIBs from trusted vendors are installed. For environments such as VCS 2.0, the acceptance level is instead removed to comply with platform-specific requirements.

- **Disabling Unnecessary Services**: Security-related services that are not required operationally are stopped on all ESXi hosts. This reduces the attack surface and limits potential vectors for compromise.

Each configuration change is applied automatically and validated per cluster during deployment. Below is the corresponding Ansible logic implementing these hardening actions:

- *vmware_host_config_manager* for setting advanced parameters
- *vmware_host_acceptance* for adjusting acceptance level
- *vmware_host_service_manager* for stopping unneeded services

These actions are repeatable, idempotent, and version-controlled within the infrastructure repository.
In addition to configuration hardening, the VCS framework supports:

- Continuous application of security updates and patches
- Logging and monitoring integration
- Enforced role-based access control (RBAC)

Together, these measures establish a secure and compliant ESXi baseline across all managed clusters.

View detailed configuration in this document [**ESXi Hardening**](#esxi-hardening).

### vCenter Hardening

The VCS product automation framework includes a dedicated playbook to harden and remediate the vCenter 8 Appliance in alignment with the Atos TSS security baseline. It performs a comprehensive set of tasks across three main areas:

#### 1. **Security Network Policy Enforcement**

The automation applies strict Distributed Switch (VDS) security settings using PowerCLI. The following policies are enforced across all applicable vCenter servers:

- **Forged Transmits**: Rejected
- **MAC Address Changes**: Rejected
- **Promiscuous Mode**: Rejected
- **Port-Level Overrides**: Blocked

These measures ensure standardized and secure network behavior across the virtual infrastructure.

#### 2. **SSO and Password Policy Management**

The framework configures the vCenter **Single Sign-On (SSO)** settings and enforces password policies via hardened PowerCLI scripts. It also:

- Sets **root password expiration** to 365 days
- Configures an **email address** for root password expiry notifications
- Retrieves and logs the current root password policy
- Enables and disables **SSH access** to the appliance securely during the operation

#### 3. **Credential Handling and Secure Execution**

All privileged credentials (vCenter and root) are securely retrieved from **HashiCorp Vault**. Actions requiring root access are performed over the vCenter appliance shell using elevated permissions.

[**vCenter Hardening**](#vcenter-hardening)

### AD Security Hardening

Active Directory is security-hardened according to Atos Standards. After deployment, the VCS product automation framework is executed to further harden the AD. This process includes enforcing strict access controls, implementing advanced security configurations, and addressing any identified vulnerabilities. Additionally, regular security scans and audits are conducted to ensure ongoing compliance with security policies.

[**AD Security Enhancement**](#ad-security-enhancement)

## Security scanning and remediation

The VCS product includes various tools pre-installed for scanning environments for vulnerability & compliance. Found vulnerabilities are considered in permanent product lifecycle management upgrades.

### Malware Protection

VCS integrates with the Trend Micro Deep Security service, managed by the Big Data and Security (BDS) team. During the deployment stages, VCS automates the installation of the antivirus (AV) agent on all Windows and Linux management virtual machines.

[**Anti Virus Onboarding**](#antivirus-onboarding)

[**Virtual Machine Build**](#virtual-machine-build)

### Nessus

Nessus enables us to scan both Linux and Windows operating systems and report any vulnerabilities.

Vulnerability scans run against all VCS servers that reside on Management, Cross Region and Local Region networks. (Customer workload machines will not be included in scans).

Vulnerability scan will run on demand during hardening.

All scans are scheduled to run every 7 days.

**Deployment Models:**

The vulnerability scanning infrastructure varies based on platform deployment type:

- **Single-tenant environments:** Standalone Nessus scanner (`nes001`) with local reporting and manual report delivery
- **Shared/Multi-tenant environments:** Tenable Nessus Proxy (`nes002`) integrated with ASN Central Tooling for centralized visibility and automated reporting

For detailed technical specifications, network requirements, and configuration details, refer to [Vulnerability Management LLD](lldVulnerabilityManagement.md).

#### Nessus Compliance Scan & Reporting Schedule

| Component                                                                                              | Value                             | Description (optional)                       |
|--------------------------------------------------------------------------------------------------------|-----------------------------------|----------------------------------------------|
| Cron job on Nessus server to run Alcatraz compliance scan against Linux and Windows management VMs     | Scheduled every Monday at 04:00   | Execution time of cron task on Ansible node  |
| Scheduled curl batch command on Nessus server to upload Alcatraz reports to ITC DB                     | Scheduled every Monday at 11:00   | Execution time of cron task on Ansible node  |
| Scheduled scan of the entire management IP network range                                               | Scheduled every Sunday at 20:00   | Default scan jobs scheduled via Nessus GUI   |
| Scheduled scan of the entire cross-region IP network range                                             | Scheduled every Sunday at 20:00   | Default scan jobs scheduled via Nessus GUI   |
| Scheduled scan of the entire local region IP network range                                             | Scheduled every Sunday at 20:00   | Default scan jobs scheduled via Nessus GUI   |
| Cron job on Nessus server to convert and send reports                                                  | Scheduled every Thursday at 04:00 | Cron scheduled reporting (via Ansible)       |

### Endpoint Detection and Response (CrowdStrike)

**Applicability:** Shared/multi-tenant platforms only

CrowdStrike Falcon provides advanced Endpoint Detection and Response (EDR) capabilities for VCS management infrastructure. The lightweight agent is deployed on Linux and Windows management servers, performing AI-based behavioral threat detection and analysis.

**Key Features:**

- Real-time threat detection using AI-based behavioral analysis
- Automated incident response and remediation
- Cloud-based processing with minimal agent footprint
- Automatic agent and signature updates

**Incident Management:**

Security incidents detected by CrowdStrike are managed through an automated workflow:

1. Threat detection triggers automatic PISA ticket creation for the Security Team
2. Security Team coordinates with platform Support Groups and CI Owners
3. Business application owners are notified for major incidents

Incident handling and security compliance are managed centrally by the Atos Security Team, with no direct platform team intervention required for day-to-day operations.

**Note:** ESXi hosts and appliances are registered in CMDB for incident tracking but do not have agent installation.

For detailed technical specifications, network requirements, firewall rules, and CMDB registration process, refer to [Vulnerability Management LLD - Section 3.8](lldVulnerabilityManagement.md#38-crowdstrike-endpoint-detection-and-response-edr).

### IT Controls (Alcatraz/ITRION) Scan

Will be used to provide compliance scan. Alcatraz compliance scanner will be installed only on Windows and Linux Management servers. Alcatraz is not responsible for compliance scan of customer workload machines.

Compliance scanner won’t be installed in virtual appliances.

Compliance reports are uploaded to ITC Prod (ASN) on weekly basis.

Compliance of VMware components will be measured by VMware Aria Operations (AOps).

### ORADAD

This tool helps us to scan our Active Directory.

Active Directory Security best practices hardening measures are evaluated on a monthly basis on selected product instances via ORADAD scan. There is a schedule in place which will scan the Active Directory and report the vulnerability if any.

[**Vulnerability Management**](#vulnerability-management)

## Compliance

The VCS product adheres to Atos standards and Broadcom settings to ensure compliance with various appliances and applications. These settings are enabled, and comprehensive scans are performed to verify that all systems and appliances meet the required standards. The scanning process is conducted exclusively on Management VMs, where it includes:

- **Vulnerability Assessment:** Identifying and reporting potential security vulnerabilities within the system.
- **Configuration Compliance:** Ensuring that system configurations align with predefined security policies and standards.
- **Patch Management:** Checking for missing security patches and updates to maintain system integrity.
- **Access Control Review:** Verifying that access controls are properly implemented and enforced.
- **Log Analysis:** Reviewing system logs for any unusual or suspicious activity that could indicate a security threat.

By performing these scans, we ensure that our infrastructure remains secure and compliant with industry standards.

Whenever there is a new VCS environment is deployed the VCS product deployment automation framework will take care of the compliance.

Security Compliance Standards to be followed:

1. Alcatraz Non compliant measure remediation - [Alcatraz](#it-controls-alcatrazitrion-scan)
2. Nessus Vulnerability - [Nessus](#nessus)
3. Customer Specifics - Siemens CERT (Siemens Specific)
4. Enhance VCS Security Measures Remediation - [Security Hardening](#security-hardening)

[**Nessus Remediation**](#nessus-remediation)

## Identity Management/Role Based Access Control

Least privilege access granting is a common best practice supported by Pre-defined roles implemented in Active Directory allowing automated assignment of those role access levels to different adminstrator groups created based on split of responsibility and resulting required access rights across all platform included compoents.

Role-Based Access Control (RBAC) is enabled on all VMs and appliances within the Management Domain. This is crucial for maintaining high security standards. The following section highlights the importance of RBAC in these components.

1. HashiCorp Vault - [HashiCorp Vault](#hashicorp-vault)
2. VMware Aria Operations - [Monitoring](#monitoring) & [Reporting](#reporting)
3. Vulnerability Tools - [Security scanning and remediation](#security-scanning-and-remediation) & [Compliance](#compliance)

### Access reviews

Regular and automated export of user access rights granted and comparison to Atos employee database is supported by Atos SOXDB and N3View. By default, access reviews are executed on a quarterly basis and SOXDB scan reports are stored as encrypted files inside Ansible controller.

1. [**RBAC**](#rbac)  
2. [**SOXDB Users Access Review**](#soxdb)

### Single Sign On (SSO)

Single sign on is enabled in most of the appliances deployed in the VCS. This allows us to connect securly from an single authenticated source.

**vCenter:** It has its own internal authentication/identity source. The Active Dirctory domain is added for the authentication.

**Workspace ONE Access(WS1) :** Broadcom's Identity manager(Workspace ONE Access) helps us in enabling the SSO authentication for its appliances. Active Directory is connected in the Workspace ONE Access(WS1) appliance.

1. VMware Aria Suite Lifecycle
2. VMware Aria Automation
3. VMware Aria Operation
4. VMware Aria Operations for Logs
5. VMware NSX

**Login:**

For OS login VCS used the below secured methods

Linux: SSSD(System Security Services Daemon) Service is used for authenticating linux servers. The users who are part of the approved user-groups are only allowed to login to the servers. This permission can be set only by the Administrator.

Windows: Login to windows servers are directly through Active Directory domain join. Users with proper permission assigned would be able to login to the servers. Only Admin can provide permissions for the users.

After the AD hardening is performed in Active Directory only "ADM" accounts will have login permissions. And also restricting the access to ICA servers to protect the certificates.

GPO is set in these servers to implement the security standards.

> ***NOTE:*** The password for these admin accounts are stored securely in the vault and only few super users have permission to retrieve it.

## Monitoring and Logging

### Monitoring

In VCS, VMware Aria Operations is used to monitor various components deployed within the VCS. Alert configurations are enabled for each component to ensure timely notifications.

The Telegraf (VMware Aria Operations) agent is installed on client servers and applications. The VCS product automation framework ensures the deployment and functional monitoring of all endpoints within the product management framework.

Similar to VMware Aria Operations for Logs, VMware Aria Operations monitors various components in the VCS:

1. The resources of the workloads deployed (Linux and Windows servers) are monitored.
2. VCF Components - Using VMware Aria Operations Manangement pack adapters.
3. Hardware Monitoring (Bull & DELL).
4. Collects all the events from VMware Aria Operations for Logs and triggers alert.
5. HashiCorp Vault

VMware Aria Operations default out-of-the-box alert definitions used.

VMware Aria Operations alerts retention will stay as a default 45 days.

All available alerts from VMware Aria Operations for Logs will be forwarded to VMware Aria Operations, but only Immediate and critical alerts to be transferred to IT Service Management (ITSM). Along with this optionally cloud operations teams would be able to decide if some additional warnings should be transferred to IT Service Management (ITSM).

Certificate Authority will provide the signed certificates for the VMware Aria Operations cluster.

Backup will be utilized to safeguard the monitoring stack. This backup is external and authenticated independently, ensuring that data can be recovered even if issues arise within the VCS itself.

**CEB - Canopy Enterprise Backup** is used.

**RBAC** - There are various set of roles which will determine the access to the objects which the particular user owns.

[**Monitoring**](#monitoring)

### Security Monitoring

Foundational knows security related events discovered by monitoring and logging components (e.g. reoccurring failed login attempts) will result in Incidents flagged as security incidents inside Atos IT Service Management (ITSM). Advanced security event monitoring services and integrations can be offered by Atos as addon services, but are not provided within default product scope or cost base.

### Logging

VMware Aria Operations for Logs: Logs from various appliances/servers are forwarded to VMware Aria Operations for Logs.

VMware Aria Operations for Logs agent is installed in all the client servers/application. This is done via the VCS product deployment automation framework in all the client servers.

Various components forwarding the logs:

1. Linux and Windows servers for OS log forwarding.
2. VCF Components - Using VMware Aria Operations for Logs Manangement pack.
3. Application/Appliance Services are integrated to VMware Aria Operations for Logs.
4. Hardware logs (Bull & DELL).
5. HashiCorp Vault.

VMware Aria Operations for Logs, log retention is generally 1 month which is set as per Atos policy.

Certificate Authority will provide the signed certificates for the VMware Aria Operations for Logs cluster

Backup will be utilized to safeguard the logging appliances. This backup is external and authenticated independently, ensuring that data can be recovered even if issues arise within the VCS itself.

**CEB - Canopy Enterprise Backup** is used.

> ***NOTE:*** Logging enabled will look out for any suspicious logins or multiple login alert or any middle man attack would be reported.

[**Logging**](#logging)

### Reporting

There are various reports generated for the servers and appliances in the VCS. VMware Aria Operations appliance is used for reporting purposes.

Within the VMware Aria Operations there are various dashboards created which will depict the actual picture of the Servers/Appliances in terms of the resources and performance.

By default all the VMs present in Management and Workload domain are visible. VCS team install telegraf agent only in the VMs present in the Management vCenter. Workload vCenter VMs are managed by customer.

There are other dashboards hosted (reports) from other tools in VMware Aria Operations:

1. Patching Report
2. Nessus Scan
3. Alcatraz scan
4. Password Expiration
5. Expiring Certificates Reports
6. AD user reports

> ***NOTE:*** Any abnormality in the scanning reports or the expiration will be reported immediately and action would be taken upon it.

vRealize Service Now notification plugin will be enabled and used in VCS. Ticket will be raised for Critical alerts and sent to the respective team.

**RBAC:**

There will be a dedicated AD groups used for the Lightweight Directory Access Protocol(LDAP) authentication method. Customers will have access only to VMware Aria Operations reporting

There are three groups created for RBAC

1. Power User Rights - For all vCenters except the management
2. Read Only Rights
3. Dashboard Viewing Rights

[**Reporting**](#reporting)

## Secret store and rotations

### HashiCorp Vault

VCS product has a HashiCorp vault present in all the releases.

1. Credentails: All the passwords/credentials of the appliances/servers are stored safely inside it with proper policy(RBAC) enabled for it. It also contains passwords of the templates which are used to build servers.

2. SSL Certificates: The SSL Certificates are also stored inside the vault. Whenever there is a new certificate generated for that appliance or server crt and key file is stored in the vault for future reference purpose.

The data stored in Vault is encrypted. Vault needs the encryption key in order to decrypt the data.
Vault is using audit devices to keep a detailed log of all requests and response to Vault. As every operation with Vault is an API request/response, the audit log contains every authenticated interaction with Vault, including errors.
With a few specific exceptions, all strings (including authentication tokens and lease information) contained within requests and responses are hashed with a salt using HMAC-SHA256

**RBAC:**  By default there are various policies set during deployment which will determine who can access the objects(credentials,certificate etc) from the HashiCorp Vault.

Backup will be utilized to safeguard the Vault data. This backup is external and authenticated independently, ensuring that data can be recovered even if issues arise within the VCS itself.

**CEB - Canopy Enterprise Backup** is used.

[**HashiCorp Vault**](#vault)

### Native Key Provider (vCenter)

VMware vSAN storage encryption keys are stored inside NKP component provided by VMware vCenter and cached inside each ESXi host TPM module

### Password Rotation

Password rotation is one of the key factors that is maintained in VCS for security purpose. The expiration date is usually set to 42 Days. This is set using a GPO in the AD server.

The VCS product automation framework scans the AD to find users which are clos to expiry(14 days or less) and resets the passwords for those accounts. By default, it also rotates the password automatically for every 30 days (12 days before the expiry). It also updates the latest password in the HashiCorp Vault. Service account used for various integration(Application/Appliance) is then updated.

It sends a mail notifications to all users with 7,3,2,1 and 0 days until password expiry. If no email is configured for AD user notification will be sent to VCS Team group mailbox instead

A report is generated for the accounts which were involved in the password rotation. It is then cross verified manually as well to verify if all the accounts has the updated password.

[**Password Rotation WI**](#password-rotation)

## Patch and update management

Update of the servers are done through patching. Patching is scheduled every month once. Once patching is done there is a scan done for all the servers again for vulnerability and ensure it is a green signal.

### Windows Patching

WSUS - Wsus server is used for patching the windows server using VCS product deployment automation framework. All the Windows servers are connected to WSUS servers which pushes the latest packages to the client windows server.

[**Windows Patching**](#windows-patching)

### Linux Patching

Debian Repository (DEB Repo) - Linux VMs are patched using a repository created on DEB servers, which contains the latest packages from the Ubuntu source. Linux client servers are configured to use this DEB server as their Apt sources list.

When the VCS product deployment automation framework is triggered, it updates all the packages on the Linux servers.

[**Linux Patching**](#linux-patching)

### Template Patching

Keeping templates patched and up to date is essential for security. In VCS, templates are frequently updated and stored in an S3 bucket, which acts as a global image across all VCS environments. This guarantees that the most recent and secure templates are always utilized when deploying VMs.

[**Windows Template Patching**](#windows-template)

[**Linux Template Patching**](#linux-template)

### Appliance Patching

This is done through VMware Aria Suite Lifecycle. If there are any minor patch releases(Security or Vulnerability fixes) for the appliances then VCS product automation framework would be apply that patch. The latest packages are downloaded and then applied by the VMware Aria Suite Lifecycle in a fully automated fashion.

1. VMware Aria Automation
2. VMware Aria Operations
3. VMware Aria Operations for Logs
4. WorkSpace ONE Access
5. VMware Aria Operations for Networks
6. VMware Aria Automation Orchestrator

**Async Patch Tool**

Apply critical patches to certain VCF components (VMware NSX-T Manager *, vCenter Server, and ESXi) outside of VMware Cloud Foundation releases.

1. Applying critical patches for vCenters and ESXi hosts.
2. Enabling upgrade to a later version of VMware Cloud Foundation.

[**Async Patch Tool**](#async-patch)

**Appliance Upgrade**

The VCF Appliance upgrade is done through VMware Aria Suite Lifecycle. VCS product automation framework upgrades the appliances to the latest major releases. This is usual done when there is a major upgrade in the VCS environment itself.

1. VMware Aria Automation
2. VMware Aria Operations
3. VMware Aria Operations for Logs
4. Workspace ONE Access

**OS Upgrade**

Both Linux and Windows OS are upgraded to the latest version during major releases of VCS. And also it is upgraded whenever there is no other minor releses in the current version. If the current version patch upgrade does not cover the vulnerabilities reported, that will also be a driving factor for an upgrade. The un-supported OS which are not officially supported is not kept in the VCS.

- Linux - Ubuntu Flavour
- Windows - Microsoft Windows

## Network communication management

To minimize internal and external communications and prevent lateral movement in the event of a security breach, product internal network traffic flow and filtering are applied. This firewall implementation is carried out for VMs and appliances in both management and workload vCenters. The Cloud Network Team typically handles the workload domain, and two NSX instances are used to manage these environments.

- Nsx001 - Management vCenter
- Nsx002 - Workload vCenter

### Zero-Trust Micro Segmentation Firewall implementation

Secure management domain with VMware NSX Distributed Firewall. Deny all traffic except those defined and approved in the network design.

Secure management components on compute workload domain with VMware NSX-T (NSX-Transformers) Distributed Firewall. Deny all traffic except those defined and approved in the network design.

Secure Customer workload on compute workload domain with VMware NSX-T (NSX-Transformers) Distributed Firewall. All ruleset created in front of migration/creation of Customer workload.

### Internet Access

Internet access within the VCS Product is restricted, with whitelisted external and outgoing connections filtered through internal proxy servers. If any server or appliance needs to connect to an external site via the proxy, the external domain must be whitelisted within the proxy servers. This ensures that other servers are not directly exposed to the internet and remain protected.

Only proxy administrators have the authority to perform this whitelisting.

## Processes and Quality Gates

### Product Release quality gate - Turn over to Service (TOS)

Before any new feature is implemented in production, it undergoes rigorous testing for both functionality and security. The Atos Operational Team tests the developed code in a testing environment that closely mirrors production. They scan it for vulnerabilities and compliance. Only after passing all security checks is the feature approved for production deployment.

### Production RollOut - Turn over to Production (TOP)

When a feature is production ready VCS product automation framework does the hardening and it is subjected to many scans(vulnerability) done and the reports are generated. Unless the reports are green the feature is not pushed to production.

[**Hardening**](#hardening)

[**Reporting**](#reporting)

Linux and Windows management VMs must be patched before Turn over to Production. Once it is patched and hardened there is another round of scanning for Vulnerabilities and compliance is done.

[**Patching**](#patching)

### Production Plan

The VCS production environment follows a structured and automated operational plan to ensure stability, compliance, and security. This plan defines recurring tasks executed on a **Daily**, **Weekly**, **Monthly**, **Quarterly**, and **Yearly** basis by the operations team or through automation.

Key activities include:

- **Daily** such as:
  - Monitoring infrastructure health
  - Validating log collection
  - Checking vCenter, NSX, and VxRail cluster status

- **Weekly** including:
  - Capacity planning reports
  - Compliance validations (e.g., AV, RVTools, vSAN)
  - Visibility checks on portals like Alcatraz and TOSCA

- **Monthly** addressing:
  - Functional account checks
  - Credential and token expiry reviews
  - OS patching (Linux, Windows, Global Image)
  - RBAC access reviews
  - License utilization analysis

- **Quarterly** such as:
  - Password rotation for VCS components
  - SOXDB user access reviews

- **Yearly** covering:
  - SSL certificate expiry reporting
  - Remediation of expired certificates

For a full list of scheduled activities and their associated work instructions, refer to the documentation [**Production Plan**](#production-plan).

## Tabular Description

The security details for each component is distributed into two tables which are shown below.

**Table 1.0:**

| Tools | Clustering | Login | RBAC | Secrets - Vault | Patching | VMware Aria Suite Lifecycle | Monitoring | Logging |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Active Directory | ✔ | ✔ | ✔ | ✔ | ✔ | ❌ | ✔ | ✔ |
| | 2 Nodes | Domain User | Domain Admin | | Playbook - WSUS | | VMware Aria Operations | vRLI |
| Windows | ✔ | ✔ | ✔ | ✔ | ✔ | ❌ | ✔ | ✔ |
| | | Domain Joined | Domain Members | | Playbook - WSUS | | VMware Aria Operations | vRLI |
| Linux | ✔ | ✔ | ✔ | ✔ | ✔ | ❌ | ✔ | |
| | | Domain Joined | SSSD Policies | | Playbook - DEB | | VMware Aria Operations | |
| ESX | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ | |
| | vCenter Cluster | | | | ? | Through SDDC Manager | VMware Aria Operations | |
| vCenter Server | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ | ✔ | |
| | Linked vCenter | AD Integrated | vCenter Roles | | ? | Through SDDC Manager | VMware Aria Operations | |
| VMware Aria Operations for Logs | ✔ | ✔ | ✔ | ✔ | ❌ | ✔ | | |
| | 2 Nodes | Through Workspace ONE Access | Roles | | | Through VMware Aria Suite Lifecycle | | |
| SDDC Manager | ❌ | ✔ | ✔ | ✔ | ❌ | ✔ | | |
| | | AD Integrated | Roles | | | Through SDDC Manager | | |
| VMware Aria Operations | ✔ | ✔ | ✔ | ✔ | ❌ | ✔ | | |
| | 2 Nodes | Through Workspace ONE Access | Roles/Scope | | | Through VMware Aria Suite Lifecycle | | |
| VMware Aria Suite Lifecycle | ❌ | ✔ | ✔ | ✔ | ❌ | ✔ | | |
| | | Through Workspace ONE Access | Roles | | | Through VMware Aria Suite Lifecycle | | |
| Workspace ONE Access | ❌ | ✔ | ✔ | ✔ | ❌ | ✔ | | |
| | | AD Integrated | Roles | | | Through VMware Aria Suite Lifecycle | | |
| [IPAM](#ipam) | ❌ | ✔ | ✔ | ✔ | ❌ | ❌ | | |
| | | AD Integrated | | | | | | |
| VMware Aria Automation | ✔ | ✔ | ✔ | ✔ | ❌ | ✔ | | |
| | 3 Nodes | Through Workspace ONE Access | Roles | | | Through VMware Aria Suite Lifecycle | | |
| VMware NSX-T (NSX-Transformers) | ✔ | ✔ | ✔ | ✔ | ❌ | ✔ | | |
| | 3 Nodes | Through Workspace ONE Access | Roles | | | Through SDDC Manager | | |
| ICA Server | ❌ | ✔ | ✔ | ✔ | ✔ | ❌ | ✔ | ✔ |
| | | Domain Joined | Domain Members | | Playbook - WSUS | | VMware Aria Operations | vRLI |
| Customer Billing Server | ❌ | ✔ | ✔ | ✔ | ✔ | ❌ | ✔ | |
| | | Domain Joined | SSSD Policies | | Playbook - DEB | | VMware Aria Operations | |
| Nessus (NES001) | ❌ | ✔ | ✔ | ✔ | ❌ | ❌ | ✔ | |
| | | AD Integrated | Roles | | | | VMware Aria Operations | |

**Table 1.1:**

| Tools | Vulnerability Scanning | Security Hardening | Certificate | Backup | Billing | Reporting | Firewall | Password Rotation |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| AD | ✔ | ✔ | ✔ | ❌ | ✔ | ✔ | ❌ | |
| | Nessus, ORADAD | Playbook | Playbook Rotated | | vCenter | Patch,Scan,VMware Aria Operations | | |
| Windows | ✔ | | | ❌ | ✔ | ✔ | ❌ | |
| | Nessus | | | | vCenter | Patch,Scan,VMware Aria Operations | | |
| Linux | ✔ | | | ❌ | ✔ | ✔ | ❌ | |
| | Nessus | | | | vCenter | Patch,Scan,VMware Aria Operations | | |
| ESX | | ✔ | ✔ | | ✔ | ✔ | ❌ | ✔ |
| | | Playbook | SDDC Manager | | vCenter | VMware Aria Operations | | |
| vCenter Server | | ✔ | ✔ | | ✔ | ✔ | ❌ | ✔ |
| | | Playbook | SDDC Manager | | vCenter | VMware Aria Operations | | |
| VMware Aria Operations for Logs | ❌ | ❌ | ✔ | ❌ | | ✔ | ❌ | ✔ |
| | | | VMware Aria Suite Lifecycle | | | VMware Aria Operations | | |
| SDDC Manager | ❌ | ❌ | ✔ | | | ✔ | ❌ | ✔ |
| | | | SDDC Manager | | | VMware Aria Operations | | |
| VMware Aria Operations | ❌ | ❌ | ✔ | | | ✔ | ❌ | ✔ |
| | | | VMware Aria Suite Lifecycle | | | VMware Aria Operations | | |
| VMware Aria Suite Lifecycle | ❌ | ❌ | ✔ | | | ✔ | ❌ | ✔ |
| | | | VMware Aria Suite Lifecycle | | | VMware Aria Operations | | |
| Workspace ONE Access | ❌ | ❌ | ✔ | | | ✔ | ❌ | ✔ |
| | | | VMware Aria Suite Lifecycle | | | VMware Aria Operations | | |
| [IPAM](#ipam) | ❌ | ❌ | ❌ | | | ✔ | ❌ | |
| | | | | | | VMware Aria Operations | | |
| VMware Aria Automation | ❌ | ❌ | ✔ | ❌ | ✔ | ✔ | ❌ | ✔ |
| | | | VMware Aria Suite Lifecycle | | | VMware Aria Operations | | |
| VMware NSX-T (NSX-Transformers) | ❌ | ❌ | ✔ | | ✔ | ✔ | ❌ | ✔ |
| | | | SDDC Manager | | | VMware Aria Operations | | |
| ICA Server | ✔ | | | ❌ | ✔ | ✔ | ❌ | |
| | Nessus | | | | vCenter | Patch,Scan,VMware Aria Operations | | |
| Customer Billing Server | ✔ | | | ❌ | ✔ | ✔ | ❌ | |
| | Nessus | | | | vCenter | Patch,Scan,VMware Aria Operations | | |
| Nessus (NES001) | ❌ | ❌ | ✔ | ❌ | ✔ | ✔ | ❌ | |
| | | | ICA Signed | | vCenter | VMware Aria Operations | | |

> ***NOTE:***
>
> - ✔  --> Indicates it is Enabled/Present
> - ❌ --> Indicates it is not Enabled/Present

## Reference Links

| Component                                                         | Document Link                                                                                                      |
|-------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------|
| <a id="ipam"></a>**IP Address Management**                        | [lldIPAM.md](../design/lldIPAM.md)                                                                                 |
| <a id="infrastructure"></a>**Infrastructure**                     | [lldInfrastructure.md](../design/lldInfrastructure.md)                                                             |
| <a id="nsxt"></a>**VMware NSX-T**                                 | [lldSoftwareDefinedNetworks.md](../design/lldSoftwareDefinedNetworks.md)                                           |
| <a id="production-plan"></a>**Production Plan**                   | [dhcProductionPlan.md](../workInstructions/dhcProductionPlan.md)                                                   |
| <a id="patching"></a>**Patching (Overview)**                      | [lldPatching.md](../design/lldPatching.md)                                                                         |
| <a id="windows-patching"></a>**Windows Patching**                 | [windowsPatching.md](../workInstructions/windowsPatching.md)                                                       |
| <a id="linux-patching"></a>**Linux Patching**                     | [linuxPatching.md](../workInstructions/linuxPatching.md)                                                           |
| <a id="windows-template"></a>**Template Patching – Windows**      | [wiManageGlobalImageWindowsPatching.md](../workInstructions/wiManageGlobalImageWindowsPatching.md)                 |
| <a id="linux-template"></a>**Template Patching – Linux**          | [wiManageGlobalImageLinuxPatching.md](../workInstructions/wiManageGlobalImageLinuxPatching.md)                     |
| <a id="async-patch"></a>**Async Patch Tool**                      | [dhcAsyncPatchTool.md](../workInstructions/dhcAsyncPatchTool.md)                                                   |
| <a id="hardening"></a>**Hardening – ESXi**                        | [lldHardening.md](../design/lldHardening.md)                                                                       |
| <a id="vcenter-hardening"></a>**Hardening – vCenter**             | [dhcvCenter8AtosTSSHardening.md](../workInstructions/dhcvCenter8AtosTSSHardening.md)                               |
| <a id="esxi-hardening"></a>**ESXi Hardening**                     | [dhcEsxi8AtosTSShardening.md](../workInstructions/dhcEsxi8AtosTSShardening.md)                                     |
| <a id="ad-security-enhancement"></a>**AD Security Enhancement**   | [lldADSecurityEnhancement2024.md](../design/lldADSecurityEnhancement2024.md)                                       |
| <a id="antivirus-onboarding"></a>**Anti Virus Onboarding**        | [dhcOnboardingAntivirus.md](../workInstructions/dhcOnboardingAntivirus.md)                                         |
| <a id="nessus-remediation"></a>**Nessus Remediation**             | [wiComplianceOverview.md#2-nessus-remediation](../workInstructions/wiComplianceOverview.md#2-nessus-remediation)   |
| <a id="vulnerability-management"></a>**Vulnerability Management** | [lldVulnerabilityManagement.md](../design/lldVulnerabilityManagement.md)                                           |
| <a id="rbac"></a>**RBAC**                                         | [lldDhcRoleBasedAccessControl.md](../design/lldDhcRoleBasedAccessControl.md#11-rbac-user-accounts-review-solution) |
| <a id="soxdb"></a>**SOXDB Access Review**                         | [dhcQuarterlyAccesReviewSoxDB.md](../workInstructions/dhcQuarterlyAccesReviewSoxDB.md)                             |
| <a id="password-rotation"></a>**Password Rotation**               | [wiPasswordRotation.md](../workInstructions/wiPasswordRotation.md)                                                 |
| <a id="vault"></a>**HashiCorp Vault**                             | [lldHashicorpVault.md](../design/lldHashicorpVault.md)                                                             |
| <a id="monitoring"></a>**Monitoring & Logging**                   | [lldMonitoringLogging.md](../design/lldMonitoringLogging.md)                                                       |
| <a id="reporting"></a>**Reporting**                               | [lldReporting.md](../design/lldReporting.md)                                                                       |
| <a id="certificate-renewal"></a>**Certificate Renewal**           | [wiUpdateCertificates.md](../workInstructions/wiUpdateCertificates.md)                                             |
| <a id="virtual-machine-build"></a>**Virtual Machine Build**       | [lldVmDeployment.md](../design/lldVmDeployment.md)                                                                 |

## Appendix

### Appendix A - Abbreviations

| Abbreviations | Acronyms                                  |
| --------------|-------------------------------------------|
| DHC           | Digital Hybrid Cloud                      |
| VCS           | VMware Cloud Services                     |
| vCF           | VMware Cloud Foundation                   |
| VM            | Virtual Machine                           |
| vSAN          | Virtual SAN                               |
| vRSLCM        | VMware Aria Suite Lifecycle               |
| vAA           | VMware Aria Automation                    |
| vRO           | VMware Aria Automation Orchestrator       |
| AOps          | VMware Aria Operations                    |
| AOL           | VMware Aria Operations for Logs           |
| WS1           | Workspace ONE Access                      |
| SIEM          | Security Information and Event Management |
| AD            | Active Directory                          |
| CA            | Certificate Authority                     |
| LDAP          | Lightweight Directory Access Protocol     |
| RBAC          | Role Based Access Control                 |
| DARE          | Data At Rest Encryption                   |
| KMS           | Key Management Server                     |
| KMIP          | Key Management Interoperability Protocol  |
| KEK           | Key Encryption Key                        |
| DEK           | Data Encryption Key                       |
| BDS           | Big Data and Security team                |
| ITSM          | IT Service Management                     |
| DEB Repo      | Debian Repository                         |
| SOX           | Sarbanes-Oxley Act of 2002                |
