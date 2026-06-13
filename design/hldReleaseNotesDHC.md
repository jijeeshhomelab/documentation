# Digital Hybrid Cloud: Release Notes

# Table of Contents

- [VMware Cloud Services: Release 2.1.1](#vmware-cloud-services-release-v211)
- [VMware Cloud Services: Release 2.1](#vmware-cloud-services-release-v21)
- [VMWare Cloud Services: Release 2.0](#vmware-cloud-services-release-v20)
- [VMWare Cloud Services: Release 1.9](#vmware-cloud-services-release-v19)
- [VMWare Cloud Services: Release 1.8.4](#vmware-cloud-services-release-v184)
- [VMWare Cloud Services: Release 1.8.3](#vmware-cloud-services-release-v183)
- [Digital Hybrid Cloud: Release 1.8.2](#digital-hybrid-cloud-release-v182)
- [Digital Hybrid Cloud: Release 1.8](#digital-hybrid-cloud-release-v18)
- [Digital Hybrid Cloud: Release 1.7](#digital-hybrid-cloud-release-v17)
- [Digital Hybrid Cloud: Release 1.6.1](#digital-hybrid-cloud-release-v161)
- [Digital Hybrid Cloud: Release 1.6](#digital-hybrid-cloud-release-v16)
- [Digital Hybrid Cloud: Release 1.5](#digital-hybrid-cloud-release-v15)
- [Digital Hybrid Cloud: Release 1.4](#digital-hybrid-cloud-release-v14)
- [Digital Hybrid Cloud: Release 1.3](#digital-hybrid-cloud-release-v13)
- [Digital Hybrid Cloud: Release 1.2](#digital-hybrid-cloud-release-v12)
- [Digital Hybrid Cloud: Release 1.1](#digital-hybrid-cloud-release-v11)
- [Digital Hybrid Cloud: Release 1.0](#digital-hybrid-cloud-release-v10)

# Changelog

The following changes have been made to this document for current release.

| Date       | Change Detail                                                                              | Author    |
|------------|--------------------------------------------------------------------------------------------|-----------|
| 30-10-2024 | VCS 1.8.3 Release Notes - 1st pass rollup                                                  | Alec Dunn |
| 31-10-2024 | VCS 1.8.4 Release Notes - 1st pass rollup                                                  | Alec Dunn |
| 11-02-2025 | VCS 1.9 Release Notes - 1st pass rollup                                                    | Alec Dunn |
| 22-04-2025 | VCS 2.0 Release Notes - 1st pass rollup                                                    | Alec Dunn |
| 09-12-2025 | VCS 2.1 Release Notes - 1st pass rollup                                                    | Radoslaw Dabrowski |
| 09-02-2026 | VCS 2.1 Release Notes - 2nd pass rollup                                                    | Radoslaw Dabrowski |
| 25-02-2026 | VCS 2.1.1 Release Notes - 1st pass rollup                                                  | Radoslaw Dabrowski |

# Introduction

This document contains release information for all versions of VCS/DHC in chronological order. It details its features, capabilities and components as well as any known bugs, limitations or issues with the relevant version. This document is compound and will expand with every version released for ease of use and reference.

VMware Cloud Services (VCS) is a Hybrid Cloud product developed by Atos to allow enterprise customers to deploy and utilize a private cloud solution with the ability to leverage the major public cloud endpoints as well as begin their transition of application modernization via the use of containers and other modern application architectures. A single product designed to serve capabilities as an API for modular extensibility and ease of maintenance.

VCS is designed to primarily run upon standard HyperConverged Infrastructure (HCI) on premises and currently supports **Generic vSAN ready nodes from any vendor** as long as they do NOT have any "value add, vendor specific" additional features (e.g. not compatible with Dell VxRail, PowerFlex or Nutanix). 'Usual' hardware recommended and supplied by Atos for a VCS project is **Bullion Sequana S200/SA20** or Dell **PowerEdge R760 servers**.  Dell **VxRail** Servers are **no longer** supported due to lack of value synergy with VCS. VCS also supports Fibre Channel connections to traditional SAN infrastructure for its workload domains.

VCS can integrate with any number of CMP/ITSM systems via leveraging its API interface for functionality. This is a separate integration project as VCS only comes with Service Broker as a default user accessible portal and requires integration and onboarding with ATF2.0.

## Format

This is a living document. Notes for the latest version of VCS will appear at the top of the file and older versions will sit below. This allows for an up to date and historical view from a single file. These notes are created at the point in time there is a version release cycle triggered (feature or LCM).

## Purpose

This document is here to provide a quick reference for VCS features and components for the specified release as well as see the release history of the product. It allows quick reference to headline changes without having to reference all lld or hld documentation individually.

# VMware Cloud Services Release V2.1.1

| Item                                 | Details           |
| :----------------------------------- | :---------------- |
| External Release Version             | DHC/VCS V2.1.1    |
| Release Date                         | TBD               |
| Prior Release                        | DHC/VCS V2.1      |
| Next Planned Feature Release         | DHC/VCS V2.2      |
| Support Expires on release of:       | DHC/VCS V2.3      |
| Notes Last Updated                   | 25/02/2026        |

## Software Stack Version Reference

The complete list of supported component versions for DHC/VCS environments is maintained centrally in the following matrix:

[**DHC/VCS Version Matrix – ALL Components**](https://atos365.sharepoint.com/sites/HybridCloudAtosInfrateam/Lists/DHC%20Version%20Matrix/ALL.aspx)

This matrix is the authoritative reference for all component versions, lifecycle timelines and compatibility information.

# Release Summary, New Features and Removals V2.1.1

This release focuses on lifecycle management of non-VMware/Broadcom vCF components included in DHC to ensure latest security and functionality updates are available in the product and remain within vendor support.

## Lifecycle Management Updates

![Feature](images/hldReleaseNotesDHC/feature.svg) [**DHC 2.1.1 Lifecycle Management**](https://msdevopsjira.fsc.atos-services.net/browse/VCS-12260)

Comprehensive lifecycle management update for non-VMware/Broadcom vCF components in DHC management. This release includes testing updates, automating updates, and re-running Nessus scanner to ensure all components receive latest security patches and functionality improvements while maintaining vendor support compliance.

**Key Features/Capabilities:**

- Automated upgrade process with component-specific tags for selective updates
- Email notifications for failed upgrade steps
- Enforced upgrade sequence ensuring proper dependency order (LCM → Aria Operations/Aria Operations for Logs → Aria Automation)
- Health prechecks executable outside maintenance window to minimize downtime
- Automated download and cleanup of old binaries to maintain disk space
- Enhanced SDDC Manager backup procedures
- Comprehensive security scanning performed (Nessus, Alcatraz, Aria Operations)
- End-to-end upgrade testing and validation completed
- Alternative update methods implemented for components with API limitations

**Updated Components:**

This release includes updates to VMware Cloud Foundation, SDDC Manager, Aria Automation, Aria Operations, Aria Operations for Logs, Nessus, Alcatraz (Windows & Linux), Avamar Proxy, HashiVault, Squid Proxy, Telegraf Agents, and vRO vCenter Plugin. Full version details are available in the Version Matrix.

**Links:**

- **Work Instructions**: [Automated Lifecycle Management VCS-2.1.1.0](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/master-2.1.1/workInstructions/wiAutomatedLifeCycleManagement-VCS2.1.1.0.md)
- **Version Matrix**: [DHC Version Matrix - Release 2.1.1](https://atos365.sharepoint.com/sites/HybridCloudAtosInfrateam/Lists/DHC%20Version%20Matrix/ALL.aspx?FilterField1=%5Fx0032%5F%5Fx002e%5F0%5Fx002e%5F2%5Fx003e%5F&FilterValue1=%2A&FilterType1=Calculated)
- **JIRA Epic**: [VCS-12260 DHC 2.1.1 Lifecycle Management](https://msdevopsjira.fsc.atos-services.net/browse/VCS-12260)
- **VMware Cloud Foundation Release Notes**: [VCF 5.2.1.2 Documentation](https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-5-2-and-earlier/5-2/vcf-release-notes/vmware-cloud-foundation-521-release-notes.html)
- **VMware Patch Matrix**: [VMware Product Releases](https://atos365.sharepoint.com/sites/HybridCloudAtosInfrateam/Lists/VMware%20Product%20Releases/AllItems.aspx)

# Known Limitations V2.1.1

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Aria Operations Vulnerabilities:** Some vulnerabilities detected during security scanning of Aria Operations will be addressed in the next LCM release (VCS-2.2.1). These are non-critical and do not impact current functionality.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **CPX Upgrade Monitoring:** Cloud Proxy (CPX) upgrade monitoring during Aria Operations upgrades is limited. CPX upgrades are not visible in Aria Lifecycle Manager UI and require CLI-based monitoring. Additionally, traffic between management servers and CPX nodes is blocked by DFW by default, which may require network adjustments for monitoring.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **VUM to vLCM Transition:** Transition from vSphere Update Manager (VUM/vLCM baseline) to vLCM image-based management requires VMware Cloud Foundation 5.2.2, which is not included in this release. This transition capability will be available in future DHC 2.0.x releases when upgrading to VCF 5.2.2.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Telegraf Agents Update:** Telegraf agent updates may fail when target VMs are powered off. An alternative two-method approach has been implemented to handle this scenario automatically. Upgrade to Aria Operations 8.18.5 (which resolves this issue) is planned for the next LCM release.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Squid Proxy Version:** Squid Proxy is maintained at the latest version available from Canonical for Ubuntu 22 LTS. Higher versions require Ubuntu 24 or 25. Critical security fixes are being backported by Canonical to the current version.

# VMware Cloud Services Release V2.1

| Item                                 | Details           |
| :----------------------------------- | :---------------- |
| External Release Version             | DHC/VCS V2.1      |
| Release Date                         | TBD               |
| Prior Release                        | DHC/VCS V2.0      |
| Next Planned Feature Release         | DHC/VCS V2.2      |
| Support Expires on release of:       | DHC/VCS V2.3      |
| Notes Last Updated                   | 09/02/2026        |

## Software Stack Version Reference

The complete list of supported component versions for DHC/VCS environments is maintained centrally in the following matrix:

[**DHC/VCS Version Matrix – ALL Components**](https://atos365.sharepoint.com/sites/HybridCloudAtosInfrateam/Lists/DHC%20Version%20Matrix/ALL.aspx)

This matrix is the authoritative reference for all component versions, lifecycle timelines and compatibility information.

# Release Summary, New Features and Removals V2.1

This release introduces the following new features:

## Security Enhancements

![Feature](images/hldReleaseNotesDHC/feature.svg) [**DHC AD Security Enhancement Q4/2024**](https://msdevopsjira.fsc.atos-services.net/browse/VCS-14389)

Comprehensive Active Directory security hardening for VCS environments addressing critical security gaps identified in audits. Implements automated remediation for object ownership, privileged account management, DNS permissions, and account lifecycle monitoring.

**Key Features/Capabilities:**

- Automated ownership assignment to Domain Administrator for user accounts, computer accounts, group policies, and organizational units
- High-privileged user accounts added to AuthenticationPolicy and AuthenticationPolicySilo
- Limited DNS Manager console permissions, removing dangerous DNSAdmins group access
- Automated detection of disabled or expired accounts in privileged groups with email notifications

**Links:**

- **Work Instructions**: [DHC AD Security Enhancement](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/develop/workInstructions/dhcAdSecurityEnhancement.md)
- **Design Documentation**: [LLD AD Security Enhancement 2024](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/develop/design/lldADSecurityEnhancement2024.md)
- **Code Repositories**: [DHC-Manage](https://github.com/GLB-CES-PrivateCloud/DHC-Manage/tree/master-2.1), [DHC-Deploy](https://github.com/GLB-CES-PrivateCloud/DHC-Deploy/tree/master-2.1)

![Feature](images/hldReleaseNotesDHC/feature.svg) [**DHC AD Security Enhancement Q1/2025**](https://msdevopsjira.fsc.atos-services.net/browse/VCS-14940)

Continued Active Directory security enhancements focusing on privileged account authentication controls. Extends authentication silo implementation for high-privileged accounts.

**Key Features/Capabilities:**

- Comprehensive implementation of AuthenticationPolicy and AuthenticationPolicySilo for all high-privileged user accounts
- Enhanced security boundaries for administrative access

**Links:**

- **Work Instructions**: [DHC AD Security Enhancement](https://github.com/GLB-CES-PrivateCloud/dhc-documentation/tree/master-2.1)
- **Design Documentation**: [LLD AD Security Enhancement 2024](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/develop/design/lldADSecurityEnhancement2024.md)
- **Code Repository**: [DHC-Manage](https://github.com/GLB-CES-PrivateCloud/dhc-manage/tree/master-2.1)

![Feature](images/hldReleaseNotesDHC/feature.svg) [**DHC AD Security Enhancement Q2/2025**](https://msdevopsjira.fsc.atos-services.net/browse/VCS-15437)

Active Directory cryptographic security hardening addressing SSL/TLS cipher suite vulnerabilities. Removes insecure medium-strength cipher suites susceptible to SWEET32 attacks through automated Group Policy Object deployment.

**Key Features/Capabilities:**

- Removal of medium-strength cipher suites vulnerable to SWEET32 birthday attacks
- Automated insecure cipher removal from local machine registry via Group Policy Object
- Addresses cryptographic security requirements and vulnerability assessments

**Links:**

- **Work Instructions**: [DHC AD Security Enhancement](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/master-2.1/workInstructions/dhcAdSecurityEnhancement.md)
- **Design Documentation**: [LLD AD Security Enhancement 2024](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/master-2.1/design/lldADSecurityEnhancement2024.md)
- **Code Repositories**: [DHC-Manage](https://github.com/GLB-CES-PrivateCloud/dhc-manage/tree/master-2.1/2025), [DHC-Deploy](https://github.com/GLB-CES-PrivateCloud/dhc-deploy/tree/master-2.1/2025)

![Feature](images/hldReleaseNotesDHC/feature.svg) [**Automated SOXDB Compliance Reporting**](https://msdevopsjira.fsc.atos-services.net/browse/VCS-12677)

Automated integration with SOXDB for compliance reporting. Scans DHC Active Directory and vCenter user accounts, generates CSV reports, and uploads to SOXDB endpoints.

**Key Features/Capabilities:**

- Automated scanning of DHC Active Directory and vCenter user accounts
- CSV report generation with encrypted storage
- SOXDB endpoint upload integration
- Scheduled scans via crontab
- On-demand execution support

**Links:**

- **Work Instructions**: [SOXDB Onboarding](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/VCS-12677---SOXDB-integration/workInstructions/dhcOnboardSOXDB.md), [Quarterly Access Review](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/VCS-12677---SOXDB-integration/workInstructions/dhcQuarterlyAccesReviewSoxDB.md), [Integration Guide](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/VCS-12677---SOXDB-integration/workInstructions/wiSoxDBIntegration.md)
- **Design Documentation**: [LLD SOXDB](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/VCS-12677---SOXDB-integration/design/lldSOXDB.md)
- **Code Repository**: [DHC-Manage](https://github.com/GLB-CES-PrivateCloud/DHC-Manage/blob/VCS-12677---SOXDB-integration/exportAndImportToSoxDB.yml)

## Managed OS & Compute Services

![Feature](images/hldReleaseNotesDHC/feature.svg) [**Fully Managed OS Deployment & Lifecycle Integration**](https://msdevopsjira.fsc.atos-services.net/browse/VCS-5271)

Integrated C&S OS management service providing fully Atos Managed servers within VCS. Automates VM and OS deployment with standard tooling integration, automatic monitoring and patching enrollment via Service Broker portal.

**Key Features/Capabilities:**

- Automated VM and OS deployment through Service Broker portal
- Standard tooling integration for monitoring and patching
- Automatic enrollment in monitoring and patching systems
- Single orchestration layer for complete lifecycle management
- Integration with Atos Managed Services platform

**Links:**

- **Work Instructions**: [Enable Managed OS](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/master-2.1/workInstructions/wiEnableManagedOs.md)
- **Design Documentation**: [LLD Managed OS](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/master-2.1/design/lldManagedOS.md)
- **Code Repositories**: [DHC-Manage](https://github.com/GLB-CES-PrivateCloud/DHC-Manage/blob/master-2.1/configureServiceBroker.yml), [VRO-Workflows](https://github.com/GLB-CES-PrivateCloud/VRO-Workflows/tree/master-2.1)

![Feature](images/hldReleaseNotesDHC/feature.svg) [**End User Provided Post-Deployment Script for ARIA Deploy VM Catalog**](https://msdevopsjira.fsc.atos-services.net/browse/VCS-15157)

Custom post-deployment script execution capability for VM provisioning via ARIA Deploy. Enables customers to provide custom bash (Linux) or PowerShell (Windows) scripts executed during first boot with built-in validation.

**Key Features/Capabilities:**

- Customer-defined post-deployment scripts during VM provisioning
- Platform support for Linux (bash) and Windows (PowerShell)
- First-boot script execution for initial configuration
- Command validation and safety checks to prevent system harm
- Integration with ARIA Deploy catalog items
- Support for custom application installation and configuration
- Environment-specific settings and policy enforcement

**Links:**

- **Work Instructions**: [Tenant Builder vRA On-Prem Multi-Tenancy](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/master-2.1/workInstructions/wiTenantBuilderVraOnPremMultiTenancy.md)
- **Design Documentation**: [LLD Service Catalog](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/master-2.1/design/lldServiceCatalog.md)
- **Code Repository**: [DHC-Manage](https://github.com/GLB-CES-PrivateCloud/DHC-Manage/tree/master-2.1)

## Networking & Load Balancing

![Feature](images/hldReleaseNotesDHC/feature.svg) [**Advanced Load Balancer – Web Application Firewall (WAF)**](https://msdevopsjira.fsc.atos-services.net/browse/VCS-10027)

Advanced Load Balancer successor to Native NSX Load Balancer with enhanced capabilities. Delivers L4-L7 load balancing with SSL offloading, Web Application Firewall functionality, GSLB, scripting, and detailed analytics dashboard.

**Key Features/Capabilities:**

- L4-L7 load balancing with SSL offloading options
- Web Application Firewall (WAF) functionality
- Global Server Load Balancing (GSLB) support
- Custom scripting capabilities
- Detailed analytics dashboard for troubleshooting
- Successor to deprecated Native NSX Load Balancer

**Links:**

- **Work Instructions**: [Automated Deploy Advanced Load Balancer](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/master-2.1/workInstructions/wiAutomatedDeployAdvancedLoadBalancer.md)
- **Design Documentation**: [LLD Advanced Load Balancer](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/master-2.1/design/lldAdvancedLoadBalancer.md), [LLD Microsegmentation](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/master-2.1/design/lldMicrosegmentation.md)
- **Code Repositories**: [DHC-Manage](https://github.com/GLB-CES-PrivateCloud/DHC-Manage/tree/master-2.1), [DHC-Firewall](https://github.com/GLB-CES-PrivateCloud/DHC-Firewall/tree/master-2.1), [DHC-Collections](https://github.com/GLB-CES-PrivateCloud/DHC-Collections/tree/master-2.1), [DHC-Version-Matrix](https://github.com/GLB-CES-PrivateCloud/DHC-Version-Matrix/tree/master-2.1)

![Feature](images/hldReleaseNotesDHC/feature.svg) [**NSX-T SSR: Manage Load Balancers**](https://msdevopsjira.fsc.atos-services.net/browse/VCS-9736)

Self-service AVI Load Balancer management via Aria Automation portal. Enables users to create, manage, and delete AVI Load Balancers through Service Broker for HA web servers and databases.

**Key Features/Capabilities:**

- Self-service AVI Load Balancer provisioning through Service Broker
- Full lifecycle management (create, modify, delete)
- Support for HA web servers and database load balancing
- Integration with Aria Automation portal
- Automated configuration and deployment

**Links:**

- **Work Instructions**: [Tenant Builder vRA On-Prem Multi-Tenancy](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/master-2.1/workInstructions/wiTenantBuilderVraOnPremMultiTenancy.md)
- **Design Documentation**: [LLD Service Catalog](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/master-2.1/design/lldServiceCatalog.md)
- **Code Repositories**: [DHC-Manage](https://github.com/GLB-CES-PrivateCloud/DHC-Manage/blob/master-2.1/createAviLbSsrsBroker.yml), [VRO-Workflows](https://github.com/GLB-CES-PrivateCloud/VRO-Workflows/tree/develop/Workflows/DHC/ALB)

![Feature](images/hldReleaseNotesDHC/feature.svg) [**NSX-T SSR: Manage Load Balancers Multi-Tenancy Support**](https://msdevopsjira.fsc.atos-services.net/browse/VCS-14861)

Multi-tenancy enablement for AVI Load Balancer SSRs in Aria Automation. Extends VCS-9736 with tenant isolation for secure multi-tenant load balancer management.

**Key Features/Capabilities:**

- Tenant-isolated AVI Load Balancer provisioning and management
- Self-service portal access scoped to tenant resources
- Full lifecycle management within tenant context
- Support for HA web servers and database load balancing per tenant
- Consistent SSR experience across single and multi-tenant deployments

**Links:**

- **Work Instructions**: [Enable AVI Load Balancer SSRs in vRA (Multi-Tenancy)](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/master-2.1/workInstructions/wiTenantBuilderVraOnPremMultiTenancy.md#enable-avi-load-balancer-ssrs-in-vra)
- **Design Documentation**: [LLD Service Catalog](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/master-2.1/design/lldServiceCatalog.md)
- **Code Repositories**: [DHC-Manage](https://github.com/GLB-CES-PrivateCloud/DHC-Manage/blob/master-2.1/createAviLbSsrsBroker.yml), [VRO-Workflows](https://github.com/GLB-CES-PrivateCloud/VRO-Workflows/tree/develop/Workflows/DHC/ALB)

![Feature](images/hldReleaseNotesDHC/feature.svg) [**SDN NSX-T SSR: Manage NSX-T Tags for VM**](https://msdevopsjira.fsc.atos-services.net/browse/VCS-14368)

Self-service NSX-T VM tag management via Service Broker portal. Enables dynamic VM security zone reassignment during operational lifetime without reprovisioning.

**Key Features/Capabilities:**

- Dynamic VM tag modification through Service Broker SSR interface
- Security zone reassignment during VM operational lifetime
- Post-provisioning flexibility for security policy changes
- Integration with NSX-T firewall policies and microsegmentation
- No VM reprovisioning required for tag changes

**Links:**

- **Work Instructions**: [Tenant Builder vRA On-Prem Multi-Tenancy](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/master-2.1/workInstructions/wiTenantBuilderVraOnPremMultiTenancy.md)
- **Design Documentation**: [LLD Service Catalog](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/master-2.1/design/lldServiceCatalog.md)
- **Code Repositories**: [DHC-Manage](https://github.com/GLB-CES-PrivateCloud/DHC-Manage/blob/master-2.1/configureServiceBroker.yml), [VRO-Workflows](https://github.com/GLB-CES-PrivateCloud/VRO-Workflows/tree/develop/Workflows/DHC/ManageFirewalls)

![Feature](images/hldReleaseNotesDHC/feature.svg) [**VCS Chargeback for NSX-T Objects**](https://msdevopsjira.fsc.atos-services.net/browse/VCS-73)

Automated metering and chargeback solution for SDN/NSX objects in DHC multi-tenant environments. Supports NSX 3.2.x and NSX 4.x with intelligent tenant assignment and scheduled data exports.

**Key Features/Capabilities:**

- NSX 3.2.x and NSX 4.x Manager API metering data retrieval
- Scheduled daily data exports
- Tenant tagging system for single-tenant (full charge) and shared objects (split pricing)
- Hierarchical object relationship mapping for inherited tenant membership
- Secure authentication with service account and automated password rotation
- CSI billing system integration
- Supported objects: Networks, Segments, Firewall Policies, Load Balancers

**Benefits:**

- Accurate multi-tenant cost allocation
- Automated billing data collection
- Reduced manual tagging through parent-child relationships

<!-- Note: Documentation pending - VCS-17383, Network bandwidth metering out of scope -->

## Container Platform & Kubernetes

![Feature](images/hldReleaseNotesDHC/feature.svg) [**vCF 5/vSphere 8 Adjustments for vSphere with Tanzu/TKG (Workload)**](https://msdevopsjira.fsc.atos-services.net/browse/VCS-12168)

Automation for deploying Tanzu Kubernetes functionality in VCS platform workload domain. Adjusted for VCF 5.2 (vSphere 8) compatibility with complete lifecycle management integration.

**Key Features/Capabilities:**

- Automated Tanzu deployment for VCS workload domain
- VCF 5.2 (vSphere 8) compatibility
- Complete lifecycle management integration
- Ansible-based deployment automation
- NSX-T network integration

**Links:**

- **Work Instructions**: [vSphere Tanzu CMP Build Guide](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/master-2.1/workInstructions/wiVsphereTanzuCmpBuildGuide.md)
- **Design Documentation**: [LLD vSphere Tanzu Management](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/master-2.1/design/lldVsphereTanzuMgmt.md)
- **Code Repositories**: [DHC-Manage](https://github.com/GLB-CES-PrivateCloud/DHC-Manage), [DHC-Collections](https://github.com/GLB-CES-PrivateCloud/DHC-Collections), [DHC-Firewall](https://github.com/GLB-CES-PrivateCloud/DHC-Firewall)

![Feature](images/hldReleaseNotesDHC/feature.svg) [**Deliver DHC Management K8s Platform**](https://msdevopsjira.fsc.atos-services.net/browse/VCS-12866)

Standardized Kubernetes/Docker container platform within DHC management cluster using vSphere with Tanzu. Provides foundation for hosting management components as containers, reducing deployment time and resource consumption.

**Key Features/Capabilities:**

- Tanzu platform implementation in management cluster
- Full deployment automation with Ansible playbooks
- Comprehensive monitoring solution for K8s platform
- HA/DR support and security hardening
- NSX NAPP cluster integration
- Capacity and cost modeling in MAIA

**Benefits:**

- Reduced deployment time for management components
- Lower resource consumption vs VM-based deployments
- Standardized container orchestration platform
- Foundation for future management infrastructure modernization

<!-- Note: Platform readiness complete. Containerization of top 3 management components planned for future release. -->

**Links:**

- **Work Instructions**: [vSphere Tanzu Management Build Guide](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/master-2.1/workInstructions/wiVsphereTanzuMgmtBuildGuide.md)
- **Design Documentation**: [LLD vSphere Tanzu Management](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/master-2.1/design/lldVsphereTanzuMgmt.md)
- **Code Repositories**: [DHC-Manage](https://github.com/GLB-CES-PrivateCloud/DHC-Manage/blob/master-2.1/tanzuMgmtPrerequisites.yml), [DHC-Collections](https://github.com/GLB-CES-PrivateCloud/DHC-Collections/tree/master-2.1/ansible_collections/atos/dhc/roles/configureTanzu), [DHC-Firewall](https://github.com/GLB-CES-PrivateCloud/DHC-Firewall/blob/master-2.1/microsegmentationImports/mdTanzuNsxt.yml)

## Monitoring & Reporting

![Feature](images/hldReleaseNotesDHC/feature.svg) [**Automated Service Integration – Standard Reporting Layer**](https://msdevopsjira.fsc.atos-services.net/browse/VCS-12396)

Centralized reporting structure consolidating reports from various tools in Aria Operations (vROps). Provides automated performance and capacity insights with RBAC, SSO via vIDM, and secure external access through reverse proxy.

**Key Features/Capabilities:**

- Centralized report management with proper RBAC for dashboards
- Authentication via AD or vIDM with SSO across multiple appliances
- Reverse proxy for secure external access to vROps and NSX-T
- vIDM as central landing page for unified access
- Integration of external reports (RVTools, Nessus, patching) into unified dashboards

**Links:**

- **Work Instructions**: [Reverse Proxy](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/master-2.1/workInstructions/wiReverseproxy.md)
- **Design Documentation**: [LLD Reporting](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/master-2.1/design/lldReporting.md), [LLD Reverse Proxy](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/master-2.1/design/lldReverseProxy.md)
- **Code Repositories**: [DHC-Manage](https://github.com/GLB-CES-PrivateCloud/DHC-Manage/tree/master-2.1), [DHC-Deploy](https://github.com/GLB-CES-PrivateCloud/DHC-Deploy/tree/master-2.1), [DHC-Firewall](https://github.com/GLB-CES-PrivateCloud/DHC-Firewall/tree/master-2.1)

## Service Delivery & Portal

![Feature](images/hldReleaseNotesDHC/feature.svg) [**Enhance VCS Customer Consumption Portals**](https://msdevopsjira.fsc.atos-services.net/browse/VCS-15330)

Enhanced Service Broker customer portal with expanded self-service capabilities. Delivers improved user experience through bulk operations, real-time health monitoring, simplified remote access, automated notifications, and comprehensive documentation.

**Key Features/Capabilities:**

- Bulk VM deployment for multi-VM provisioning in single operation
- vROps health integration with real-time VM health status in deployment view
- Dedicated SSH and RDP connection tabs for simplified remote access
- Automated provisioning completion emails with VM details and access information
- Comprehensive SSR (Self-Service Request) documentation within portal

**Benefits:**

- Reduced time for large-scale VM deployments
- Proactive health monitoring visibility
- Streamlined remote connectivity workflow
- Improved user awareness through automated notifications

**Links:**

- **Work Instructions**: [Tenant Builder vRA On-Prem Multi-Tenancy](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/develop/workInstructions/wiTenantBuilderVraOnPremMultiTenancy.md)
- **Code Repositories**: [DHC-Manage](https://github.com/GLB-CES-PrivateCloud/DHC-Manage/tree/master-2.1), [VRO-Workflows](https://github.com/GLB-CES-PrivateCloud/VRO-Workflows/tree/master-2.1)

![Feature](images/hldReleaseNotesDHC/feature.svg) [**IaaS+ Virtual Web Services**](https://msdevopsjira.fsc.atos-services.net/browse/VCS-14934)

Automated deployment of Atos Managed IIS (Internet Information Services) web servers on VCS platform. Extends Managed OS capabilities with IIS-specific automation for fully operational Windows web servers.

**Key Features/Capabilities:**

- Automated IIS installation and configuration via TSSA automation
- Support for all Windows distributions available on Service Broker portal
- Fully managed lifecycle including monitoring, patching, and operations
- Pre-configured web server deployment with operational readiness
- Integration with VCS Managed OS services

**Benefits:**

- Rapid deployment of production-ready IIS web servers
- Consistent configuration and compliance through automation
- Reduced manual configuration and setup time

**Links:**

- **Work Instructions**: [Enable Managed OS](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/master-2.1/workInstructions/wiEnableManagedOs.md)
- **Design Documentation**: [LLD Managed OS](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/master-2.1/design/lldManagedOS.md)
- **Code Repositories**: [DHC-Manage](https://github.com/GLB-CES-PrivateCloud/DHC-Manage/tree/master-2.1), [VRO-Workflows](https://github.com/GLB-CES-PrivateCloud/VRO-Workflows/tree/master-2.1)

## Platform Operations

![Feature](images/hldReleaseNotesDHC/feature.svg) [**Improve ESXi / vCenter Patching Process DHC 2.0**](https://msdevopsjira.fsc.atos-services.net/browse/VCS-15010)

Automated playbook for Async Patch Tool usage in VCF environments (pre-5.2). Provides scheduled patch readiness checks, automated patch installation with validation, and comprehensive error handling.

**Key Features/Capabilities:**

- Scheduled daily prereq/dependency checks before change windows
- Patch readiness reporting to central repository
- Automated trigger and patch validation in dev environments
- Unattended remote execution with alarming chain
- Configurable cron scheduling for automatic patch installation
- Email reports for installations and error conditions

**Benefits:**

- Proactive patch readiness monitoring
- Automatic bundle availability and domain prechecks
- Process improvements for change management speedup

**Links:**

- **Work Instructions**: [DHC Async Patch Tool](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/develop/workInstructions/dhcAsyncPatchTool.md)
- **Code Repository**: [DHC-Manage](https://github.com/GLB-CES-PrivateCloud/DHC-Manage/blob/develop/updateVcfAsync.yml)

![Feature](images/hldReleaseNotesDHC/feature.svg) [**DHC Automation Telemetry Implementation**](https://msdevopsjira.fsc.atos-services.net/browse/VCS-11821)

Comprehensive telemetry and observability solution for DHC Ansible automation using OpenTelemetry, Elasticsearch, and Kibana. Provides centralized collection, analysis, and visualization of automation execution traces.

**Key Features/Capabilities:**

- OpenTelemetry collectors for distributed trace collection from Ansible playbooks
- Elasticsearch stack for centralized log storage and analysis (local CLY1 deployment)
- Kibana dashboards for visualization and trace analysis
- APM Server for Application Performance Monitoring integration
- Logstash for data processing and enrichment
- End-to-end trace collection from Ansible automation workflows
- Automated onboarding for new DHC lab environments
- Integration with remote ELK stacks (SaaSon) for shared instances

**Benefits:**

- Improved automation troubleshooting and debugging
- Performance optimization insights
- Centralized operational visibility across multiple DHC environments
- Reduced MTTR for automation issues

**Links:**

- **Documentation**: [DHC OpenTelemetry Solution](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/tree/master-2.1)

## Operational Improvements

![Feature](images/hldReleaseNotesDHC/feature.svg) [**DHC 2.1 – Documentation & Code Fix from Previous Releases**](https://msdevopsjira.fsc.atos-services.net/browse/VCS-14144)
<!-- Operational Improvement - Category: Operational Improvement, BASE, TOS SCOPE: NO -->

![Feature](images/hldReleaseNotesDHC/feature.svg) [**Test & Adjust BAU Manage Playbooks**](https://msdevopsjira.fsc.atos-services.net/browse/VCS-13084)
<!-- Operational Improvement - Category: Operational Improvement, BASE, TOS SCOPE: NO -->

![Feature](images/hldReleaseNotesDHC/feature.svg) [**Include Service Account Changes in LCM Release**](https://msdevopsjira.fsc.atos-services.net/browse/VCS-16100)
<!-- Operational Improvement - Category: Operational Improvement, BASE, TOS SCOPE: YES -->

![Feature](images/hldReleaseNotesDHC/feature.svg) [**Retest and Adjust ARIA Automation Remote Console Action**](https://msdevopsjira.fsc.atos-services.net/browse/VCS-14892)
<!-- Operational Improvement - Category: Operational Improvement, BASE, TOS SCOPE: YES -->

# Feature Limitations in V2.1

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Standard Reporting:** No extended MFA support in initial release.
![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Load Balancer SSR:** Advanced features excluded from self-service portal.
![Limitation](images/hldReleaseNotesDHC/limitation.svg) **NSX-T Tags:** System tags are immutable and cannot be modified through automation.
![Limitation](images/hldReleaseNotesDHC/limitation.svg) **WAF:** Limited ruleset tuning capabilities in initial deployment.
![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Tanzu/TKG:** Workload domain deployment only. Management cluster support requires additional configuration.

# BIOS and Firmware Versions in DHC/VCS

All BIOS and firmware must match VMware HCL versions at deployment time.
Reference links: VMware HCL I/O Matrix, Supported Drivers, Bullion Server Compatibility Lists.

# Fixed Issues in V2.1

![Fixed](images/hldReleaseNotesDHC/fixed.svg) [Jira filter showing Fixed bugs for DHC Version 2.1](
https://msdevopsjira.fsc.atos-services.net/browse/VCS-14331?jql=project%20%3D%20VCS%20AND%20issuetype%20%3D%20Bug%20AND%20status%20in%20(Cancelled%2C%20Done)%20AND%20resolution%20%3D%20Unresolved%20AND%20fixVersion%20in%20(%22DHC%202.1%22%20%2C%22DHC%202.0%22%2C%20%22DHC%201.9%22)%20ORDER%20BY%20priority%20DESC%2C%20updated%20DESC)

# Remaining Bugs in V2.1

![Issue](images/hldReleaseNotesDHC/issue.svg) [Jira filter showing Remaining bugs for DHC Version 2.1](
https://msdevopsjira.fsc.atos-services.net/browse/VCS-17686?jql=project%20%3D%20VCS%20AND%20issuetype%20%3D%20Bug%20AND%20status%20in%20(%22In%20Progress%22%2C%20%22To%20Do%22%2C%20Review%2C%20Blocked%2C%20Test%2C%20%22ANALYZE%20%26%20PREPARE%22)%20AND%20resolution%20%3D%20Unresolved%20AND%20fixVersion%20%3D%20%22DHC%202.1%22%20%20ORDER%20BY%20priority%20DESC%2C%20updated%20DESC)

# Known Limitations V2.1

Several features within this DHC release are marked as BETA release features. These functionalities do not come with any associated SLA and are intended as technical previews only.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Networking:** If Active\Passive DR is enabled then any customer specific changes to NSX-T after initial setup must be manually replicated on the DR site as there is no mechanism to sync between the management stacks in this release in Active\passive DR. NOTE: As there are no SDN SSRs available in this release the chances of encountering this limitation are low but operations should be aware of the limitation.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Stretched Cluster:** In this release the maximum vSAN Stretched cluster size is 20+20+1 (A/B/Witness). This is a product limitation.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **DR:** SRM Enterprise currently has a limit of 5000 DR protected Virtual machines per install. As it is not possible to assign a 2nd instance to a vCenter already managed by SRM. This puts the practical VCS DR limit at 5000 VMs (per VCS master DHC MGMT cluster). This is a vendor technology limitation at this time.

# VMware Cloud Services Release V2.0

| Item                                 | Details     |
| :----------------------------------- | :---------- |
| External Release Version             | VCS V2.0    |
| Release Date                         | 01/05/2025  |
| Prior Release                        | VCS V1.9    |
| Next Planned Feature Release         | VCS V2.1    |
| Support Expires on release of:       | VCS V2.2    |
| Notes Last Updated                   | 22/04/2025  |

## Release Summary, New Features and Removals V2.0

This release includes the following new features:

![Feature](images/hldReleaseNotesDHC/feature.svg) [**CUSTOMER FEATURE**: VCS 2.0 Greenfield Release Based on vSphere 8](https://msdevopsjira.fsc.atos-services.net/browse/VCS-VCS-6316)

![Feature](images/hldReleaseNotesDHC/feature.svg) [**CUSTOMER FEATURE**: Improved consumption portal exposure](https://msdevopsjira.fsc.atos-services.net/browse/VCS-12616)

![Feature](images/hldReleaseNotesDHC/feature.svg) [**OPERATIONAL FEATURE**: AD security enhancement(Q4 '24) Tenable, RBAC, ORADAD](https://msdevopsjira.fsc.atos-services.net/browse/VCS-12167)

![Feature](images/hldReleaseNotesDHC/feature.svg) [**OPERATIONAL FEATURE**: VCF5/vSphere 8 Security hardening enhancements](https://msdevopsjira.fsc.atos-services.net/browse/VCS-9845)

![Feature](images/hldReleaseNotesDHC/feature.svg) [**OPERATIONAL FEATURE**: VCF5 Adjustments for vRA on prem](https://msdevopsjira.fsc.atos-services.net/browse/VCS-9847)

![Feature](images/hldReleaseNotesDHC/feature.svg) [**OPERATIONAL FEATURE**: VCF5 Adjustments for Active Active DR](https://msdevopsjira.fsc.atos-services.net/browse/VCS-9849)

![Feature](images/hldReleaseNotesDHC/feature.svg) [**OPERATIONAL FEATURE**: NSX-T 4 Multi-tennancy adoption](https://msdevopsjira.fsc.atos-services.net/browse/VCS-12175)

![Feature](images/hldReleaseNotesDHC/feature.svg) [**OPERATIONAL FEATURE**: Automated Billing Integration](https://msdevopsjira.fsc.atos-services.net/browse/VCS-13584)

### Feature Limitations in V2.0

![Limitation](images/hldReleaseNotesDHC/limitation.svg) The current implementation of **Tanzu Kubernetes Grid** has a limitation where Tanzu vSphere Supervisor Cluster hosted vSpherePods/NativePods/PodVMs can only use internal repository for images until unless there is direct DNS/Internet connection available in Customer provided DNS. i.e. connected repositories on the internet MUST have a DNS route through to the internet.

# DHC Software Stack Versions V2.0

VCS is based on a number of software components. The current versions of major components are listed in the table below. Full software stack version list is available via the VCS Version Matrix.
[Available At This Link](https://atos365.sharepoint.com/sites/HybridCloudAtosInfrateam/Lists/DHC%20Version%20Matrix/ALL.aspx?ovuser=33440fc6%2Db7c7%2D412c%2Dbb73%2D0e70b0198d5a%2Calec%2Edunn%40atos%2Enet&OR=Teams%2DHL&CT=1730199508380&clickparams=eyJBcHBOYW1lIjoiVGVhbXMtRGVza3RvcCIsIkFwcFZlcnNpb24iOiI0OS8yNDA5MDEwMTQyMyIsIkhhc0ZlZGVyYXRlZFVzZXIiOmZhbHNlfQ%3D%3D)

| Component                                                 | Version     | End of Support | Notes                   |
| :-------------------------------------------------------- | :---------- | :------------- | :---------------------- |
| Cloud Builder VM                                          | 5.2.0       | 2027-10-11     | Short support expected  |
| SDDC Manager                                              | 5.2.0       | 2027-10-11     | Short support expected  |
| VMware vCenter Server Appliance                           | 8.0 U3a     | 2027-10-11     | N/A                     |
| VMware ESXi                                               | 8.0 u3      | 2027-10-11     | N/A                     |
| VMware vSAN                                               | 8.0 u3      | 2027-10-11     | N/A                     |
| VMware NSX-T Data Center                                  | 4.2.0       | 2027-10-11     | N/A                     |
| VMware NSX Edge                                           | 4.2.0       | 2027-10-11     |                         |
| VMware Aria Suite Lifecycle Manager                       | 8.18.0.0    | 2027-10-11     | N/A                     |
| VMware Identity Access Manager                            | 3.3.7.0     | 2027-10-11     | N/A                     |
| VMware Aria Automation                                    | 8.18.0      | 2027-10-11     | N/A                     |
| VMware Aria for Logs                                      | 8.18.0-HF1  | 2027-10-11     | N/A                     |
| Vmware Aria Automation orchestrator                       | N/A         | N/A            | N/A                     |
| Aria for Operations                                       | 8.18        | 2027-10-11     | N/A                     |
| Aria for Operations Network                               | 6.13.0      | 2027-10-11     | N/A                     |
| Windows Servers                                           | Server 2022 | 2030-01-12     | Patched by MGMT Cluster |
| Ubuntu Server                                             | 22.04.2 LTS | 2029-04-01     | Supported By Canonical  |
| Hashicorp Vault                                           | 1.17.5      | 2026           | Supported by Canonical  |
| Ansible                                                   | 10.4.0      | N/A            | N/A                     |
| VMware SRM                                                | 9.0.2       | 2027-03-19     | Optional Install        |
| vSphere Replication Server                                | 9.0.2       | 2027-03-19     | Optional Install        |
| Infoblox                                                  | 9.0.5       | 2026-01-30     | N/A                     |
| Nessus                                                    | 10.8.3      | 2026-02-28     | N/A                     |
| Python                                                    | 22.0.2      | N/A            | Application Specific    |
| Tanzu Kubernetes Grid                                     | 1.21.6      | N/A            |                         |
| CEB (AVAMAR)                                              | 19.4        | N/A            | N/A                     |
| VMware HCX                                                | N/A         | N/A            | latest at Install time  |

# BIOS and Firmware Versions in DHC

VCS requires all Firmware and BIOS versions for hosts, NICs and storage cards to be validated at build time against the VMware HCL. This is because it is not possible to test every version of HW and FW in a nested lab environment. The **requirement** of DHC is that the version installed should be the newest version of the BIOS or FW that is supported/listed in the VMware HCL (Noted Below).

[VMware HCL Matrix for I/O devices](https://compatibilityguide.broadcom.com/search?program=io&persona=live&column=brandName&order=asc)

[VMware Supported Driver Versions for I/O Devices](https://knowledge.broadcom.com/external/article?legacyId=2030818)

[For Bullion S200 Servers](https://compatibilityguide.broadcom.com/detail?program=server&productId=47740&persona=live)

[For Bullion SA10 Servers](https://compatibilityguide.broadcom.com/detail?program=server&productId=51172&persona=live)

[For Bullion SA20 Servers](https://compatibilityguide.broadcom.com/detail?program=server&productId=51159&persona=live)

# Resolved Limitations V2.0

The following items from previous releases have been fixed and no longer limit the product.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) The older implementation of  **Tanzu Kubernetes Grid** may not have beeen suitable for an Active/Active implementaiton of VCS.  Deployment on a stretched cluster was not supported by VMware. **This limitation is now resolved in VCS 2.0**

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Logging:** Aria Insight For Networks currently has a limited number of data sources. This will be enhanced in a later release as the vendor allows. **This is now better than before in Network Insight.**

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **DR:** SRM Enterprise currently has a limit of 2000 DR protected Virtual machines per install. As it is not possible to assign a 2nd instance to a vCenter already managed by SRM. This puts the practical VCS DR limit at 2000 VMs (per VCS master DHC MGMT cluster). This is a vendor technology limitation at this time. **This limit is sort of removed.  It is now 5000.**

# Known Limitations V2.0

Several features within this DHC release are marked as BETA release features. These functionalities do not come with any associated SLA and are intended as technical previews only.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Networking:** If Active\Passive DR is enabled then any customer specific changes to NSX-T after initial setup must be manually replicated on the DR site as there is no mechanism to sync between the management stacks in this release in Active\passive DR. NOTE: As there are no SDN SSRs available in this release the chances of encountering this limitation are low but operations should be aware of the limitation.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Stretched Cluster:** In this release the maximum vSAN Stretched cluster size is 20+20+1 (A/B/Witness). This is a product limitation.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **DR:** SRM Enterprise currently has a limit of 5000 DR protected Virtual machines per install. As it is not possible to assign a 2nd instance to a vCenter already managed by SRM. This puts the practical VCS DR limit at 5000 VMs (per VCS master DHC MGMT cluster). This is a vendor technology limitation at this time.

# Resolved Bugs and Known Issues V2/0

The following significant bugs from VCS 1.9 have been remediated for this release.

This release of VCS includes and additional **X** low level bugs fixed in this release of VCS and these can be found at the following JIRA link:

![Fixed](images/hldReleaseNotesDHC/fixed.svg) [Jira filter showing Fixed bugs for DHC Version 2.0](https://msdevopsjira.fsc.atos-services.net/issues/?jql=project%20%3D%20VCS%20AND%20issuetype%20%3D%20Bug%20AND%20status%20in%20(Cancelled%2C%20Done)%20AND%20resolution%20%3D%20Unresolved%20AND%20fixVersion%20in%20(%22DHC%202.0%22%2C%20%22DHC%201.9%22)%20ORDER%20BY%20priority%20DESC%2C%20updated%20DESC)

# Bugs and Known Issues V2.0

This section details the known issues in this release and their relevant JIRA Items. It should be noted that the way Agile development is done all minor bugs and issues are tracked in Jira but may also have their "fix version" changed as development moves forward.  For this reason, as these notes get updated with newer versions the "remaining bugs" for older versions of VCS may contain ZERO entries as they are moved forward to the next release to be worked upon.

All major bugs will have their own line items so they can be seen as to when they are introduced and fixed. The "fixed issues" sections of any release will have accurate list of bugs fixed from previous version.

![Issue](images/hldReleaseNotesDHC/issue.svg) [Remaining minor bugs in DHC 2.0(https://msdevopsjira.fsc.atos-services.net/issues/?jql=project%20%3D%20VCS%20AND%20issuetype%20%3D%20Bug%20AND%20status%20in%20(%22In%20Progress%22%2C%20%22To%20Do%22%2C%20Review%2C%20Blocked%2C%20Test%2C%20%22ANALYZE%20%26%20PREPARE%22)%20AND%20resolution%20%3D%20Unresolved%20AND%20fixVersion%20%3D%20%22DHC%202.0%22%20ORDER%20BY%20priority%20DESC%2C%20updated%20DESC)

# VMware Cloud Services Release V1.9

| Item                                 | Details     |
| :----------------------------------- | :---------- |
| External Release Version             | VCS V1.9    |
| Release Date                         | 20/02/2025  |
| Prior Release                        | VCS V1.8.4  |
| Next Planned Feature Release         | VCS V2.0    |
| Support Expires on release of:       | VCS V2.1    |
| Notes Last Updated                   | 11/02/2025  |

## Release Summary, New Features and Removals V1.9

This release includes the following new features:

![Feature](images/hldReleaseNotesDHC/feature.svg) [**CUSTOMER FEATURE**: NSX-T SSR: Manage Security Groups & Manage Firewall rules](https://msdevopsjira.fsc.atos-services.net/browse/VCS-9733)

![Feature](images/hldReleaseNotesDHC/feature.svg) [**CUSTOMER FEATURE**: AD security enhancement(Q1) and RBAC split for domain admins](https://msdevopsjira.fsc.atos-services.net/browse/VCS-11769)

![Feature](images/hldReleaseNotesDHC/feature.svg) [**OPERATIONAL FEATURE**: NSX Edge Firewall TLS packet inspection](https://msdevopsjira.fsc.atos-services.net/browse/VCS-10972)

![Feature](images/hldReleaseNotesDHC/feature.svg) [**OPERATIONAL FEATURE**: User based firewalling / Identity Firewall (IDFW)](https://msdevopsjira.fsc.atos-services.net/browse/VCS-9819)

![Feature](images/hldReleaseNotesDHC/feature.svg) [**OPERATIONAL FEATURE**: Distributed Firewall Security Group reporting](https://msdevopsjira.fsc.atos-services.net/browse/VCS-13752)

### Feature Limitations in V1.9

![Limitation](images/hldReleaseNotesDHC/limitation.svg) The current implementation of  **Tanzu Kubernetes Grid** may not be suitable for an Active/Active implementaiton of VCS.  Deployment on a stretched cluster is not supported by VMware, as the vSphere with Tanzu layer does not distinguish between stretched and non-stretched vSphere clusters and provisions VMs randomly across the two sites. As a result, vSphere with Tanzu may provision VMs in a way that does not allow enough resources to your applications, resulting in downtime. There is also a known issue in upstream ETCd which VMware has found can cause corruption of one or more ETCd replica. This can result in a cluster being unable to schedule pods, requiring significant time and effort to recover.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) The current implementation of **Tanzu Kubernetes Grid** has a limitation where Tanzu vSphere Supervisor Cluster hosted vSpherePods/NativePods/PodVMs can only use internal repository for images until unless there is direct DNS/Internet connection available in Customer provided DNS. i.e. connected repositories on the internet MUST have a DNS route through to the internet.

# DHC Software Stack Versions V1.9

VCS is based on a number of software components. The current versions of major components are listed in the table below. Full software stack version list is available via the VCS Version Matrix.
[Available At This Link](https://atos365.sharepoint.com/sites/HybridCloudAtosInfrateam/Lists/DHC%20Version%20Matrix/ALL.aspx?ovuser=33440fc6%2Db7c7%2D412c%2Dbb73%2D0e70b0198d5a%2Calec%2Edunn%40atos%2Enet&OR=Teams%2DHL&CT=1730199508380&clickparams=eyJBcHBOYW1lIjoiVGVhbXMtRGVza3RvcCIsIkFwcFZlcnNpb24iOiI0OS8yNDA5MDEwMTQyMyIsIkhhc0ZlZGVyYXRlZFVzZXIiOmZhbHNlfQ%3D%3D)

| Component                                                 | Version     | End of Support | Notes                   |
| :-------------------------------------------------------- | :---------- | :------------- | :---------------------- |
| Cloud Builder VM                                          | 4.5.2       | 2025-10-02     | Short support expected  |
| SDDC Manager                                              | 4.5.2       | 2025-10-02     | Short support expected  |
| VMware vCenter Server Appliance                           | 7.0 U3m     | 2025-10-02     | N/A                     |
| VMware ESXi                                               | 7.0 U3p     | 2025-10-02     | N/A                     |
| VMware vSAN                                               | 7.0 U3p     | 2025-10-02     | N/A                     |
| VMware NSX-T Data Center                                  | 3.2.3.1     | 2025-10-02     | N/A                     |
| VMware NSX Edge                                           | 3.2.3.1     | 2025-10-02     |                         |
| VMware Aria Suite Lifecycle Manager                       | 8.18.0.0    | 2027-10-11     | N/A                     |
| VMware Identity Access Manager                            | 3.3.7.0     | 2025-12-31     | N/A                     |
| VMware Aria Automation                                    | 8.18.0      | 2027-10-11     | N/A                     |
| VMware Aria for Logs                                      | 8.18.0-HF1  | 2027-10-11     | N/A                     |
| Vmware Aria Automation orchestrator                       | N/A         | N/A            | N/A                     |
| Aria for Operations                                       | 8.18        | 2027-10-11     | N/A                     |
| Aria for Operations Network                               | 6.13.0      | 2027-10-11     | N/A                     |
| Windows Servers                                           | Server 2016 | 2027-01-12     | Patched by MGMT Cluster |
| Ubuntu Server                                             | 22.04.2 LTS | 2029-04-01     | Supported By Canonical  |
| Hashicorp Vault                                           | 1.17.5      | 2026           | Supported by Canonical  |
| Ansible                                                   | 4.2.0       | 2023-06-02     | N/A                     |
| VMware SRM                                                | 9.0.2       | 2027-03-19     | Optional Install        |
| vSphere Replication Server                                | 9.0.2       | 2027-03-19     | Optional Install        |
| Infoblox                                                  | 9.0.5       | 2026-01-30     | N/A                     |
| Nessus                                                    | 10.6.1      | 2025-02-28     | N/A                     |
| Python                                                    | 22.0.2      | N/A            | Application Specific    |
| Tanzu Kubernetes Grid                                     | 1.21.6      | N/A            |                         |
| CEB (AVAMAR)                                              | 19.4        | N/A            | N/A                     |
| VMware HCX                                                | N/A         | N/A            | latest at Install time  |

# BIOS and Firmware Versions in DHC

VCS requires all Firmware and BIOS versions for hosts, NICs and storage cards to be validated at build time against the VMware HCL. This is because it is not possible to test every version of HW and FW in a nested lab environment. The **requirement** of DHC is that the version installed should be the newest version of the BIOS or FW that is supported/listed in the VMware HCL (Noted Below).

[VMware HCL Matrix for I/O devices](https://compatibilityguide.broadcom.com/search?program=io&persona=live&column=brandName&order=asc)

[VMware Supported Driver Versions for I/O Devices](https://knowledge.broadcom.com/external/article?legacyId=2030818)

[For Bullion S200 Servers](https://compatibilityguide.broadcom.com/detail?program=server&productId=47740&persona=live)

[For Bullion SA10 Servers](https://compatibilityguide.broadcom.com/detail?program=server&productId=51172&persona=live)

[For Bullion SA20 Servers](https://compatibilityguide.broadcom.com/detail?program=server&productId=51159&persona=live)

# Resolved Limitations V1.9

The following items from previous releases have been fixed and no longer limit the product.

# Known Limitations V1.9

Several features within this DHC release are marked as BETA release features. These functionalities do not come with any associated SLA and are intended as technical previews only.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Networking:** If Active\Passive DR is enabled then any customer specific changes to NSX-T after initial setup must be manually replicated on the DR site as there is no mechanism to sync between the management stacks in this release in Active\passive DR. NOTE: As there are no SDN SSRs available in this release the chances of encountering this limitation are low but operations should be aware of the limitation.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Stretched Cluster:** In this release the maximum vSAN Stretched cluster size is 20+20+1 (A/B/Witness). This is a product limitation.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Logging:** vRNI currently has a limited number of data sources. This will be enhanced in a later release as the vendor allows.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **DR:** SRM Enterprise currently has a limit of 2000 DR protected Virtual machines per install. As it is not possible to assign a 2nd instance to a vCenter already managed by SRM. This puts the practical VCS DR limit at 2000 VMs (per VCS master DHC MGMT cluster). This is a vendor technology limitation at this time.

# Resolved Bugs and Known Issues V1.9

The following significant bugs from VCS 1.8 have been remediated for this release.

This release of VCS includes and additional **X** low level bugs fixed in this release of VCS and these can be found at the following JIRA link:

![Fixed](images/hldReleaseNotesDHC/fixed.svg) [Jira filter showing Fixed bugs for DHC Version 1.9](https://msdevopsjira.fsc.atos-services.net/issues/?jql=project%20%3D%20VCS%20AND%20issuetype%20%3D%20Bug%20AND%20status%20in%20(Cancelled%2C%20Done)%20AND%20resolution%20%3D%20Unresolved%20AND%20fixVersion%20in%20(%22DHC%201.8%22%2C%20%22DHC%201.8.4%22)%20ORDER%20BY%20priority%20DESC%2C%20updated%20DESC)

# Bugs and Known Issues V1.9

This section details the known issues in this release and their relevant JIRA Items. It should be noted that the way Agile development is done all minor bugs and issues are tracked in Jira but may also have their "fix version" changed as development moves forward.  For this reason, as these notes get updated with newer versions the "remaining bugs" for older versions of VCS may contain ZERO entries as they are moved forward to the next release to be worked upon.

All major bugs will have their own line items so they can be seen as to when they are introduced and fixed. The "fixed issues" sections of any release will have accurate list of bugs fixed from previous version.

![Issue](images/hldReleaseNotesDHC/issue.svg) [Remaining minor bugs in DHC 1.9](https://msdevopsjira.fsc.atos-services.net/issues/?jql=project%20%3D%20VCS%20AND%20issuetype%20%3D%20Bug%20AND%20status%20in%20(%22In%20Progress%22%2C%20%22To%20Do%22%2C%20Review%2C%20Blocked%2C%20Test%2C%20%22ANALYZE%20%26%20PREPARE%22)%20AND%20resolution%20%3D%20Unresolved%20AND%20fixVersion%20%3D%20%22DHC%201.9%22%20ORDER%20BY%20priority%20DESC%2C%20updated%20DESC)

# VMware Cloud Services Release V1.8.4

| Item                                 | Details     |
| :----------------------------------- | :---------- |
| External Release Version             | VCS V1.8.4  |
| Release Date                         | 27/11/2024  |
| Prior Release                        | VCS V1.8.3  |
| Next Planned Feature Release         | VCS V1.9.0  |
| Support Expires on release of:       | VCS V2.00   |
| Notes Last Updated                   | 31/10/2024  |

## Release Summary, New Features and Removals V1.8.4

VCS/DHC 1.8.4 is a patch release. As such, there are no features as part of this release. It is only intended to update the core infrastructure to the latest versions and eliminate identified bugs and security risks.

Worked on items and fixes can be found [In this JIRA area](https://msdevopsjira.fsc.atos-services.net/issues/?jql=fixVersion%20%3D%20%22DHC%201.8.4%22%20ORDER%20BY%20issuetype%20ASC)

### Summary

- Uplift of VMware VCF and other components in the MGMT stack to be at their latest versions as part of the VCF 4.x stack.  Note that VCF 5.x functionality and compatibility will come in VCS 2.0 release.

# VCS Software Stack Versions V1.8.4

VCS is based on a number of software components. The current versions are listed in the table below. Full software stack version list is available via the DHC Version Matrix.

| Component                                                 | Version        | End of Support | Notes                   |
| :-------------------------------------------------------- | :------------- | :------------- | :---------------------- |
| Cloud Builder VM                                          | 4.5.2          | 2025-05-31     | Short support expected  |
| SDDC Manager                                              | 4.5.2          | 2025-05-31     | Short support expected  |
| VMware vCenter Server Appliance                           | 7.0 u3m        | 2025-10-20     | N/A                     |
| VMware ESXi                                               | 7.0 u3p        | 2025-10-20     | N/A                     |
| VMware vSAN                                               | 7.0 u3p        | 2025-10-20     | N/A                     |
| VMware NSX-T Data Center                                  | 3.2.3.1        | 2025-04-07     | N/A                     |
| VMware NSX Edge                                           | 3.2.3.1        | 2025-04-07     |                         |
| VMware Aria Suite Lifecycle Manager                       | 8.18           | 2025-07-23     | N/A                     |
| Workspace One Access (vIDM)                               | 3.3.7.0        | 2025-07-23    | N/A                     |
| VMware Aria Automation                                    | 8.16           | 2025-10-16     | N/A                     |
| VMware Aria Automation for Logs                           | 8.18           | 2025-07-23    | N/A                     |
| Aria For Operations Manager                               | 8.18           | 2025-07-23     | N/A                     |
| Aria for Networks Intellignece (vRNI)                     | 6.13           | 2025-02-28     | N/A                     |
| Windows Servers                                           | Server 2022    | 2030-01-12     | Patched by MGMT Cluster |
| Ubuntu Server                                             | 22.04.2 LTS    | 2027-04-30     | Supported By Canonical  |
| Hashicorp Vault                                           | 1.14.1         | 2025           | Supported by Canonical  |
| Ansible                                                   | 4.2.0          | 2023-06-02     | N/A                     |
| VMware SRM                                                | 8.8.8          | 2025-10-11     | Optional Install        |
| vSphere Replication Server                                | 8.8.8          | 2025-10-11     | Optional Install        |
| CloudLink KMS (Optional)                                  | 7.1.5          | N/A            | For vSAN Encryption     |
| Infoblox                                                  | 8.6.1          | 2024-11-30     | N/A                     |
| Nessus                                                    | 10.6.1         | 2024-09-30     | N/A                     |
| Python                                                    | Various        | N/A            | Application Specific    |
| Tanzu Kubernetes Grid                                     | 1.21.6         | N/A            |                         |
| CEB (AVAMAR)                                              | 19.4.100       | N/A            | N/A                     |
| VMware HCX                                                | N/A            | N/A            | latest at Install time  |

# BIOS and Firmware Versions in DHC

DHC requires all Firmware and BIOS versions for hosts, NICs and storage cards to be validated at build time against the VMware HCL. This is because it is not possible to test every version of HW and FW in a nested lab environment. The **requirement** of DHC is that the version installed should be the newest version of the BIOS or FW that is supported/listed in the VMware HCL (Noted Below).

[VMware HCL Matrix for I/O devices](https://www.vmware.com/resources/compatibility/search.php?deviceCategory=io)

[VMware Supported Driver Versions for I/O Devices](https://kb.vmware.com/s/article/2030818)

[For Bullion S200 Servers](https://www.vmware.com/resources/compatibility/detail.php?deviceCategory=server&productid=47740)

[For Bullion SA10 Servers](https://www.vmware.com/resources/compatibility/detail.php?deviceCategory=server&productid=51172)

[For Bullion SA20 Servers](https://www.vmware.com/resources/compatibility/detail.php?deviceCategory=server&productid=51159)

# VMware Cloud Services Release V1.8.3

| Item                                 | Details     |
| :----------------------------------- | :---------- |
| External Release Version             | DHC V1.8.3  |
| Release Date                         | 27/10/2024  |
| Prior Release                        | DHC V1.8.2  |
| Next Planned Feature Release         | DHC V1.8.4  |
| Support Expires on release of:       | DHC V2.00   |
| Notes Last Updated                   | 30/10/2024  |

## Release Summary, New Features and Removals V1.8.3

TDHC 1.8.3 is a patch release. As such, there are no features as part of this release. It is only intended to update the core infrastructure to the latest versions and eliminate identified bugs and security risks.

Worked on items and fixes can be found [In this JIRA area](https://msdevopsjira.fsc.atos-services.net/issues/?jql=fixVersion%20%3D%20%22DHC%201.8.3%22%20ORDER%20BY%20issuetype%20ASC)

### Summary

- Windows servers within the management domain have been upgraded to Windows Server 2022 and all automation and other actions has been altered and tested to reflect this change.
- The name of the product is now changed from Digital Hybrid Cloud (DHC) to Vmware Cloud Services (VCS)

# DHC Software Stack Versions V1.8.3

DHC is based on a number of software components. The current versions are listed in the table below. Full software stack version list is available via the DHC Version Matrix.

| Component                                                 | Version        | End of Support | Notes                   |
| :-------------------------------------------------------- | :------------- | :------------- | :---------------------- |
| Cloud Builder VM                                          | 4.5.2          | 2025-05-31     | Short support expected  |
| SDDC Manager                                              | 4.5.2          | 2025-05-31     | Short support expected  |
| VMware vCenter Server Appliance                           | 7.0 u3m        | 2025-05-21     | N/A                     |
| VMware ESXi                                               | 7.0 u3n        | 2025-05-21     | N/A                     |
| VMware vSAN                                               | 7.0 u3n        | 2025-05-21     | N/A                     |
| VMware NSX-T Data Center                                  | 3.2.3.1        | 2025-04-07     | N/A                     |
| VMware NSX Edge                                           | 3.2.3.1        | 2025-04-07     |                         |
| VMware Aria Suite Lifecycle Manager                       | 8.14.0.4       | 2024-04-20     | N/A                     |
| Workspace One Access (vIDM)                               | 3.3.7.0        | 2024-12-31     | N/A                     |
| VMware Aria Automation                                    | 8.16           | 2025-01-16     | N/A                     |
| VMware vRealize Log Insight                               | 8.14.1         | 2024-12-31     | N/A                     |
| vRealize Operations Manager                               | 8.14.1         | 2024-12-31     | N/A                     |
| vRealize Network Insight (vRNI)                           | 6.11.0         | 2025-02-28     | N/A                     |
| Windows Servers                                           | Server 2022    | 2030-01-12     | Patched by MGMT Cluster |
| Ubuntu Server                                             | 22.04.2 LTS    | 2027-04-30     | Supported By Canonical  |
| Hashicorp Vault                                           | 1.14.1         | 2025           | Supported by Canonical  |
| Ansible                                                   | 4.2.0          | 2023-06-02     | N/A                     |
| VMware SRM                                                | 8.8.8          | 2025-10-11     | Optional Install        |
| vSphere Replication Server                                | 8.8.8          | 2025-10-11     | Optional Install        |
| CloudLink KMS (Optional)                                  | 7.1.5          | N/A            | For vSAN Encryption     |
| Infoblox                                                  | 8.6.1          | 2024-11-30     | N/A                     |
| Nessus                                                    | 10.6.1         | 2024-09-30     | N/A                     |
| Python                                                    | Various        | N/A            | Application Specific    |
| Tanzu Kubernetes Grid                                     | 1.21.6         | N/A            |                         |
| CEB (AVAMAR)                                              | 19.4.100       | N/A            | N/A                     |
| VMware HCX                                                | N/A            | N/A            | latest at Install time  |

# BIOS and Firmware Versions in DHC

DHC requires all Firmware and BIOS versions for hosts, NICs and storage cards to be validated at build time against the VMware HCL. This is because it is not possible to test every version of HW and FW in a nested lab environment. The **requirement** of DHC is that the version installed should be the newest version of the BIOS or FW that is supported/listed in the VMware HCL (Noted Below).

[VMware HCL Matrix for I/O devices](https://www.vmware.com/resources/compatibility/search.php?deviceCategory=io)

[VMware Supported Driver Versions for I/O Devices](https://kb.vmware.com/s/article/2030818)

[For Bullion S200 Servers](https://www.vmware.com/resources/compatibility/detail.php?deviceCategory=server&productid=47740)

[For Bullion SA10 Servers](https://www.vmware.com/resources/compatibility/detail.php?deviceCategory=server&productid=51172)

[For Bullion SA20 Servers](https://www.vmware.com/resources/compatibility/detail.php?deviceCategory=server&productid=51159)

# Digital Hybrid Cloud Release V1.8.2

| Item                                 | Details     |
| :----------------------------------- | :---------- |
| External Release Version             | DHC V1.8.2  |
| Release Date                         | 27/06/2024  |
| Prior Release                        | DHC V1.8    |
| Next Planned Feature Release         | DHC V1.9    |
| Support Expires on release of:       | DHC V2.00   |
| Notes Last Updated                   | 17/06/2024  |

## Release Summary, New Features and Removals V1.8.2

TDHC 1.8.2 is a patch release. As such, there are no features as part of this release. It is only intended to update the core infrastructure to the latest versions and eliminate identified bugs and security risks.

Worked on items and fixes can be found [In this JIRA area](https://msdevopsjira.fsc.atos-services.net/issues/?jql=fixVersion%20%3D%20%22DHC%201.8.2%22%20ORDER%20BY%20issuetype%20ASC)

### Summary

- All Linux VMs in MGMT updated to Ubuntu 22 LTS.
- vIDM is moved to a NON-CLUSTERED design as per VMware recomendations.
- AD security hardening changes are implemented in this release globally (from various PEN tests and such).

DHC 1.8.2 is a skip level update we need to consider all starting scenarios:

- From DHC 1.7.1 - full scope -> Action include viDM delcustering in 1.8.2 [LCM WI:](https://msdevopsjira.fsc.atos-services.net/browse/VCS-13154)
- From DHC 1.8.0 - without vIDM de-clustering, with Ubuntu
- From DHC 1.8.1 - without vIDM and Ubuntu update

# DHC Software Stack Versions V1.8.2

DHC is based on a number of software components. The current versions are listed in the table below. Full software stack version list is available via the DHC Version Matrix.

| Component                                                 | Version        | End of Support | Notes                   |
| :-------------------------------------------------------- | :------------- | :------------- | :---------------------- |
| Cloud Builder VM                                          | 4.5.2          | 2025-05-31     | Short support expected  |
| SDDC Manager                                              | 4.5.2          | 2025-05-31     | Short support expected  |
| VMware vCenter Server Appliance                           | 7.0.3.q        | 2025-05-21     | N/A                     |
| VMware ESXi                                               | 7.0.3.q        | 2025-05-21     | N/A                     |
| VMware vSAN                                               | 7.0.3.q        | 2025-05-21     | N/A                     |
| VMware NSX-T Data Center                                  | 3.2.3.1        | 2025-04-07     | N/A                     |
| VMware NSX Edge                                           | 3.2.3.1        | 2025-04-07     |                         |
| VMware Aria Suite Lifecycle Manager                       | 8.14           | 2024-04-20     | N/A                     |
| Workspace One Access (vIDM)                               | 3.3.7.0        | 2024-12-31     | N/A                     |
| VMware Aria Automation                                    | 8.16           | 2025-01-16     | N/A                     |
| VMware vRealize Log Insight                               | 8.14.1         | 2024-12-31     | N/A                     |
| vRealize Operations Manager                               | 8.14.1         | 2024-12-31     | N/A                     |
| vRealize Network Insight (vRNI)                           | 6.11.0         | 2025-02-28     | N/A                     |
| Windows Servers                                           | Server 2016    | 2027-01-12     | Patched by MGMT Cluster |
| Ubuntu Server                                             | 22.04.2 LTS    | 2027-04-30     | Supported By Canonical  |
| Hashicorp Vault                                           | 1.14.1         | 2025           | Supported by Canonical  |
| Ansible                                                   | 4.2.0          | 2023-06-02     | N/A                     |
| VMware SRM                                                | 8.8.8          | 2025-10-11     | Optional Install        |
| vSphere Replication Server                                | 8.8.8          | 2025-10-11     | Optional Install        |
| CloudLink KMS (Optional)                                  | 7.1.5          | N/A            | For vSAN Encryption     |
| Infoblox                                                  | 8.6.1          | 2024-11-30     | N/A                     |
| Nessus                                                    | 10.6.1         | 2024-09-30     | N/A                     |
| Python                                                    | Various        | N/A            | Application Specific    |
| Tanzu Kubernetes Grid                                     | 1.21.6         | N/A            |                         |
| CEB (AVAMAR)                                              | 19.4.100       | N/A            | N/A                     |
| VMware HCX                                                | N/A            | N/A            | latest at Install time  |

# BIOS and Firmware Versions in DHC

DHC requires all Firmware and BIOS versions for hosts, NICs and storage cards to be validated at build time against the VMware HCL. This is because it is not possible to test every version of HW and FW in a nested lab environment. The **requirement** of DHC is that the version installed should be the newest version of the BIOS or FW that is supported/listed in the VMware HCL (Noted Below).

[VMware HCL Matrix for I/O devices](https://www.vmware.com/resources/compatibility/search.php?deviceCategory=io)

[VMware Supported Driver Versions for I/O Devices](https://kb.vmware.com/s/article/2030818)

[For Bullion S200 Servers](https://www.vmware.com/resources/compatibility/detail.php?deviceCategory=server&productid=47740)

[For Bullion SA10 Servers](https://www.vmware.com/resources/compatibility/detail.php?deviceCategory=server&productid=51172)

[For Bullion SA20 Servers](https://www.vmware.com/resources/compatibility/detail.php?deviceCategory=server&productid=51159)

**NOTE For Dell PowerEdge R760/R76xd Servers:**  It is expected that DHC systems deployed on Dell HW from this version on will be using Dell R760/R760XD servers. DHC Engineering does not expect anything to change from the perspective of DHC build and run with these servers vs the older generation **however** Atos have not had any systems in to be able to validate or test. As such, Atos mandates any install using R750 systems as the base for DHC to have the following checks performed at the end of install PRIOR to go live:

- Manual verification of Firmware and Driver versions installed for Network cards and HBA cards against VMware HCL. Use latest validated compatible version
- BIOS of server to be updated to latest version
- Documentation on WI for above likely to be slightly different to that of R740 systems. Discretion is advised.

# Digital Hybrid Cloud Release V1.8

| Item                                 | Details     |
| :----------------------------------- | :---------- |
| External Release Version             | DHC V1.8    |
| Release Date                         | 01/10/2023  |
| Prior Release                        | DHC V1.7    |
| Next Planned Feature Release         | DHC V1.9    |
| Support Expires on release of:       | DHC V2.00   |
| Notes Last Updated                   | 08/09/2023  |

## VMware Aria / vRealize Addendum

During 2023 VMware re-branded vRealize suite as VMware Aria.  This change will not be reflected across DHC in one go, it will be a gradual shift. However, this is strictly a branding change. Nothing technical is altered.

## Release Summary, New Features and Removals V1.8

This release includes the following new features:

![Feature](images/hldReleaseNotesDHC/feature.svg) [**CUSTOMER FEATURE**: Enablement of VMC as a landing zone for VMs](https://msdevopsjira.fsc.atos-services.net/browse/VCS-6342)

![Feature](images/hldReleaseNotesDHC/feature.svg) [**CUSTOMER FEATURE**: DR policy management for Active/Passive DR](https://msdevopsjira.fsc.atos-services.net/browse/VCS-3686)

![Feature](images/hldReleaseNotesDHC/feature.svg) [**OPERATIONAL FEATURE**: vRA vIDM PKI integration enablement for single sign on](https://msdevopsjira.fsc.atos-services.net/browse/VCS-4399)

![Feature](images/hldReleaseNotesDHC/feature.svg) [**OPERATIONAL FEATURE**: Multiple documentation fixes](https://msdevopsjira.fsc.atos-services.net/browse/VCS-9490)

![Feature](images/hldReleaseNotesDHC/feature.svg) [**OPERATIONAL FEATURE**: Generic monitoring enhancements](https://msdevopsjira.fsc.atos-services.net/browse/VCS-6739)

![Feature](images/hldReleaseNotesDHC/feature.svg) [**ENGINEERING FEATURE**: Generic automation enhancements](https://msdevopsjira.fsc.atos-services.net/browse/VCS-6746)

![Feature](images/hldReleaseNotesDHC/feature.svg) [**ENGINEERING FEATURE**: Various LCM of DHC components (vROPS, vRA, SRM and other)](https://msdevopsjira.fsc.atos-services.net/browse/VCS-10420)

![Feature](images/hldReleaseNotesDHC/feature.svg) [**ENGINEERING FEATURE**: A/P DR support for FC storage and vRA on prem](https://msdevopsjira.fsc.atos-services.net/browse/VCS-33)

![Feature](images/hldReleaseNotesDHC/feature.svg) [**ENGINEERING FEATURE**: A/P DR support for vSAN storage and vRA on prem](https://msdevopsjira.fsc.atos-services.net/browse/VCS-5854)

### Feature Limitations in V1.8

![Limitation](images/hldReleaseNotesDHC/limitation.svg) The current implementation of  **Tanzu Kubernetes Grid** may not be suitable for an Active/Active implementaiton of DHC.  Deployment on a stretched cluster is not supported by VMware, as the vSphere with Tanzu layer does not distinguish between stretched and non-stretched vSphere clusters and provisions VMs randomly across the two sites. As a result, vSphere with Tanzu may provision VMs in a way that does not allow enough resources to your applications, resulting in downtime. There is also a known issue in upstream ETCd which VMware has found can cause corruption of one or more ETCd replica. This can result in a cluster being unable to schedule pods, requiring significant time and effort to recover.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) The current implementation of **Tanzu Kubernetes Grid** has a limitation where Tanzu vSphere Supervisor Cluster hosted vSpherePods/NativePods/PodVMs can only use internal repository for images until unless there is direct DNS/Internet connection available in Customer provided DNS. i.e. connected repositories on the internet MUST have a DNS route through to the internet.

# DHC Software Stack Versions V1.8

DHC is based on a number of software components. The current versions are listed in the table below. Full software stack version list is available via the DHC Version Matrix.

| Component                                                 | Version     | End of Support | Notes                   |
| :-------------------------------------------------------- | :---------- | :------------- | :---------------------- |
| Cloud Builder VM                                          | 4.5         | 2025-04-07     | Short support expected  |
| SDDC Manager                                              | 4.5.1       | 2025-05-31     | Short support expected  |
| VMware vCenter Server Appliance                           | 7.0 U3o     | 2025-04-02     | N/A                     |
| VMware ESXi                                               | 7.0 U3o     | 2025-04-02     | N/A                     |
| VMware vSAN                                               | 7.0 U3o     | 2025-04-02     | N/A                     |
| VMware NSX-T Data Center                                  | 3.2.2.1     | 2025-04-07     | N/A                     |
| VMware NSX Edge                                           | 3.2.2.1     | 2025-04-07     |                         |
| VMware Aria Suite Lifecycle Manager                       | 8.12.0.7    | 2024-04-20     | N/A                     |
| Workspace One Access (vIDM)                               | 3.3.7.0     | 2024-12-31     | N/A                     |
| VMware Aria Automation                                    | 8.12.1      | 2024-05-18     | N/A                     |
| VMware vRealize Log Insight                               | 8.10.2      | 2023-04-28     | N/A                     |
| vRealize Operations Manager                               | 8.10.2      | 2024-10-11     | N/A                     |
| vRealize Network Insight (vRNI)                           | 6.7.0       | 2024-07-12     | N/A                     |
| Windows Servers                                           | Server 2016 | 2027-01-12     | Patched by MGMT Cluster |
| Ubuntu Server                                             | 18.04.6 LTS | 2028-04-01     | Supported By Canonical  |
| Hashicorp Vault                                           | 1.12.2      | 2025           | Supported by Canonical  |
| Ansible                                                   | 4.2.0       | 2023-06-02     | N/A                     |
| VMware SRM                                                | 8.7.0       | 2025-10-11     | Optional Install        |
| vSphere Replication Server                                | 8.7.0       | 2025-10-11     | Optional Install        |
| CloudLink KMS (Optional)                                  | 7.1.5       | N/A            | For vSAN Encryption     |
| Infoblox                                                  | 8.6.1       | 2024-11-30     | N/A                     |
| Nessus                                                    | 10.1.1      | 2024-01-31     | N/A                     |
| Python                                                    | Various     | N/A            | Application Specific    |
| Tanzu Kubernetes Grid                                      | 1.21.6      | N/A            |                         |
| CEB (AVAMAR)                                              | 19.4        | N/A            | N/A                     |
| VMware HCX                                                | N/A         | N/A            | latest at Install time  |

# BIOS and Firmware Versions in DHC

DHC requires all Firmware and BIOS versions for hosts, NICs and storage cards to be validated at build time against the VMware HCL. This is because it is not possible to test every version of HW and FW in a nested lab environment. The **requirement** of DHC is that the version installed should be the newest version of the BIOS or FW that is supported/listed in the VMware HCL (Noted Below).

[VMware HCL Matrix for I/O devices](https://www.vmware.com/resources/compatibility/search.php?deviceCategory=io)

[VMware Supported Driver Versions for I/O Devices](https://kb.vmware.com/s/article/2030818)

[For Bullion S200 Servers](https://www.vmware.com/resources/compatibility/detail.php?deviceCategory=server&productid=47740)

[For Bullion SA10 Servers](https://www.vmware.com/resources/compatibility/detail.php?deviceCategory=server&productid=51172)

[For Bullion SA20 Servers](https://www.vmware.com/resources/compatibility/detail.php?deviceCategory=server&productid=51159)

**NOTE For Dell PowerEdge R750/R750xd Servers:**  It is expected that DHC systems deployed on Dell HW from this version on will be using Dell R750/R750XD servers. DHC Engineering does not expect anything to change from the perspective of DHC build and run with these servers vs the older generation **however** Atos have not had any systems in to be able to validate or test. As such, Atos mandates any install using R750 systems as the base for DHC to have the following checks performed at the end of install PRIOR to go live:

- Manual verification of Firmware and Driver versions installed for Network cards and HBA cards against VMware HCL. Use latest validated compatible version
- BIOS of server to be updated to latest version
- Documentation on WI for above likely to be slightly different to that of R740 systems. Discretion is advised.

# Resolved Limitations V1.8

The following items from previous releases have been fixed and no longer limit the product.

![Fixed](images/hldReleaseNotesDHC/fixed.svg) The current validated install of **vRealize Automation** on prem was limited to deployments of DHC using the active/active deployment configuration. it has now been validated against A/P DR on both vSAN and via Fibre Channel storage

# Known Limitations V1.8

Several features within this DHC release are marked as BETA release features. These functionalities do not come with any associated SLA and are intended as technical previews only.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **DR Invocation:** There is a known deficiency that involves a manual step after a DR event that requires VMs to be manually onboarded when running from FC connected storage.  This is planned to be addressed int he next release (CESDHC-64)

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Networking:** If Active\Passive DR is enabled then any customer specific changes to NSX-T after initial setup must be manually replicated on the DR site as there is no mechanism to sync between the management stacks in this release in Active\passive DR. NOTE: As there are no SDN SSRs available in this release the chances of encountering this limitation are low but operations should be aware of the limitation.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Stretched Cluster:** In this release the maximum vSAN Stretched cluster size is 20+20+1 (A/B/Witness). This is a product limitation.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Logging:** vRNI currently has a limited number of data sources. This will be enhanced in a later release as the vendor allows.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **DR:** SRM Enterprise currently has a limit of 2000 DR protected Virtual machines per install. As it is not possible to assign a 2nd instance to a vCenter already managed by SRM. This puts the practical DHC DR limit at 2000 VMs (per DHC master DHC MGMT cluster). THis is a vendor technology limitation at this time.

# Resolved Bugs and Known Issues V1.8

The following significant bugs from DHC 1.8 have been remediated for this release.

This release of DHC includes and additional **X** low level bugs fixed in this release of DHC and these can be found at the following JIRA link:

![Fixed](images/hldReleaseNotesDHC/fixed.svg) [Jira filter showing Fixed bugs for DHC Version 1.8](https://msdevopsjira.fsc.atos-services.net/issues/?jql=project%20%3D%20VCS%20AND%20issuetype%20%3D%20Bug%20AND%20status%20in%20(Cancelled%2C%20Done)%20AND%20resolution%20%3D%20Unresolved%20AND%20fixVersion%20%3D%20%22DHC%201.8%22%20ORDER%20BY%20priority%20DESC%2C%20updated%20DESC)

# Bugs and Known Issues V1.8

This section details the known issues in this release and their relevant JIRA Items. It should be noted that the way Agile development is done all minor bugs and issues are tracked in Jira but may also have their "fix version" changed as development moves forward.  For this reason, as these notes get updated with newer versions the "remaining bugs" for older versions of DHC may contain ZERO entries as they are moved forward to the next release to be worked upon.

All major bugs will have their own line items so they can be seen as to when they are introduced and fixed. The "fixed issues" sections of any release will have accurate list of bugs fixed from previous version.

![Issue](images/hldReleaseNotesDHC/issue.svg) [Remaining minor bugs in DHC 1.8](https://msdevopsjira.fsc.atos-services.net/issues/?jql=project%20%3D%20VCS%20AND%20issuetype%20%3D%20Bug%20AND%20status%20in%20(%22In%20Progress%22%2C%20%22To%20Do%22%2C%20Review%2C%20Blocked%2C%20Test%2C%20%22ANALYZE%20%26%20PREPARE%22)%20AND%20resolution%20%3D%20Unresolved%20AND%20fixVersion%20%3D%20%22DHC%201.8%22%20ORDER%20BY%20priority%20DESC%2C%20updated%20DESC)

# Digital Hybrid Cloud Release V1.7

| Item                                 | Details     |
| :----------------------------------- | :---------- |
| External Release Version             | DHC V1.7    |
| Release Date                         | 01/04/2023  |
| Prior Release                        | DHC V1.6    |
| Next Planned Feature Release         | DHC V1.8    |
| Support Expires on release of:       | DHC V1.10   |
| Notes Last Updated                   | 15/04/2023  |

## VMware Aria / vRealize Addendum

During this release cycle VMware re-branded vRealize suite as VMware Aria.  This change will not be reflected until the next release of DHC. However, this is strictly a branding change. Nothing technical is altered.

## Release Summary, New Features and Removals V1.7

This release includes the following new features:

![Feature](images/hldReleaseNotesDHC/feature.svg) [Bi-Directional Active/Passive DR support for DHC using FC connected storage](https://msdevopsjira.fsc.atos-services.net/browse/VCS-64)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Storage SSR Support (Modify Storage class) for DHC with FC connected storage](https://atos-global.atlassian.net/jira/software/c/projects/CESVXR/issues/CESDHC-29)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Enablement of automated testing of DHC components](https://atos-global.atlassian.net/jira/software/c/projects/CESVXR/issues/CESDHC-66) This is an feature benefiting internal process and support.

![Feature](images/hldReleaseNotesDHC/feature.svg) [Engineering Enhancement - Version Matrix simplification and refactor](https://msdevopsjira.fsc.atos-services.net/browse/VCS-5142) This is an feature benefiting internal process and support.

![Feature](images/hldReleaseNotesDHC/feature.svg) [Engineering Enhancement - Environment monitoring dashboard](https://msdevopsjira.fsc.atos-services.net/browse/VCS-3876) This is an feature benefiting internal process and support.

![Feature](images/hldReleaseNotesDHC/feature.svg) [Specific Siemens only CERT enhancements](https://atos-global.atlassian.net/jira/software/c/projects/CESVXR/issues/CESDHC-3759) For all security requests that are NOT relevant for the wider DHC customer base.

![Feature](images/hldReleaseNotesDHC/feature.svg) [Enablement of VM console access for customers towards customer appliance VMs](https://msdevopsjira.fsc.atos-services.net/browse/VCS-4385)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Customer Specific (ANS) enhancement to allow VMs to be moved to specific folders in vCenter](https://atos-global.atlassian.net/jira/software/c/projects/CESVXR/issues/CESDHC-4417)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Exposure of Service Broker to customers as a portal/front end option](https://msdevopsjira.fsc.atos-services.net/browse/VCS-3657)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Enablement of Tanzu Kubernetes Grid IaaS capability for DHC](https://msdevopsjira.fsc.atos-services.net/browse/VCS-3697) Allows customers to request and deploy a TKG environment for container use in DHC. Based, in this release, on Tanzu Basic offering.

![Feature](images/hldReleaseNotesDHC/feature.svg) [Enablement of vRealize Automation **On Prem** With Regional Deployment Model](https://msdevopsjira.fsc.atos-services.net/browse/VCS-3742) Enables massive cost savings by removing the requirement for using SaaS vRA deployment type.

![Feature](images/hldReleaseNotesDHC/feature.svg) [HashiCorp Vault LCM](https://msdevopsjira.fsc.atos-services.net/browse/VCS-5593)

![Feature](images/hldReleaseNotesDHC/feature.svg) [CSI data upload changes](https://msdevopsjira.fsc.atos-services.net/browse/VCS-5390)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Security Improvements](https://msdevopsjira.fsc.atos-services.net/browse/VCS-6120)

![Feature](images/hldReleaseNotesDHC/feature.svg) [vRA on-premises multitenancy rework](https://msdevopsjira.fsc.atos-services.net/browse/VCS-72)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Automated Workload Domain Addition](https://msdevopsjira.fsc.atos-services.net/browse/VCS-4336)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Automated OS template sync across DHC sites](https://msdevopsjira.fsc.atos-services.net/browse/VCS-4398)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Customer VM backup/restore SSRs for CEB Avamar](https://msdevopsjira.fsc.atos-services.net/browse/VCS-3685)

### Feature Limitations in V1.7

![Limitation](images/hldReleaseNotesDHC/limitation.svg) The current implementation of  **Tanzu Kubernetes Grid** may not be suitable for an Active/Active implementaiton of DHC.  Deployment on a stretched cluster is not supported by VMware, as the vSphere with Tanzu layer does not distinguish between stretched and non-stretched vSphere clusters and provisions VMs randomly across the two sites. As a result, vSphere with Tanzu may provision VMs in a way that does not allow enough resources to your applications, resulting in downtime. There is also a known issue in upstream ETCd which VMware has found can cause corruption of one or more ETCd replica. This can result in a cluster being unable to schedule pods, requiring significant time and effort to recover.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) The current implementation of **Tanzu Kubernetes Grid** has a limitation where Tanzu vSphere Supervisor Cluster hosted vSpherePods/NativePods/PodVMs can only use internal repository for images until unless there is direct DNS/Internet connection available in Customer provided DNS. i.e. connected repositories on the internet MUST have a DNS route through to the internet.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) The current validated install of **vRealize Automation** on prem is limited to deployments of DHC using the active/active deployment configuration. If it is required to use SRM with an active/passive DHC configuration vRealize Automation - Cloud must still be used.

# DHC Software Stack Versions V1.7

DHC is based on a number of software components. The current versions are listed in the table below:
NOTE: As this is a feature release versions between DHC 1.6.1 and DHC 1.7 are unchanged.  Table provided for ease of reference.

| Component                                                 | Version     | End of Support | Notes                   |
| :-------------------------------------------------------- | :---------- | :------------- | :---------------------- |
| Cloud Builder VM                                          | 4.5         | 07-04-2024     | Short support expected  |
| SDDC Manager                                              | 4.5         | 07-04-2024     | Short support expected  |
| VMware vCenter Server Appliance                           | 7.0 U3h     | 02-04-2025     | N/A                     |
| VMware ESXi                                               | 7.0 U3g     | 02-04-2025     | N/A                     |
| VMware vSAN                                               | 7.0 U3g     | 02-04-2025     | N/A                     |
| VMware NSX-T Data Center                                  | 3.2.1.2     | 07-04-2024     | N/A                     |
| VMware vRealize Suite Lifecycle Manager                   | 8.8.2       | 28-04-2023     | N/A                     |
| VMware vRealize Log Insight                               | 8.8.2       | 28-04-2023     | N/A                     |
| Workspace One Access (vIDM)                               | 3.3.6.0     | 18-07-2023     | N/A                     |
| vRealize Automation                                       | 8.6.2       | 18-01-2023     | N/A                     |
| vRealize Log insight Content Pack for NSX-T               | 4.0.8       | N/A            | N/A                     |
| vRealize Log Insight Content Pack for Linux               | 2.2         | N/A            | N/A                     |
| vRealize Log Insight Content Pack for Linux (SystemD)     | 1.0         | N/A            | N/A                     |
| vRealize Log insight Content Pack for vRSLCM              | 1.0.2       | N/A            | N/A                     |
| vRealize Log insight Content Pack for vIDM (WSO)          | 2.0         | N/A            | N/A                     |
| vRealize Log insight Content Pack for vSphere             | 8.10        | N/A            | N/A                     |
| vRealize Log insight Content Pack for vRA                 | 1.0         | N/A            | N/A                     |
| vRealize Log insight Content Pack for vROps               | 4.3         | N/A            | N/A                     |
| vRealize Log insight Content Pack for vSAN                | 2.5         | N/A            | N/A                     |
| vRealize Operations Manager                               | 8.6.3       | 18-01-2023     | N/A                     |
| vROps Content Pack for vIDM                               | 1.3         | N/A            | N/A                     |
| vROps Content Pack for vRA                                | 8.6.1       | N/A            | N/A                     |
| vROps Content Pack for vSphere Replication                | 8.4         | N/A            | N/A                     |
| vROps Content Pack for SRM                                | 8.4         | N/A            | N/A                     |
| vSAN Content Pack for vRLI                                | 8.6.1       | N/A            | N/A                     |
| vROps Content Pack for NSX-T                              | 8.6.1       | N/A            | N/A                     |
| Windows Servers                                           | Server 2016 | 12-01-2027     | Patched by MGMT Cluster |
| Ubuntu Server                                             | 18.04.6 LTS | 01-04-2028     | Supported By Canonical  |
| Hashicorp Vault                                           | 1.8.3       | 2024           | Supported by Canonical  |
| CloudLink KMS (Optional)                                  | 7.1.0       | N/A            | For vSAN Encryption     |
| CEB (AVAMAR)                                              | 19.4        | N/A            | N/A                     |
| Trend Micro Deep Security                                 | 20.0.5394   | N/A            | N/A                     |
| Ansible                                                   | 4.2.0       | 02-06-2023     | N/A                     |
| Nessus                                                    | 8.15.2      | 30-06-2023     | N/A                     |
| Infoblox                                                  | 8.6.2       | N/A            | N/A                     |
| vRealize Network Insight (vRNI)                           | 6.7.0       | 13-10-2023     | N/A                     |
| Python                                                    | 3.7         | 27-06-2023     | N/A                     |
| VMware HCX                                                | 4.4         | 14-07-2023     | Optional Install        |
| VMware SRM                                                | 8.4.0.2     | 01-04-2023     | Optional Install        |
| vSphere Replication Server                                | 8.3         | 01-04-2023     | Optional Install        |
| vLCM                                                      | 8.1 P1      | 10-02-2023     | vSAN Witness Creation   |
| Selenium                                                  | 3.8.0       | N/A            | N/A                     |

# BIOS and Firmware Versions in DHC

DHC requires all Firmware and BIOS versions for hosts, NICs and storage cards to be validated at build time against the VMware HCL. This is because it is not possible to test every version of HW and FW in a nested lab environment. The **requirement** of DHC is that the version installed should be the newest version of the BIOS or FW that is supported/listed in the VMware HCL (Noted Below).

[VMware HCL Matrix for I/O devices](https://www.vmware.com/resources/compatibility/search.php?deviceCategory=io)

[VMware Supported Driver Versions for I/O Devices](https://kb.vmware.com/s/article/2030818)

[For Bullion S200 Servers](https://www.vmware.com/resources/compatibility/detail.php?deviceCategory=server&productid=47740)

[For Bullion SA10 Servers](https://www.vmware.com/resources/compatibility/detail.php?deviceCategory=server&productid=51172)

[For Bullion SA20 Servers](https://www.vmware.com/resources/compatibility/detail.php?deviceCategory=server&productid=51159)

**NOTE For Dell PowerEdge R750/R750xd Servers:**  It is expected that DHC systems deployed on Dell HW  will be using Dell R750/R750XD server. These new generation servers are expected to replace R740 totally by the end of 2023 in the channel. CES engineering does not expect anything to change from the perspective of DHC build and run with these servers **however** Atos have not had any systems in to be able to validate or test. As such Atos mandates any install using R750 systems as the base for DHC to have the following checks performed at the end of install PRIOR to go live:

- Manual verification of Firmware and Driver versions installed for Network cards and HBA cards against VMware HCL. Use latest validated compatible version
- BIOS of server to be updated to latest version
- Documentation on WI for above likely to be slightly different to that of R740 systems. Discretion is advised.

# Resolved Limitations V1.7

The following items from previous releases have been fixed and no longer limit the product.

![Fixed](images/hldReleaseNotesDHC/fixed.svg) **Lifecycle Cadence for VCF and all VMware components:** Atos and DHC is now in step with VMware and their LCM for VCF.  We will be releasing frequent patch releases for DHC with updated VCf components from DHC 1.6 onwards. DHC 1.7 fully supports this news support structure.

![Fixed](images/hldReleaseNotesDHC/fixed.svg) **Out of Bundle VCF Patching:** This release of DHC continues support for the VMware asynchronous patch tool. This allows core elements of the VCF stack to be patched and uplifted outside of the normal LCM bundles. Allowing for faster remediation of critical security fixes.  This is a supported method of uplift within DHC and will not affect the time or complexity of DHC version uplifts.

# Known Limitations V1.7

Several features within this DHC release are marked as BETA release features. These functionalities do not come with any associated SLA and are intended as technical previews only.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **DR Invocation:** There is a known deficiency that involves a manual step after a DR event that requires VMs to be manually onboarded when running from FC connected storage.  This is planned to be addressed int he next release (CESDHC-64)

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Networking:** If Active\Passive DR is enabled then any customer specific changes to NSX-T after initial setup must be manually replicated on the DR site as there is no mechanism to sync between the management stacks in this release in Active\passive DR. NOTE: As there are no SDN SSRs available in this release the chances of encountering this limitation are low but operations should be aware of the limitation.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Stretched Cluster:** In this release the maximum vSAN Stretched cluster size is 20+20+1 (A/B/Witness). This is a product limitation.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Automation:** Due to a lack of, or buggy, API the following bring up tasks are currently manually done in DHC. These are all being rectified with help from the appropriate vendor.
    - RBAC implementation vIDM is still manual
    - Self-signed vIDM Certificate is in place at this time.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Logging:** vRNI currently has a limited number of data sources. This will be enhanced in a later release as the vendor allows.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **DR:** SRM Enterprise currently has a limit of 2000 DR protected Virtual machines per install. As it is not possible to assign a 2nd instance to a vCenter already managed by SRM. This puts the practical DHC DR limit at 2000 VMs (per DHC master DHC MGMT cluster). THis is a vendor technology limitation at this time.

# Resolved Bugs and Known Issues V1.7

The following significant bugs from DHC 1.7 have been remediated for this release.

This release of DHC includes and additional **X** low level bugs fixed in this release of DHC and these can be found at the following JIRA link:

![Fixed](images/hldReleaseNotesDHC/fixed.svg) [Jira filter showing Fixed bugs for DHC Version 1.7](https://msdevopsjira.fsc.atos-services.net/browse/VCS-9362?jql=project%20%3D%20VCS%20AND%20issuetype%20%3D%20Bug%20AND%20status%20%3D%20Done%20AND%20fixVersion%20%3D%20%22DHC%201.7%22)

# Bugs and Known Issues V1.7

This section details the known issues in this release and their relevant JIRA Items. It should be noted that the way Agile development is done all minor bugs and issues are tracked in Jira but may also have their "fix version" changed as development moves forward.  For this reason, as these notes get updated with newer versions the "remaining bugs" for older versions of DHC may contain ZERO entries as they are moved forward to the next release to be worked upon.

All major bugs will have their own line items so they can be seen as to when they are introduced and fixed. The "fixed issues" sections of any release will have accurate list of bugs fixed from previous version.

![Issue](images/hldReleaseNotesDHC/issue.svg) [Remaining minor bugs in DHC 1.7](https://msdevopsjira.fsc.atos-services.net/browse/VCS-9364?jql=project%20%3D%20VCS%20AND%20issuetype%20%3D%20Bug%20AND%20status%20%3D%20%22To%20Do%22%20AND%20fixVersion%20%3D%20%22DHC%201.7%22)

# Digital Hybrid Cloud Release V1.6.1

| Item                                 | Details     |
| :----------------------------------- | :---------- |
| External Release Version             | DHC V1.6.1  |
| Release Date                         | 07/02/2023  |
| Prior Release                        | DHC V1.6    |
| Next Planned Feature Release         | DHC V1.7    |
| Support Expires on release of:       | Release 1.9 |
| Notes Last Updated                   | 07/02/2023  |

## Release Summary, Changes V1.6.1

This patch release includes the following changes only:

![Feature](images/hldReleaseNotesDHC/feature.svg) [Enhance DHC security baseline using Siemens CERT measures](https://atos-global.atlassian.net/browse/CESDHC-3763)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Create vCF patch update 4.5.0](https://atos-global.atlassian.net/browse/CESDHC-4332)

### Software stack versions V1.6.1

DHC is based on a number of software components. The current versions are listed in the table below:

| Component                                                 | Version     | End of Support | Notes                   |
| :-------------------------------------------------------- | :---------- | :------------- | :---------------------- |
| Cloud Builder VM                                          | 4.5         | 07-04-2024     | Short support expected  |
| SDDC Manager                                              | 4.5         | 07-04-2024     | Short support expected  |
| VMware vCenter Server Appliance                           | 7.0 U3h     | 02-04-2025     | N/A                     |
| VMware ESXi                                               | 7.0 U3g     | 02-04-2025     | N/A                     |
| VMware vSAN                                               | 7.0 U3g     | 02-04-2025     | N/A                     |
| VMware NSX-T Data Center                                  | 3.2.1.2     | 07-04-2024     | N/A                     |
| VMware vRealize Suite Lifecycle Manager                   | 8.8.2       | 28-04-2023     | N/A                     |
| VMware vRealize Log Insight                               | 8.6.2       | 31-10-2023     | N/A                     |
| Workspace One Access (vIDM)                               | 3.3.6.0     | 18-07-2023     | N/A                     |
| vRealize Automation                                       | 8.6.2       | 18-01-2023     | N/A                     |
| vRealize Log insight Content Pack for NSX-T               | 4.0.4       | N/A            | N/A                     |
| vRealize Log Insight Content Pack for Linux               | 2.2         | N/A            | N/A                     |
| vRealize Log Insight Content Pack for Linux (SystemD)     | 1.0         | N/A            | N/A                     |
| vRealize Log insight Content Pack for vRSLCM              | 1.0.2       | N/A            | N/A                     |
| vRealize Log insight Content Pack for vIDM (WSO)          | 2.0         | N/A            | N/A                     |
| vRealize Operations Manager                               | 8.6.2       | 18-01-2023     | N/A                     |
| vROps Content Pack for vIDM                               | 1.3         | N/A            | N/A                     |
| vROps Content Pack for vSphere Replication                | 8.4         | N/A            | N/A                     |
| vROps Content Pack for SRM                                | 8.4         | N/A            | N/A                     |
| vSAN Content Pack for vRLI                                | 8.2         | N/A            | N/A                     |
| vROps Content Pack for NSX-T                              | 8.2         | N/A            | N/A                     |
| Windows Servers                                           | Server 2016 | 12-01-2027     | Patched by MGMT Cluster |
| Ubuntu Server                                             | 18.04.6 LTS | 01-04-2028     | Supported By Canonical  |
| Hashicorp Vault                                           | 1.7.3       | 2023           | Supported by Canonical  |
| CloudLink KMS (Optional)                                  | 7.1.0       | N/A            | For vSAN Encryption     |
| CEB (AVAMAR)                                              | 19.4        | N/A            | N/A                     |
| Trend Micro Deep Security                                 | 20.0.3445   | N/A            | N/A                     |
| Ansible                                                   | 4.2.0       | 02-06-2023     | N/A                     |
| Nessus                                                    | 8.15.2      | 30-06-2023     | N/A                     |
| Infoblox                                                  | 8.6.2       | N/A            | N/A                     |
| vRealize Network Insight (vRNI)                           | 6.5.1       | 13-10-2023     | N/A                     |
| Python                                                    | 3.7         | 27-06-2023     | N/A                     |
| VMware HCX                                                | 4.4         | 14-07-2023     | Optional Install        |
| VMware SRM                                                | 8.4.0.2     | 01-04-2023     | Optional Install        |
| vSphere Replication Server                                | 8.3         | 01-04-2023     | Optional Install        |
| vLCM                                                      | 8.1 P1      | 10-02-2023     | vSAN Witness Creation   |
| Selenium                                                  | 3.8.0       | N/A            | N/A                     |

# Digital Hybrid Cloud Release V1.6

| Item                                 | Details     |
| :----------------------------------- | :---------- |
| External Release Version             | DHC V1.6    |
| Release Date                         | 01/11/2022  |
| Prior Release                        | DHC V1.5    |
| Next Planned Feature Release         | DHC V1.7    |
| Support Expires on release of:       | Release 1.9 |
| Notes Last Updated                   | 24/10/2022  |

## Release Summary, New Features and Removals V1.6

This release includes the following new features:

![Feature](images/hldReleaseNotesDHC/feature.svg) [Enablement of FC connected SAN storage as Primary Storage in DHC](https://atos-global.atlassian.net/jira/software/c/projects/CESVXR/issues/CESDHC-57)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Lifecycle of VCF components to VCF 4.4.1.1 level](https://atos-global.atlassian.net/jira/software/c/projects/CESVXR/issues/CESDHC-3740)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Enablement of vRA on Prem option in Active-Active configuration](https://atos-global.atlassian.net/jira/software/c/projects/CESVXR/issues/CESDHC-71) This enables large cost savings for accounts not needing SaaS functionality

![Feature](images/hldReleaseNotesDHC/feature.svg) [Validation and enablement of Asynchronous patch tool for account teams](https://atos-global.atlassian.net/jira/software/c/projects/CESVXR/issues/CESDHC-3741) This allows the patching of specific VCF components outside of regular VCF LCM, but within a VMware supplied matrix.

![Feature](images/hldReleaseNotesDHC/feature.svg) [Enhancement DHC baseline security to be Siemens CERT compliant](https://atos-global.atlassian.net/jira/software/c/projects/CESVXR/issues/CESDHC-3759)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Bypass of Abstraction Layer 1.0 for Eventing](https://atos-global.atlassian.net/jira/software/c/projects/CESVXR/issues/CESDHC-3759)

### Removed Features in V1.6

![Limitation](images/hldReleaseNotesDHC/limitation.svg)Atos has withdrawn support for **CMP2.0** as a product and, as this is not deployed in any live DHC environment, DHC withdraws support for this product as a front end / ITSM effective immediately.

![Limitation](images/hldReleaseNotesDHC/limitation.svg)The current validated install of **vRealize Automation** on prem is limited to deployments of DHC using the active/active deployment configuration. If it is required to use SRM with an active/passive DHC configuration vRealise Automation - Cloud must still be used.

# DHC Software Stack Versions V1.6

DHC is based on a number of software components. The current versions are listed in the table below:

| Component                                                 | Version     | End of Support | Notes                   |
| :-------------------------------------------------------- | :---------- | :------------- | :---------------------- |
| Cloud Builder VM                                          | 4.4.1       | 10-02-2023     | Short support expected  |
| SDDC Manager                                              | 4.4.1.1     | 10-02-2023     | Short support expected  |
| VMware vCenter Server Appliance                           | 7.0 U3d     | 02-04-2025     | N/A                     |
| VMware ESXi                                               | 7.0 U3d     | 02-04-2025     | N/A                     |
| VMware vSAN                                               | 7.0 U3c     | 02-04-2025     | N/A                     |
| VMware NSX-T Data Center                                  | 3.1.3.7.4   | 07-04-2024     | N/A                     |
| VMware vRealize Suite Lifecycle Manager                   | 8.6.2       | 31-10-2023     | N/A                     |
| VMware vRealize Log Insight                               | 8.6.2       | 31-10-2023     | N/A                     |
| Workspace One Access (vIDM)                               | 3.3.6.0     | 18-07-2023     | N/A                     |
| vRealize Automation                                       | 8.6.2       | 18-01-2023     | N/A                     |
| vRealize Log insight Content Pack for NSX-T               | 4.0.4       | N/A            | N/A                     |
| vRealize Log Insight Content Pack for Linux               | 2.2         | N/A            | N/A                     |
| vRealize Log Insight Content Pack for Linux (SystemD)     | 1.0         | N/A            | N/A                     |
| vRealize Log insight Content Pack for vRSLCM              | 1.0.2       | N/A            | N/A                     |
| vRealize Log insight Content Pack for vIDM (WSO)          | 2.0         | N/A            | N/A                     |
| vRealize Operations Manager                               | 8.6.2       | 18-01-2023     | N/A                     |
| vROps Content Pack for vIDM                               | 1.3         | N/A            | N/A                     |
| vROps Content Pack for vSphere Replication                | 8.4         | N/A            | N/A                     |
| vROps Content Pack for SRM                                | 8.4         | N/A            | N/A                     |
| vSAN Content Pack for vRLI                                | 8.2         | N/A            | N/A                     |
| vROps Content Pack for NSX-T                              | 8.2         | N/A            | N/A                     |
| Windows Servers                                           | Server 2016 | 12-01-2027     | Patched by MGMT Cluster |
| Ubuntu Server                                             | 18.04.6 LTS | 01-04-2028     | Supported By Canonical  |
| Hashicorp Vault                                           | 1.7.3       | 2023           | Supported by Canonical  |
| CloudLink KMS (Optional)                                  | 7.1.0       | N/A            | For vSAN Encryption     |
| CEB (AVAMAR)                                              | 19.4        | N/A            | N/A                     |
| Trend Micro Deep Security                                 | 20.0.3445   | N/A            | N/A                     |
| Ansible                                                   | 4.2.0       | 02-06-2023     | N/A                     |
| Nessus                                                    | 8.15.2      | 30-06-2023     | N/A                     |
| Infoblox                                                  | 8.6.2       | N/A            | N/A                     |
| vRealize Network Insight (vRNI)                           | 6.5.1       | 13-10-2023     | N/A                     |
| Python                                                    | 3.7         | 27-06-2023     | N/A                     |
| VMware HCX                                                | 4.4         | 14-07-2023     | Optional Install        |
| VMware SRM                                                | 8.4.0.2     | 01-04-2023     | Optional Install        |
| vSphere Replication Server                                | 8.3         | 01-04-2023     | Optional Install        |
| vLCM                                                      | 8.1 P1      | 10-02-2023     | vSAN Witness Creation   |
| Selenium                                                  | 3.8.0       | N/A            | N/A                     |

# BIOS and Firmware Versions in DHC

DHC requires all Firmware and BIOS versions for hosts, NICs and storage cards to be validated at build time against the VMware HCL. This is because it is not possible to test every version of HW and FW in a nested lab environment. The **requirement** of DHC is that the version installed should be the newest version of the BIOS or FW that is supported/listed in the VMware HCL (Noted Below).

[VMware HCL Matrix for I/O devices](https://www.vmware.com/resources/compatibility/search.php?deviceCategory=io)

[VMware Supported Driver Versions for I/O Devices](https://kb.vmware.com/s/article/2030818)

[For Bullion S200 Servers](https://www.vmware.com/resources/compatibility/detail.php?deviceCategory=server&productid=47740)

[For Bullion SA10 Servers](https://www.vmware.com/resources/compatibility/detail.php?deviceCategory=server&productid=51172)

[For Bullion SA20 Servers](https://www.vmware.com/resources/compatibility/detail.php?deviceCategory=server&productid=51159)

**NOTE:** In the time frame of this DHC release Dell have made generally available Poweredge R750 servers. These new generation servers are expected to replace R740 totally by the end of 2023 in the channel. CES engineering does not expect anything to change from the perspective of DHC build and run with these servers **however** Atos have not had any systems in to be able to validate or test. As such Atos mandates any install using R750 systems as the base for DHC to have the following checks performed at the end of install PRIOR to go live:

- Manual verification of Firmware and Driver versions installed for Network cards and HBA cards against VMware HCL. Use latest validated compatible version
- BIOS of server to be updated to latest version
- Documentation on WI for above likely to be slightly different to that of R740 systems. Discretion is advised.

# Resolved Limitations V1.6

The following items from previous releases have been fixed and no longer limit the product.

![Fixed](images/hldReleaseNotesDHC/fixed.svg) **Billing: Internal Financial Flow:** MAIA now supports the correct financial flow between different support GBUs within Atos.

![Fixed](images/hldReleaseNotesDHC/fixed.svg) **Out of Bundle VCF Patching:** This release of DHC brings support for the VMware asynchronous patch tool. This allows core elements of the VCF stack to be patched and uplifted outside of the normal LCM bundles. Allowing for faster remediation of critical security fixes.  This is a supported method of uplift within DHC and will not affect the time or complexity of DHC version uplifts.

# Known Limitations V1.6

Several features within this DHC release are marked as BETA release features. These functionalities do not come with any associated SLA and are intended as technical previews only.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **VCF Support Cycle:** VMware release VCF versions a few months after mainline component releases and in packaged/validated form. The usual Atos requirement if 12 months clear support from a component introduction to being out of vendor support is not possible with this cycle. At most core DHC components will have 6-9 months of vendor support on the release of a new version of DHC.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Networking:** If Active\Passive DR is enabled then any customer specific changes to NSX-T after initial setup must be manually replicated on the DR site as there is no mechanism to sync between the management stacks in this release in Active\passive DR. NOTE: As there are no SDN SSRs available in this release the chances of encountering this limitation are low but operations should be aware of the limitation.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Stretched Cluster:** In this release the maximum vSAN Stretched cluster size is 20+20+1 (A/B/Witness). This is a product limitation.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **DR:** Active/Passive DR implementation currently does not support multiple VCF/vSphere clusters. So far, this setup has not been tested. At the moment there is no automation for integrating a newly built cluster with SRM and enabling a DR functionality. However, this improvement is planned for a subsequent release.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Automation:** Due to a lack of, or buggy, API the following bring up tasks are currently manually done in DHC. These are all being rectified with help from the appropriate vendor.
    - RBAC implementation vIDM is still manual
    - Self-signed vIDM Certificate is in place at this time.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Logging:** vRNI currently has a limited number of data sources. This will be enhanced in a later release as the vendor allows.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **DR:** SRM Enterprise currently has a limit of 2000 DR protected Virtual machines per install. As it is not possible to assign a 2nd instance to a vCenter already managed by SRM. This puts the practical DHC DR limit at 2000 VMs (per DHC master DHC MGMT cluster). THis is a vendor technology limitation at this time.

# Resolved Bugs and Known Issues V1.6

The following significant bugs from DHC 1.5 have been remediated for this release.

![Fixed](images/hldReleaseNotesDHC/fixed.svg) **Incorrect Customer Name in Alcatraz Reporting** - Bug in naming variable in Alcatraz scan causes reports to not be correctly processed by TOSCA. [Bug Link](https://msdevopsjira.fsc.atos-services.net/browse/DHC-3550)

![Fixed](images/hldReleaseNotesDHC/fixed.svg) **Incorrect OS Settings on Windows 2019** - When deploying from vRA there are incorrect OS settings on Windows 2019 servers. OS is marked as not compatible in vCenter. [Bug Link](https://msdevopsjira.fsc.atos-services.net/browse/DHC-3315)

![Fixed](images/hldReleaseNotesDHC/fixed.svg) **LCM upgradeBinaries.yml Fails without Proxy** - If there is direct internet access in a DHC the noted yml file fails as it is expecting a proxy [Bug Link](https://msdevopsjira.fsc.atos-services.net/browse/DHC-2314)

This release of DHC includes and additional **7** low level bugs fixed in this release of DHC and these can be found at the following JIRA link:

![Fixed](images/hldReleaseNotesDHC/fixed.svg) [Jira filter showing Fixed bugs for DHC Version 1.6](https://msdevopsjira.fsc.atos-services.net/issues/?jql=project%20%3D%20DHC%20AND%20issuetype%20%3D%20Bug%20AND%20status%20%3D%20CLOSED%20AND%20fixVersion%20%3D%20%22DHC%201.6%22%20ORDER%20BY%20created%20DESC)

# Bugs and Known Issues V1.6

This section details the known issues in this release and their relevant JIRA Items. It should be noted that the way Agile development is done all minor bugs and issues are tracked in Jira but may also have their "fix version" changed as development moves forward.  For this reason, as these notes get updated with newer versions the "remaining bugs" for older versions of DHC may contain ZERO entries as they are moved forward to the next release to be worked upon.

All major bugs will have their own line items so they can be seen as to when they are introduced and fixed. The "fixed issues" sections of any release will have accurate list of bugs fixed from previous version.

![Issue](images/hldReleaseNotesDHC/issue.svg) **Problem with create reverse DNS in Infoblox** - Issue that prefix is REQUIRED but not mandatory when creating certain network ranges. [Bug Link](https://msdevopsjira.fsc.atos-services.net/browse/DHC-3606)

![Issue](images/hldReleaseNotesDHC/issue.svg) **DHC vTEP GW is uses hardcoded value** - The vTEP is hardcoded on AD DHCP settings to be .1. This causes an issue when the GW is not x.x.x.1. [Bug Link](https://msdevopsjira.fsc.atos-services.net/browse/DHC-2556)

![Issue](images/hldReleaseNotesDHC/issue.svg) [Remaining minor bugs in DHC 1.6](https://msdevopsjira.fsc.atos-services.net/issues/?jql=project%20%3D%20DHC%20AND%20issuetype%20%3D%20Bug%20AND%20status%20%3D%20%22CREATE%22%20AND%20fixVersion%20%3D%20%22DHC%201.6%22%20ORDER%20BY%20created%20DESC)

# Digital Hybrid Cloud Release V1.5

| Item                                 | Details    |
| :----------------------------------- | :--------- |
| External Release Version             | DHC V1.5   |
| Release Date                         | 14/6/2022  |
| Prior Release                        | DHC V1.4   |
| Next Planned Feature Release         | DHC V1.6   |
| Support Expires on release of:       | Release 1.8|
| Notes Last Updated                   | 27/05/2022 |

## Release Summary, New Features and Removals V1.5

This release includes the following new feature

![Feature](images/hldReleaseNotesDHC/feature.svg) [DHC platform components upgraded to VCF 4.3.1.1](https://msdevopsjira.fsc.atos-services.net/browse/DHC-231)

![Feature](images/hldReleaseNotesDHC/feature.svg) [DHC Greenfield Deployment Enablement On VCF 4.3.1.1](https://msdevopsjira.fsc.atos-services.net/browse/DHC-2530)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Active Passive DR Bi-Directional Enhancement ABBA](https://msdevopsjira.fsc.atos-services.net/browse/DHC-2490)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Include new High IOPS storage class in default DHC Build](https://msdevopsjira.fsc.atos-services.net/browse/DHC-3425)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Enablement of VxRail as a standard, Supported HW options for DHC](https://msdevopsjira.fsc.atos-services.net/secure/Dashboard.jspa?selectPageId=23101)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Manage customer VM storage classes in DHC portal](https://msdevopsjira.fsc.atos-services.net/browse/DHC-2487)

### Removed Features in V1.5

![Limitation](images/hldReleaseNotesDHC/limitation.svg)Atos has withdrawn support for **CMP2.0** as a product and, as this is not deployed in any live DHC environment, DHC withdraws support for this product as a front end / ITSM effective immediately.

# DHC Software Stack Versions V1.5

DHC is based on a number of software components. The current versions are listed in the table below:

| Component                                                 | Version     | End of Support | Notes                   |
| :-------------------------------------------------------- | :---------- | :------------- | :---------------------- |
| Cloud Builder VM                                          | 4.3.1       | 24-08-2022     | N/A                     |
| SDDC Manager                                              | 4.3.1.1     | 24-08-2022     | Includes log4j Fixes    |
| VMware vCenter Server Appliance                           | 7.0 U2d     | 02-04-2025     | N/A                     |
| VMware ESXi                                               | 7.0 U2c     | 02-04-2025     | N/A                     |
| VMware vSAN                                               | 7.0 U2c     | 02-04-2025     | N/A                     |
| VMware NSX-T Data Center                                  | 3.1.3.1     | 07-04-2024     | N/A                     |
| VMware vRealize Suite Lifecycle Manager                   | 8.4.1 P2    | 31-10-2022     | N/A                     |
| VMware vRealize Log Insight                               | 8.4.1       | 31-10-2022     | N/A                     |
| Workspace One Access (vIDM)                               | 3.3.5       | 20-11-2022     | N/A                     |
| vRealize Automation (NOT EXPOSED IN THIS VERSION)         | 8.5         | N/A            | N/A                     |
| vRealize Log insight Content Pack for NSX-T               | 4.0.4       | N/A            | N/A                     |
| vRealize Log Insight Content Pack for Linux               | 2.2         | N/A            | N/A                     |
| vRealize Log Insight Content Pack for Linux (SystemD)     | 1.0         | N/A            | N/A                     |
| vRealize Log insight Content Pack for vRSLCM              | 1.0.2       | N/A            | N/A                     |
| vRealize Log insight Content Pack for vIDM (WSO)          | 2.0         | N/A            | N/A                     |
| vRealize Operations Manager                               | 8.5         | 31-10-2022     | N/A                     |
| vROps Content Pack for vIDM                               | 1.3         | N/A            | N/A                     |
| vROps Content Pack for vSphere Replication                | 8.4         | N/A            | N/A                     |
| vROps Content Pack for SRM                                | 8.4         | N/A            | N/A                     |
| vSAN Content Pack for vRLI                                | 8.2         | N/A            | N/A                     |
| vROps Content Pack for NSX-T                              | 8.2         | N/A            | N/A                     |
| Windows Servers                                           | Server 2016 | 12-01-2027     | Patched by MGMT Cluster |
| Ubuntu Server                                             | 18.04.6 LTS | 01-04-2028     | Supported By Canonical  |
| Hashicorp Vault                                           | 1.7.3       | 2023           | Supported by Canonical  |
| CloudLink KMS (Optional)                                  | 7.1.0       | N/A            | For vSAN Encryption     |
| CEB (AVAMAR)                                              | 19.4        | N/A            | N/A                     |
| Trend Micro Deep Security                                 | 20.0.3445   | N/A            | N/A                     |
| Ansible                                                   | 4.2.0       | 02-06-2023     | N/A                     |
| Nessus                                                    | 8.15.2      | 30-06-2023     | N/A                     |
| Infoblox                                                  | 8.6.2       | N/A            | N/A                     |
| vRealize Network Insight                                  | 6.1.0       | 30-10-2022     | N/A                     |
| Python                                                    | 3.7         | 27-06-2023     | N/A                     |
| VMware HCX                                                | R145        | 05-08-2022     | Optional Install        |
| VMware SRM                                                | 8.4.0.2     | 01-04-2023     | Optional Install        |
| vSphere Replication Server                                | 8.3         | 01-04-2023     | Optional Install        |
| vLCM                                                      | 8.1 P1      | 31-10-2022     | vSAN Witness Creation   |
| Selenium                                                  | 3.8.0       | N/A            | N/A                     |

# BIOS and Firmware Versions in DHC

DHC requires all Firmware and BIOS versions for hosts, NICs and storage cards to be validated at build time against the VMware HCL. This is because it is not possible to test every version of HW and FW in a nested lab environment. The **requirement** of DHC is that the version installed should be the newest version of the BIOS or FW that is supported/listed in the VMware HCL (Noted Below).

[VMware HCL Matrix for I/O devices](https://www.vmware.com/resources/compatibility/search.php?deviceCategory=io)

[VMware Supported Driver Versions for I/O Devices](https://kb.vmware.com/s/article/2030818)

[For Bullion S200 Servers](https://www.vmware.com/resources/compatibility/detail.php?deviceCategory=server&productid=47740)

[For Bullion SA10 Servers](https://www.vmware.com/resources/compatibility/detail.php?deviceCategory=server&productid=51172)

[For Bullion SA20 Servers](https://www.vmware.com/resources/compatibility/detail.php?deviceCategory=server&productid=51159)

NOTE: In the release time frame of  DHC 1.5 Dell are releasing PowerEdge R750 servers. These new generation servers are expected to replace R740 totally. CES engineering does not expect anything to change from the perspective of DHC build and run with these servers **however** we have not had any systems in to be able to validate or test. As such we mandate any install using R750 systems as the base for DHC to have  the following checks performed at the end of install PRIOR to go live:

- Manual verification of Firmware and Driver versions installed for Network cards and HBA cards against VMware HCL. Use latest validated compatible version
- BIOS of server to be updated to latest version
- Documentation on WI for above likely to be slightly different to that of R740 systems. Discretion is advised.

# Resolved Limitations V1.5

The following items from previous releases have been fixed and no longer limit the product.

![Fixed](images/hldReleaseNotesDHC/fixed.svg) **Billing: Networking/Multi Tenancy Billing:** MAIA now supports Multi Tenant billing and cost modeling.

![Fixed](images/hldReleaseNotesDHC/fixed.svg) **VxRail:** VxRail is now a supported hardware type/configuration within DHC.

![Fixed](images/hldReleaseNotesDHC/fixed.svg) **DR/Multitenancy:** Active/Passive DR implementation is now supported in a Multi-tenant DHC instance no longer limiting customers on stretched clusters to Active/Active deployments.

![Fixed](images/hldReleaseNotesDHC/fixed.svg) **Stretched Cluster Limitation:** vSAN Stretched cluster maximums has been increased from 15+15+1 nodes (31 total) to 20+20+1 notes (41 total).

# Known Limitations V1.5

Several features within this DHC release are marked as BETA release features. These functionalities do not come with any associated SLA and are intended as technical previews only.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Networking:** If Active\Passive DR is enabled then any customer specific changes to NSX-T after initial setup must be manually replicated on the DR site as there is no mechanism to sync between the management stacks in this release in Active\passive DR. NOTE: As there are no SDN SSRs available in this release the chances of encountering this limitation are low but operations should be aware of the limitation.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Stretched Cluster:** In this release the maximum vSAN Stretched cluster size is 20+20+1 (A/B/Witness). This is a product limitation.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **DR:** Active/Passive DR implementation currently does not support multiple VCF/vSphere clusters. So far, this setup has not been tested. At the moment there is no automation for integrating a newly built cluster with SRM and enabling a DR functionality. However, this improvement is planned for a subsequent release.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Automation:** Due to a lack of, or buggy, API the following bring up tasks are currently manually done in DHC.  These are all being rectified with help from the appropriate vendor.
    - RBAC implementation vIDM is still manual
    - Self-signed vIDM Certificate is in place at this time.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Logging:** vRNI currently has a limited number of data sources. This will be enhanced in a later release as the vendor allows.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **DR:** SRM Enterprise currently has a limit of 2000 DR protected Virtual machines per install. As it is not possible to assign a 2nd instance to a vCenter already managed by SRM. This puts the practical DHC DR limit at 2000 VMs (per DHC master DHC MGMT cluster). THis is a vendor technology limitation at this time.

# Resolved Bugs and Known Issues V1.5

This release of DHC includes the fixes for Apache log4j vulnerability (CVE-2021-44228 and CVE-2021-45046 as described in VMSA-2021-0028)

There are an additional **56** low level bugs fixed in this release of DHC and these can be found at the following JIRA link:

![Fixed](images/hldReleaseNotesDHC/fixed.svg) [Jira filter showing Fixed bugs for DHC Version 1.5](https://msdevopsjira.fsc.atos-services.net/issues/?jql=project%20%3D%2021807%20AND%20issuetype%20%3D%20Bug%20AND%20resolution%20in%20(Fixed%2C%20Done)%20AND%20fixVersion%20%3D%20%22DHC%201.5%22%20ORDER%20BY%20resolution%20ASC%2C%20created%20DESC%2C%20status%20DESC)

# Bugs and Known Issues V1.5

This section details the known issues in this release and their relevant JIRA Items. It should be noted that the way Agile development is done all minor bugs and issues are tracked in Jira but may also have their "fix version" changed as development moves forward. For this reason, as these notes get updated with newer versions the "remaining bugs" for older versions of DHC may contain ZERO entries as they are moved forward to the next release to be worked upon.

All major bugs will have their own line items so they can be seen as to when they are introduced and fixed. The "fixed issues" sections of any release will have accurate list of bugs fixed froim previous version.

![Issue](images/hldReleaseNotesDHC/issue.svg) **Problem with create reverse DNS in Infoblox** - Issue that prefix is REQUIRED but not mandatory when creating certain network ranges. [Bug Link](https://msdevopsjira.fsc.atos-services.net/browse/DHC-3606)

![Issue](images/hldReleaseNotesDHC/issue.svg) **Incorrect Customer Name in Alcatraz Reporting** - Bug in naming variable in Alcatraz scan causes reports to not be correctly processed by TOSCA. [Bug Link](https://msdevopsjira.fsc.atos-services.net/browse/DHC-3550)

![Issue](images/hldReleaseNotesDHC/issue.svg) **Incorrect OS Settings on Windows 2019** - When deploying from vRA there are incorrect OS settings on Windows 2019 servers. OS is marked as not compatible in vCenter. [Bug Link](https://msdevopsjira.fsc.atos-services.net/browse/DHC-3315)

![Issue](images/hldReleaseNotesDHC/issue.svg) **DHC vTEP GW is uses hardcoded value** - the vTEP is hardcoded on AD DHCP settings to be .1. This causes an issue when the GW is not x.x.x.1. [Bug Link](https://msdevopsjira.fsc.atos-services.net/browse/DHC-2556)

![Issue](images/hldReleaseNotesDHC/issue.svg) **LCM upgradeBinaries.yml Fails without Proxy** - If there is direct internet access in a DHC the noted yml file fails as it is expecting a proxy [Bug Link](https://msdevopsjira.fsc.atos-services.net/browse/DHC-2314)

![Issue](images/hldReleaseNotesDHC/issue.svg) [Remaining minor bugs in DHC 1.5](https://msdevopsjira.fsc.atos-services.net/issues/?filter=27268&jql=project%20%3D%20DHC%20AND%20issuetype%20%3D%20Bug%20AND%20resolution%20%3D%20Unresolved%20AND%20fixVersion%20%3D%20%22DHC%201.5%22%20ORDER%20BY%20resolution%20ASC%2C%20created%20DESC%2C%20status%20DESC)

# Digital Hybrid Cloud Release V1.4

This is the 5th release of DHC designed to make the platform production ready and integrated with Atos CMP2.0 offering.

| Item                                 | Details    |
| :----------------------------------- | :--------- |
| External Release Version             | DHC V1.4   |
| Release Date                         | 02/09/2021 |
| Prior Release                        | DHC V1.3   |
| Planned Upcoming Release             | DHC V1.4   |
| Support Expires on release of:       | Release 1.7|
| Notes Last Updated                   | 02/09/2021 |

## Release Summary and New Features V1.4

This release includes the following new features (Committed - alec to remove before release and check with uncommitted features):

![Feature](images/hldReleaseNotesDHC/feature.svg) [DHC platform components upgraded to VCF 4.2](https://msdevopsjira.fsc.atos-services.net/browse/DHC-68)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Automated additional stretched cluster enablement](https://msdevopsjira.fsc.atos-services.net/browse/DHC-2416)

![Feature](images/hldReleaseNotesDHC/feature.svg) [DHC Production deployment support enhancement (DevOps Bug Fixing and Support Process)](https://msdevopsjira.fsc.atos-services.net/browse/DHC-2002)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Support Windows Server Failover Clustering on Native vSAN VMDKs](https://msdevopsjira.fsc.atos-services.net/browse/DHC-2392)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Fully Support Storage classes and QoS in DHC vSAN](https://msdevopsjira.fsc.atos-services.net/browse/DHC-659)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Cluster scaling support in DHC](https://msdevopsjira.fsc.atos-services.net/browse/DHC-2419)

![Change](images/hldReleaseNotesDHC/change.svg) CSI Dev and Ops support now available via CES

# DHC Software Stack Versions V1.4

DHC is based on a number of software components. The current versions are listed in the table below:

| Component                                                 | Version     | End Of Support | Notes                   |
| :-------------------------------------------------------- | :---------- | :------------- |:---------------------- |
| Cloud Builder VM                                          | 4.2.1       | 02/10/2023     |N/A                     |
| SDDC Manager                                              | 4.2.1       | 02/10/2023     |N/A                     |
| VMware vCenter Server Appliance                           | 7.0.1.00301 | 04/07/2025     |N/A                     |
| VMware ESXi                                               | 7.0 U1d     | 04/07/2025     |N/A                     |
| VMware vSAN                                               | 7.0 U1d     | 04/07/2025     |N/A                     |
| VMware NSX-T Data Center                                  | 3.1.2       | 04/07/2024     |N/A                     |
| VMware vRealize Suite Lifecycle Manager                   | 8.2 P2      | 31/10/2022     |N/A                     |
| VMware vRealize Log Insight                               | 8.2         | 31/10/2022     |N/A                     |
| Workspace One Identity Manager                            | 3.3.4       | 04-08-2022     |N/A                     |
| vRealize Automation (NOT EXPOSED IN THIS VERSION)         | 8.2         | N/A            |N/A                     |
| vRealize Log insight Content Pack for NSX-T               | 4.0.3       | N/A            |N/A                     |
| vRealize Log Insight Content Pack for Linux               | 2.1         | N/A            |N/A                     |
| vRealize Log Insight Content Pack for Linux (SystemD)     | 1.0         | N/A            |N/A                     |
| vRealize Log insight Content Pack for vRSLCM              | 1.0.2       | N/A            |N/A                     |
| vRealize Log insight Content Pack for vIDM (WSO)          | 2.0         | N/A            |N/A                     |
| vRealize Operations Manager                               | 8.2         | N/A            |N/A                     |
| vROps Content Pack for vIDM                               | 1.1         | N/A            |N/A                     |
| vROps Content Pack for vSphere Replication                | 8.3         | N/A            |N/A                     |
| vROps Content Pack for SRM                                | 8.3         | N/A            |N/A                     |
| vSAN Content Pack for vRLI                                | 2.2         | N/A            |N/A                     |
| vROps Content Pack for NSX-T                              | 8.1         | N/A            |N/A                     |
| Windows Servers                                           | Server 2016 | 01/12/ 2027    |Patched by MGMT Cluster |
| Ubuntu Server                                             | 18.04.5 LTS | 04/01/2028     |Supported By Canonical  |
| Hashicorp Vault                                           | 1.3.2       | N/A            |Supported by Canonical  |
| CloudLink KMS (Optional)                                  | 6.9.0       | N/A            |For vSAN Encryption     |
| CEB (AVAMAR)                                              | 19.3        | N/A            |N/A                     |
| Trend Micro Deep Security                                 | 12          | N/A            |N/A                     |
| Ansible                                                   | 2.10.0      | N/A            |N/A                     |
| Nessus                                                    | 8.8.0       | N/A            |N/A                     |
| Infoblox                                                  | 8.4.4       | N/A            |N/A                     |
| vRealize Network Insight                                  | 5.1.0       | 31-10-2021     |N/A                     |
| Python                                                    | 3.6.9.1     | N/A            |Part of Selenium        |
| VMware HCX                                                | R145        | 08/05/2022     |Optional Install        |
| VMware SRM                                                | 8.4.0.2     | 04/01/2023     |Optional Install        |
| vSphere Replication Server                                | 8.3         | 04/01/2023     |Optional Install        |
| vLCM                                                      | 8.1.0.25    | 31/10/2022     |vSAN Witness Creation   |
| Selenium                                                  | 3.8.0       | N/A            |N/A                     |

# Resolved Limitations V1.4

The following items from previous releases have been fixed and no longer limit the product.

![Fixed](images/hldReleaseNotesDHC/fixed.svg) **IOPS and Storage Classes:** DHC storage classes have been enhanced and now allow for customer set IOPS values and multiple IOPS classes per VM (1 per disk).

![Fixed](images/hldReleaseNotesDHC/fixed.svg) **Stretched Clusters:** You may now have a DHC with more than 1 stretched cluster.  Additional cluster bringup are automated (DPC-590).

![Fixed](images/hldReleaseNotesDHC/fixed.svg) **Clusters:** You can now scale clusters past their initial deployment size (DPC-592)

# Known Limitations V1.4

Several features within this DHC release are marked as BETA release features. These functionalities do not come with any associated SLA and are intended as technical previews only.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Billing: Networking/Multi Tenancy Billing:** Currently, although DHC supports multiple tenants and network constructs to support these tenants (T0,T1, vRF etc).  There is no way of currently billing these consumed resources on a per tenant basis.  This will be remedied in a later release.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Networking:** If Active\Passive DR is enabled then any customer specific changes to NSX-T after initial setup must be manually replicated on the DR site as there is no mechanism to sync between the management stacks in this release in Active\passive DR. NOTE: As there are no SDN SSRs available in this release the chances of encountering this limitation are low but operations should be aware of the limitation.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Logging:** vRNI currently has a limited number of data sources. This will be enhanced in a later release as the vendor allows.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **DR:** SRM Enterprise currently has a limit of 2000 DR protected Virtual machines per install. As it is not possible to assign a 2nd instance to a vCenter already managed by SRM. This puts the practical DHC DR limit at 2000 VMs (per DHC master DHC MGMT cluster). This is a vendor technology limitation at this time.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **DR:** Active/Passive DR implementation currently does not support multiple VCF/vSphere clusters. So far, this setup has not been tested. At the moment there is no automation for integrating a newly built cluster with SRM and enabling a DR functionality. However, this improvement is planned for a subsequent release.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **DR/Multi-tenancy:** Active/Passive DR implementation is not supported in a Multi-tenant DHC instance. So far, setting up an A/P DR-enabled DHC has not been tested with the new organization structure in VRA Cloud introduced by the Multi-tenancy feature. At the moment the documented procedures and automation for setting up an Active/Passive DR environment do not include extra steps required for enabling this functionality in a shared environment. However, this improvement is planned for a subsequent release.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Stretched Cluster:** In this release the maximum vSAN Stretched cluster size is 15+15+1 (A/B/Witness). This is a product limitation.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Automation:** Due to a lack of, or buggy, API the following bring up tasks are currently manually done in DHC. These are all being rectified with help from the appropriate vendor.
    - RBAC implementation for SDDC Manager and vIDM is still manual
    - Self-signed vIDM Certificate is in place at this time.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **VxRail:** In this version, VxRail is not compatible with DHC due to support and API deficiencies. A work item is under way to remedy this issue in a subsequent release.  

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **CMP2.0:** If CMP2.0 is the ITSM of choice then Active/Passive DR is not available as CMP2.0 has no concept of the duplicate (DR) VM CIs and iss, therefore, not compatible with the feature. Note: CMP2 is now an end of life product

# Resolved Bugs and Known Issues V1.4

There are an additional **13** low level bugs fixed in this release of DHC and these can be found at the following JIRA link:

![Fixed](images/hldReleaseNotesDHC/fixed.svg) [Jira filter showing Fixed bugs for DHC Version 1.4](https://msdevopsjira.fsc.atos-services.net/issues/?filter=27268&jql=project%20%3D%20DHC%20AND%20issuetype%20%3D%20Bug%20AND%20resolution%20in%20(Fixed%2C%20Done)%20AND%20fixVersion%20%3D%20%22DHC%201.4%22%20ORDER%20BY%20resolution%20ASC%2C%20created%20DESC%2C%20status%20DESC)

# Bugs and Known Issues V1.4

This section details the known issues in this release and their relevant JIRA Items.

![Issue](images/hldReleaseNotesDHC/issue.svg) **DFW to vRLI Integration Issue:** - There is an issue where some DFW logs are not configured to be sent to vRLI. This is being tracked in the following story. [Bug Link](https://msdevopsjira.fsc.atos-services.net/browse/DHC-2325)

![Issue](images/hldReleaseNotesDHC/issue.svg) **Playbook UpgradeBinaries.yml' does nt work without proxy:** - During an LCM attempt the mentioned playbook may fail if there is not an internet proxy in place in the configured DHC (i.e. if there is direct access).  This issue is being addressed in the following story. [Bug Link](https://msdevopsjira.fsc.atos-services.net/browse/DHC-2314)

![Issue](images/hldReleaseNotesDHC/issue.svg) **vRLI Integration Issue:** There is an issue with the integration of vCenter and vRLI in certain instances. This is being tracked in the following story. [Bug Link](https://msdevopsjira.fsc.atos-services.net/browse/DHC-2304)

![Issue](images/hldReleaseNotesDHC/issue.svg) [Remaining minor bugs in DHC 1.4](https://msdevopsjira.fsc.atos-services.net/issues/?filter=27268&jql=project%20%3D%20DHC%20AND%20issuetype%20%3D%20Bug%20AND%20resolution%20%3D%20Unresolved%20AND%20fixVersion%20%3D%20%22DHC%201.4%22%20ORDER%20BY%20resolution%20ASC%2C%20created%20DESC%2C%20status%20DESC)

# Planned and Upcoming Improvements V1.5

The Following features or capabilities did not make it into the current DHC version. They are, however, planned for inclusion in a later release pending capacity and business case.

![Feature](images/hldReleaseNotesDHC/feature.svg) [Manage customer storage classes via VMware portal](https://atos-ppm.aha.io/features/DPC-593)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Automated, additional Active/passive cluster deployment](https://atos-ppm.aha.io/features/DPC-591)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Multi Tenant A/P DR Enhancements](https://atos-ppm.aha.io/features/DPC-588)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Active/Passive Passive/Active DR enhancement. Bi-directional](https://atos-ppm.aha.io/features/DPC-571)

![Feature](images/hldReleaseNotesDHC/feature.svg) [LCM to VCF 4.3. Including Greenfield Deployment Capability](https://atos-ppm.aha.io/features/DPC-466)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Basic Tanzu Enablement](https://atos-ppm.aha.io/features/DPC-489)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Azure Public Endpoint integration PoC](https://atos-ppm.aha.io/features/DPC-564)

# Digital Hybrid Cloud Release V1.3

This is the 4th release of DHC designed to make the platform production ready and integrated with Atos CMP2.0 offering.

| Item                                 | Details    |
| :----------------------------------- | :--------- |
| External Release Version             | DHC V1.3   |
| Release Date                         | 12/06/2021 |
| Prior Release                        | DHC V1.2   |
| Planned Upcoming Release             | DHC V1.4   |
| Support Expires on release of:       | Release 1.6|
| Notes Last Updated                   | 09/06/2021 |

## Release Summary and New Features V1.3

This release includes the following new features:

![Feature](images/hldReleaseNotesDHC/feature.svg) [DHC platform components upgraded to VCF 4.1](https://msdevopsjira.fsc.atos-services.net/browse/DHC-276)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Multi-Tenancy Enablement](https://msdevopsjira.fsc.atos-services.net/browse/DHC-245)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Enablement of LCM upgrades between DHC versions](https://msdevopsjira.fsc.atos-services.net/browse/DHC-286)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Creation of DPC to DHC Migration path](https://msdevopsjira.fsc.atos-services.net/browse/DHC-77)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Automated Additional Cluster Enablement](https://msdevopsjira.fsc.atos-services.net/browse/DHC-595)

![Feature](images/hldReleaseNotesDHC/feature.svg) [CFM Integration and Enablement](https://msdevopsjira.fsc.atos-services.net/browse/DHC-1122)

![Feature](images/hldReleaseNotesDHC/feature.svg) [DevOps Support Model Enablement](https://msdevopsjira.fsc.atos-services.net/browse/DHC-1512)

![Feature](images/hldReleaseNotesDHC/feature.svg) [DHC ServiceNow CMDB discovery integration](https://msdevopsjira.fsc.atos-services.net/browse/DHC-1437)

![Change](images/hldReleaseNotesDHC/change.svg) [Enablement of vRA-Cloud\Service Broker as user facing portal or service Catalog](https://msdevopsjira.fsc.atos-services.net/browse/DHC-590)

![Change](images/hldReleaseNotesDHC/change.svg) Addition of vLCM component for vSAN Witness creation

# DHC Software Stack Versions V1.3

DHC is based on a number of software components. The current versions are listed in the table below:

| Component                                                 | Version     | End Of Support |Notes                   |
| :-------------------------------------------------------- | :---------- | :------------- | :---------------------- |
| Cloud Builder VM                                          | 4.1         | 02-10-2023     | N/A                     |
| SDDC Manager                                              | 4.1         | 02-10-2023     | N/A                     |
| VMware vCenter Server Appliance                           | 7.0 U1      | 02-10-2025     | N/A                     |
| VMware ESXi                                               | 7.0 U1      | 02-10-2025     | N/A                     |
| VMware vSAN                                               | 7.0 U1      | 02-10-2025     | N/A                     |
| VMware NSX-T Data Center                                  | 3.0.2       | 02-10-2025     | N/A                     |
| VMware vRealize Suite Lifecycle Manager                   | 8.1 P1      | 17-10-2021     | N/A                     |
| VMware vRealize Log Insight                               | 8.1.1       | 17-10-2021     | N/A                     |
| Workspace One Identity Manager                            | 3.3.2       | 17-10-2021     | N/A                     |
| vRealize Automation (NOT EXPOSED IN THIS VERSION)         | 8.1 P2      | TBC            | N/A                     |
| vRealize Log insight Content Pack for NSX-T               | 3.9.1       | N/A            | N/A                     |
| vRealize Log Insight Content Pack for Linux               | 2.1         | N/A            | N/A                     |
| vRealize Log Insight Content Pack for Linux (SystemD)     | 1.0         | N/A            | N/A                     |
| vRealize Log insight Content Pack for vRSLCM              | 1.0         | N/A            | N/A                     |
| vRealize Log insight Content Pack for vIDM (WSO)          | 2.0         | N/A            | N/A                     |
| vRealize Operations Manager                               | 8.1.1       | 09-07-2022     | N/A                     |
| vROps Content Pack for vIDM                               | 1.1         | N/A            | N/A                     |
| vROps Content Pack for vSphere Replication                | 8.3         | N/A            | N/A                     |
| vROps Content Pack for SRM                                | 8.3         | N/A            | N/A                     |
| vSAN Content Pack for vRLI                                | 2.2         | N/A            | N/A                     |
| vROps Content Pack for NSX-T                              | 8.1         | N/A            | N/A                     |
| Windows Servers                                           | Server 2016 | 01/12/2027     | Patched by MGMT Cluster |
| Ubuntu Server                                             | 18.04.5 LTS | 04/01/2028     | Supported By Canonical  |
| Hashicorp Vault                                           | 1.3.2       | N/A            | Supported by Canonical  |
| CloudLink KMS (Optional)                                  | 6.9.0       | N/A            | For vSAN Encryption     |
| CEB (AVAMAR)                                              | 19.3        | N/A            | Check 19.3              |
| Trend Micro Deep Security                                 | 12          | N/A            | N/A                     |
| Ansible                                                   | 2.8.8       | N/A            | N/A                     |
| Nessus                                                    | 8.8.0       | N/A            | N/A                     |
| Infoblox                                                  | 8.4.4       | N/A            | N/A                     |
| vRealize Network Insight                                  | 5.1.0       | 31-10-2021     | N/A                     |
| Python                                                    | 3.8.0       | 14/10/2024     | Part of Selenium        |
| VMware HCX                                                | R145        | 08/05/2022     | Optional Install        |
| VMware SRM                                                | 8.3.1       | 04/01/2023     | Optional Install        |
| vSphere Replication Server                                | 8.3         | 04/01/2023     | Optional Install        |
| vLCM                                                      | 8.1.0.25    | 31/10/2022     | vSAN Witness Creation   |

# Resolved Limitations V1.3

The following items from previous releases have been fixed and no longer limit the product.

![Fixed](images/hldReleaseNotesDHC/fixed.svg) **NTP:** The NTP design of DHC has been enhanced to remove the single point of failure and become more complete across different operating systems.

![Fixed](images/hldReleaseNotesDHC/fixed.svg) **Security:** Alcatraz scan import testing functionality now available. The fix is detailed in story DHC-634

![Fixed](images/hldReleaseNotesDHC/fixed.svg) **Automation:** vSAN encryption on cluster setup now automated (Fixed via VCF 4.1)

![Fixed](images/hldReleaseNotesDHC/fixed.svg) **HCX:** HCX is now compatible with DHC in this version using out of the box release.

# Known Limitations V1.3

Several features within this DHC release are marked as BETA release features. These functionalities do not come with any associated SLA and are intended as technical previews only.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Networking:** If Active\Passive DR is enabled then any customer specific changes to NSX-T after initial setup must be manually replicated on the DR site as there is no mechanism to sync between the management stacks in this release in Active\passive DR. NOTE: as there are no SDN SSRs available in this release the chances of encountering this limitation are low but operations should be aware of the limitation.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Logging:** vRNI currently has a limited number of data sources. This will be enhanced in a later release as the vendor allows.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **DR:** SRM Enterprise currently has a limit of 2000 DR protected Virtual machines per install. As it is not possible to assign a 2nd instance to a vCenter already managed by SRM. This puts the practical DHC DR limit at 2000 VMs (per DHC master DHC MGMT cluster). This is a vendor technology limitation at this time.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Automation:** Due to a lack of, or buggy, API the following bring up tasks are currently manually done in DHC.  These are all being rectified with help from the appropriate vendor.
    - RBAC implementation for SDDC Manager and vIDM is still manual
    - Self-signed vIDM Certificate is in place at this time.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **VxRail:** In this version, VxRail is not a fully automated install, config and LCM option. VxRail is compatible with DHC but there are some manual steps and procedures when running on this HW.  

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **CMP2.0:** If CMP2.0 is the ITSM of choice then Active/Passive DR is not available as CMP2.0 has no concept of the duplicate (DR) VM CIs and iss, therefore, not compatible with the feature.

# Resolved Bugs and Known Issues V1.3

There are an additional **213** low level bugs fixed in this release of DHC and these can be found at the following JIRA link:

![Fixed](images/hldReleaseNotesDHC/fixed.svg) [Jira filter showing Fixed bugs for DHC Version 1.3](https://msdevopsjira.fsc.atos-services.net/issues/?filter=27268&jql=project%20%3D%20DHC%20AND%20issuetype%20%3D%20Bug%20AND%20resolution%20in%20(Fixed%2C%20Done)%20AND%20fixVersion%20%3D%20%22DHC%201.3%22%20ORDER%20BY%20resolution%20ASC%2C%20created%20DESC%2C%20status%20DESC)

# Bugs and Known Issues V1.3

This section details the known issues in this release and their relevant JIRA Items.

![Issue](images/hldReleaseNotesDHC/issue.svg) **MGMT Domain Firewall Issue:** - There is an issue in  the management domain where, if already inside the MGMT cluster, microsegmentation between various management components can be bypassed if they belong to the same VIF connection.  This is being remedied via a redesign and advice that will be backported to 1.2.1 and this 1.3 release but is not ready in time for this specific release of the product.  It should be noted that this is only an issue if all other security measures are breached and access is gained to the MGMT stack. **NOTE** this is being remediated by moving all non core infrastructure VMs off to a separated network area within the MGMT domain.

![Issue](images/hldReleaseNotesDHC/issue.svg) **VRA Cloud:** - After a DR event (Failover / Fail-back), the name of the VM in Cloud Assembly is returned as empty. However, without a value in this field onboarding of failed over VM can not start. This is due to a VMware bug and as a consequence customer VMs cannot be onboarded after failover into vRA (see VMware SR 20175585711). Manual workaround available: ON a fail back, all VMS must be imported in to a NEW instance of vRA to be able to be registered correctly. Vendor is expecting to get a permanent solution for this (i.e. failing back to existing vRA instance to be available in June 2021. Impact: customer workload VMs after DR failover cannot be seen or managed in vRA until onboarding into a temporary vRA instance.

![Issue](images/hldReleaseNotesDHC/issue.svg) **Licensing:** Due to the way production licences are delivered there may be additional steps when entering keys in to the PreReqVM. We have been unable to test with "live" licences and are aware that they may differ in delivery format (suite Licences) from Development ones. Any issues may be addressed once there is a proven deployment pattern.

![Issue](images/hldReleaseNotesDHC/issue.svg) [Remaining minor bugs in DHC 1.3](https://msdevopsjira.fsc.atos-services.net/issues/?filter=27268&jql=project%20%3D%20DHC%20AND%20issuetype%20%3D%20Bug%20AND%20resolution%20%3D%20Unresolved%20AND%20fixVersion%20%3D%20%22DHC%201.3%22%20ORDER%20BY%20resolution%20ASC%2C%20created%20DESC%2C%20status%20DESC)

# Planned and Upcoming Improvements V1.4

The Following features or capabilities did not make it into the current DHC version. They are, however, planned for inclusion in a later release pending capacity and business case.

![Feature](images/hldReleaseNotesDHC/feature.svg) [DHC Production deployment Support](https://atos-ppm.aha.io/features/DPC-578)

![Feature](images/hldReleaseNotesDHC/feature.svg) [NSX-T SSR Development](https://atos-ppm.aha.io/features/DPC-282)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Support For Converged Storage Investigation](https://atos-ppm.aha.io/features/DPC-503)

![Feature](images/hldReleaseNotesDHC/feature.svg) [LCM to VCF 4.2](https://atos-ppm.aha.io/features/DPC-555)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Addition ofStorage Classes into DHC](https://atos-ppm.aha.io/features/DPC-561)

# Digital Hybrid Cloud Release V1.2

This is the 3rd release of DHC designed to make the platform production ready and integrated with Atos CMP2.0 offering.

| Item                                 | Details    |
| :----------------------------------- | :--------- |
| External Release Version             | DHC V1.2   |
| Release Date                         | XX/XX/XXXX |
| Prior Release                        | DHC V1.1   |
| Planned Upcoming Release             | DHC V1.3   |
| Support Expires on release of:       | Release 1.5|
| Notes Last Updated                   | 26/11/2020 |

## Release Summary and New Features V1.2

This release includes the following new features:

![Feature](images/hldReleaseNotesDHC/feature.svg) [DHC platform components changed to VCF4.0 Including NSX-T](https://msdevopsjira.fsc.atos-services.net/browse/DPC-22415)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Asynchronous, Active/Passive, DR capability for DHC](https://msdevopsjira.fsc.atos-services.net/browse/DPC-18645)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Vulnerability scanning solution for Single Tenant DHC installs is enabled (NESSUS)](https://msdevopsjira.fsc.atos-services.net/browse/DPC-22647)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Automated VM template sync across sites is enabled](https://msdevopsjira.fsc.atos-services.net/browse/DPC-22650)

![Change](images/hldReleaseNotesDHC/change.svg) [DHC Management Domain now uses NSX-T for networking and this has been adapted to support active/active DR as per DHC 1.1](https://msdevopsjira.fsc.atos-services.net/browse/DPC-19890)

![Change](images/hldReleaseNotesDHC/change.svg) [DHC Service Reporting enhancements](https://msdevopsjira.fsc.atos-services.net/browse/DPC-21307 )

![Change](images/hldReleaseNotesDHC/change.svg) Lifecycle Management of OS templates and DHC IaaS Platform as a whole (up to date VCF and MGMT stack).

![Beta](images/hldReleaseNotesDHC/beta.svg) [VxRail deployment enablement Manual](https://msdevopsjira.fsc.atos-services.net/browse/DPC-22717)

# DHC Software Stack Versions V1.2

DHC is based on a number of software components. The current versions are listed in the table below:

| Component                                                    | Version       | Notes                     |
| :----------------------------------------------------------- | :------------ | :------------------------ |
| Cloud Builder VM                                             | 4.0.0         | N/A                       |
| SDDC Manager                                                 | 4.0.0         | N/A                       |
| VMware vCenter Server Appliance                              | 7.0           | N/A                       |
| VMware ESXi                                                  | 7.0           | N/A                       |
| VMware vSAN                                                  | 7.0           | N/A                       |
| VMware NSX-T Data Center                                     | 3.0           | N/A                       |
| VMware Enterprise PKS                                        | REMOVED       | N/A                       |
| VMware vRealize Suite Lifecycle Manager                      | 8.1P1         | N/A                       |
| VMware vRealize Log Insight                                  | 8.1           | N/A                       |
| vRealize Log Insight Content Pack for NSX for vSphere        | REMOVED       | /NA                       |
| vRealize Log Insight Content Pack for Linux                  | 2.1           | N/A                       |
| vRealize Log Insight Content Pack for vRealize Orchestrator  | 2.1           | N/A                       |
| vRealize Log insight Content Pack for NSX-T                  | 3.9.1         | N/A                       |
| vSAN Content Pack for vRLI                                   | 2.2           | N/A                       |
| Content Pack for vSphere                                     | 8.1           | N\A                       |
| Content Pack General                                         | 4.4           | N\A                       |
| vRealize Operations Manager                                  | 8.1           | N/A                       |
| Windows Servers                                              | Server 2016   | Patched by MGMT Cluster   |
| Ubuntu Server                                                | 18.04.4 LTS   | Supported By Canonical    |
| Hashicorp Vault                                              | 1.3.2         | Supported by Canonical    |
| CloudLink KMS (Optional)                                     | 6.9           | For vSAN Encryption       |
| CEB (AVAMAR)                                                 | 19.3          | Check 19.3                |
| Trend Micro Deep Security                                    | 12            | N/A                       |
| Ansible                                                      | 2.8.8         | N/A                       |
| Nessus                                                       | 8.8.0         | N/A                       |
| Infoblox                                                     | 8.4.4         | N/A                       |
| vRealize Network Insight                                     | 5.1.0         | N/A                       |
| Python                                                       | 3.x           |                           |
| VMware HCX                                                   | R143          | Optional Install          |
| VMware SRM                                                   | 8.2           |                           |

# Resolved Limitations V1.2

The following items from previous releases have been fixed and no longer limit the product.

![Fixed](images/hldReleaseNotesDHC/fixed.svg) VM Provisioning: There is no ability to manually pin VMs to sides of an availability zone in this release. This has been remediated and is now possible.

![Fixed](images/hldReleaseNotesDHC/fixed.svg) RBAC implementation for vRLI and vROps is now automated as the design intended.

![Fixed](images/hldReleaseNotesDHC/fixed.svg) [CSA Register Assessment Refresh](https://msdevopsjira.fsc.atos-services.net/browse/DPC-22643)

![Fixed](images/hldReleaseNotesDHC/fixed.svg) Stretched cluster for WORKLOAD Domain is now an automated process (although initiated manually due to requirement for Witness to be available in customer site)

# Known Limitations V1.2

Several features within this DHC release are marked as BETA release features. These functionalities do not come with any associated SLA and are intended as technical previews only.

![Issue](images/hldReleaseNotesDHC/issue.svg) **VRA Cloud:** - After a DR event (Failover / Failback), the name of the VM in Cloud Assembly is returned as empty. However, without a value in this field onboarding of failed over VM can not start. This is due to a VMware bug and as a consequence customer VMs cannot be onboarded after failover into vRA (see VMware SR 20175585711). Manual workaround available: ON a fail back, all VMS must be imported in to a NEW instance of vRA to be able to be registered correctly. Vendor is expecting to get a permanent solution for this (i.e. failing back to existing vRA instance to be available in June 2021. Impact: customer workload VMs after DR failover cannot be seen or managed in vRA until onboarding into a temporary vRA instance.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Networking:** If Active\Passive DR is enabled then any customer specific changes to NSX-T after initial setup must be manually replicated on the DR site as there is no mechanism to sync between the management stacks in this release in Active\passive DR. NOTE: as there are no SDN SSRs available in this release the chances of encountering this limitation are low but operations should be aware of the limitation.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **NTP:** The PDC emulator FSMO role is a single point of failure (within a Resilient ADC design). IF the role is offline and not seized by another controller DHC will be unable to sync time with an external source.  A fix is under investigation [Here](https://msdevopsjira.fsc.atos-services.net/browse/DHC-674)

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Logging:** vRNI currently has a limited number of data sources. This will be enhanced in a later release as the vendor allows.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **DR:** SRM Enterprise currently has a limit of 2000 DR protected Virtual machines per install. As it is not possible to assign a 2nd instance to a vCenter already managed by SRM. This puts the practical DHC DR limit at 2000 VMs (per DHC master DHC MGMT cluster). This is a vendor technology limitation at this time.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Security:** Alcatraz scan import testing functionality not available. This is now in progress via DHC-634

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **Automation:** Due to a lack of, or buggy, API the following bring up tasks are currently manually done in DHC.  These are all being rectified with help from the appropriate vendor.
    - Enable vSAN encryption on cluster (Fixed in VCF 4.1 and will be implemented in next release)
    - RBAC implementation for SDDC Manager and vIDM is still manual
    - Self-signed vIDM Certificate is in place at this time.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **VxRail:** In this version, VxRail is not a fully automated install, config and LCM option. VxRail is compatible with DHC but there are some manual steps and procedures when running on this HW.  

![Limitation](images/hldReleaseNotesDHC/limitation.svg) **CMP2.0:** If CMP2.0 is the ITSM of choice then Active/Passive DR is not available as CMP2.0 has no concept of the duplicate (DR) VM CIs and iss, therefore, not compatible with the feature.

# Resolved Bugs and Known Issues V1.2

There are an additional 111 low level bugs fixed in this release of DHC and these can be found at the following JIRA link:

![Fixed](images/hldReleaseNotesDHC/fixed.svg) [Jira filter showing Fixed bugs for DHC Version 1.2](https://msdevopsjira.fsc.atos-services.net/issues/?filter=27268&jql=project%20%3D%20DPC%20AND%20issuetype%20%3D%20Bug%20AND%20resolution%20in%20(Fixed%2C%20Done)%20AND%20fixVersion%20%3D%20DHC-1.2.0%20ORDER%20BY%20resolution%20ASC%2C%20created%20DESC%2C%20status%20DESC&startIndex=100)

# Bugs and Known Issues V1.2

This section details the known issues in this release and their relevant JIRA Items.

![Issue](images/hldReleaseNotesDHC/issue.svg) **VRA Cloud:** - After a DR event (Failover / Failback), the name of the VM in Cloud Assembly is returned as empty. However, without a value in this field onboarding of failed over VM can not start. This is due to a VMware bug and as a consequence customer VMs cannot be onboarded after failover into vRA (see VMware SR 20175585711). Manual workaround available: ON a fail back, all VMS must be imported in to a NEW instance of vRA to be able to be registered correctly. Vendor is expecting to get a permanent solution for this (i.e. failing back to existing vRA instance to be available in June 2021. Impact: customer workload VMs after DR failover cannot be seen or managed in vRA until onboarding into a temporary vRA instance.

![Issue](images/hldReleaseNotesDHC/issue.svg) **HCX:** HCX is not compatible with DHC in this version using out of the box release. For HCX to be used the software must be manually upgraded to R143. Vendor Limitation.

![Issue](images/hldReleaseNotesDHC/issue.svg) **Licencing:** Due to the way production licences are delivered there may be additional steps when entering keys in to the PreReqVM. We have been unable to test with "live" licences and are aware that they may differ in delivery format (suite Licences) from Development ones. Any issues may be addressed once there is a known format.

![Issue](images/hldReleaseNotesDHC/issue.svg) [Remaining minor bugs in DHC 1.2](https://msdevopsjira.fsc.atos-services.net/issues/?filter=27268&jql=project%20%3D%20DPC%20AND%20issuetype%20%3D%20Bug%20AND%20resolution%20%3D%20Unresolved%20AND%20fixVersion%20%3D%20DHC-1.2.0%20ORDER%20BY%20resolution%20ASC%2C%20created%20DESC%2C%20status%20DESC)

# Planned and Upcoming Improvements V1.3

The Following features or capabilities did not make it into the current DHC version. They are, however, planned for inclusion in a later release pending capacity and business case.

![Feature](images/hldReleaseNotesDHC/feature.svg) [Application aware backup capability](https://msdevopsjira.fsc.atos-services.net/browse/DPC-21524)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Management of Backup and DR from portal/API](https://msdevopsjira.fsc.atos-services.net/browse/DPC-18939)

![Feature](images/hldReleaseNotesDHC/feature.svg) [Demo Environment and Portal for Product Market / Vertical focused DHC Demos](https://msdevopsjira.fsc.atos-services.net/browse/DPC-21275)

![Feature](images/hldReleaseNotesDHC/feature.svg) Multi-Tenancy for DHC using vRA-Cloud

![Change](images/hldReleaseNotesDHC/change.svg) Multiple Workload domains automated for a single DHC

![Feature](images/hldReleaseNotesDHC/change.svg) Full automated LCM pipeline and upgrade for DHC enablement

# Digital Hybrid Cloud Release V1.1

This is the 2nd release of DHC designed to make the platform production ready and integrated with Atos CMP2.0 offering.

| Item                                 | Details    |
| :----------------------------------- | :--------- |
| External Release Version             | DHC V1.1   |
| Release Date                         | 11/05/2020 |
| Prior Releases                       | DHC V1.0   |
| Planned Upcoming Release             | DHC V1.2   |
| Support Expires on release of:       | Release 1.4|
| Notes Last Updated                   | 23/04/2020 |

## Release Summary and New Features V1.1

This release includes the following new features:

![Feature](images/hldReleaseNotesDHC/feature.svg) Integration with  Atos CMP 2.0 offering (Portal, ServiceNOW CMP and service catalog).

![Feature](images/hldReleaseNotesDHC/feature.svg) Enable the use of vRA-Cloud portal as an alternative Service Catalog.

![Feature](images/hldReleaseNotesDHC/feature.svg) Enable a full vROps reporting portable, accessible by customers and account teams.

![Feature](images/hldReleaseNotesDHC/feature.svg) Integrate automation of DNS provisioning (Customer Workload) into DHC.

![Feature](images/hldReleaseNotesDHC/feature.svg) Enable support for Dell VxRail hardware (Manual deployment) and compatibility with DHC VCF implementation and operations.

![Feature](images/hldReleaseNotesDHC/feature.svg) PKS as an IaaS offering moved out of BETA to Production status (No managed service).

![Feature](images/hldReleaseNotesDHC/feature.svg) Enable customer charging, metering and billing functionality.

![Change](images/hldReleaseNotesDHC/change.svg) Google Anthos on DHC: This version of DHC is now compatible with the Atos Anthos add-on service (delivered by another part of Atos).

![Change](images/hldReleaseNotesDHC/change.svg) Provide multiple monitoring and ITSM integration improvements (Ticketing, alerting etc via vROPps to ITSM platform).

![Change](images/hldReleaseNotesDHC/change.svg) Provide enhancements to DHC deployment automation capability (reliability, error reporting and rollback).

![Change](images/hldReleaseNotesDHC/change.svg) Enhancements to application blueprinting capability within DHC.

![Change](images/hldReleaseNotesDHC/change.svg) Lifecycle Management of OS templates and DHC IaaS Platform as a whole (up to date VCF and MGMT stack).

![Beta](images/hldReleaseNotesDHC/beta.svg) Initial engineering implementation of basic API gateway functionality (Design/Basic vRA-Cloud use only, not for consumption).

# DHC Software Stack Versions V1.1

DHC is based on a number of software components. The current versions are listed in the table below:

| Component                                                             | Version         | Build    | Notes                        |
| :-------------------------------------------------------------------- | :-------------- | :------- | :--------------------------- |
| Cloud Builder VM                                                      | 2.2.1.0         | 14866160 | N/A                          |
| SDDC Manager                                                          | 3.9.1           | 14866160 | N/A                          |
| VMware vCenter Server Appliance                                       | 6.7 Update 3b   | 14367737 | N/A                          |
| VMware ESXi                                                           | 6.7 Update 3b   | 14320388 | N/A                          |
| VMware vSAN                                                           | 6.7 Update 3b   | 14263135 | N/A                          |
| VMware NSX Data Center for vSphere                                    | 6.4.6           | 13282012 | N/A                          |
| VMware NSX-T Data Center                                              | 2.5             | 14663974 | N/A                          |
| VMware Enterprise PKS                                                 | 1.5             | 14878150 | N/A                          |
| VMware vRealize Suite Lifecycle Manager                               | 2.1 Patch 2     | 14062628 | N/A                          |
| VMware vRealize Log Insight                                           | 4.8             | 14062628 | N/A                          |
| vRealize Log Insight Content Pack for NSX for vSphere                 | 3.9             | N/A      | N/A                          |
| vRealize Log Insight Content Pack for Linux                           | 2.0.1           | N/A      | N/A                          |
| vRealize Log Insight Content Pack for vRealize Orchestrator 7.0.1+    | 2.1             | N/A      | N/A                          |
| vRealize Log insight Content Pack for NSX-T                           | 3.8.2           | N/A      | N/A                          |
| vSAN Content Pack for Log Insight                                     | 2.2             | N/A      | N/A                          |
| vRealize Operations Manager                                           | 7.5             | 13165949 | N/A                          |
| Windows Servers                                                       | Server 2016     | N/A      | Patched by MGMT Cluster      |
| Ubuntu Server                                                         | 18.04.3 LTS     | N/A      | Supported By Canonical       |
| Hashicorp Vault                                                       | 1.3.2           | N/A      | Supported by Canonical       |
| CloudLink KMS (Optional)                                              | 6.9             | 899.7    | For vSAN Encryption          |
| CEB (AVAMAR)                                                          | 18.2            | N/A      | N/A                          |
| Trend Micro Deep Security                                             | 12.x            | N/A      | N/A                          |
| Ansible                                                               | 2.8.5           | N/A      | N/A                          |
| Nessus                                                                | 8.8.0           | N/A      | N/A                          |
| Infoblox                                                              | 8.4.4           | N/A      | N/A                          |
| vRealize Network Insight                                              | 4.1.1           | N/A      | N/A                          |

# Resolved Limitations V1.1

The following items from previous releases have been fixed and no longer limit the product.

![Fixed](images/hldReleaseNotesDHC/fixed.svg) NSX: Connectivity to HCX is currently enabled via a workaround. Vendor bug.

![Fixed](images/hldReleaseNotesDHC/fixed.svg) Automation: The following automation bugs have been fixed in this version:
    - Tag creation in vRA Cloud for cluster/Resource pool

# Known Limitations V1.1

Several features within this DHC release are marked as BETA release features. These functionalities do not come with any associated SLA and are intended as technical previews only.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) HCX: In this release the deployment of HCX is manual (not automated). Vendor Limitation.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) vRNI currently has a limited number of data sources. This will be enhanced in the next release.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) Tenancy: Multi Tenant vRA-Cloud setup (As defined) is manual and limited to (Beta) status. Not to be deployed.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) Security: Alcatraz scan import testing functionality not available (Blocked by vROps2ITX tooling issue)..

![Limitation](images/hldReleaseNotesDHC/limitation.svg) VM Provisioning: New and Migrated (By HCX) VMs and randomly assigned and distributed within an Availability Zone when running in an Active/Active stretched cluster. i.e. no manual placement ability.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) VM Provisioning: There is no ability to manually pin VMs to sides of an availability zone in this release.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) Automation: Due to a lack of, or buggy, API the following bring up tasks are currently manually done in DHC.  These are all being rectified with the appropriate vendor.
    - Enable vSAN encryption on cluster (Fixed in VCF 4.0 and will be implemented in next release)
    - RBAC implementation for vRLI, vROps and SDDC Manager
    - Replacing Self-signed vIDM Certificate
    - Enabling stretched cluster for WORKLOAD Domain (This is fixed in VCF 4.0 and will be implemented next release).

# Resolved Bugs and Known Issues V1.1

![Fixed](images/hldReleaseNotesDHC/fixed.svg) DPC-20520 - Some automatically created storage policies can be incompatible with vSAN datastore when created as part of a new Workload Domain.  Workaround available (Manual recreation).

# Bugs and Known Issues V1.1

This section details the known issues in this release and their relevant JIRA Items.

![Issue](images/hldReleaseNotesDHC/issue.svg) DPC-19569 - Changelog not available on Debian based Linux VM images (Vendor to fix) Currently blocked by contractual support negotiations

![Issue](images/hldReleaseNotesDHC/issue.svg) DPC-20312 - Incorrect vROPS MGMT packs being automatically downloaded during setup.  Known VMware Marketplace bug.  Workaround is manual download of correct version during setup.

# Planned and Upcoming Improvements V1.2

The Following features or capabilities did not make it into the current DHC version.  They are, however, planned for inclusion soon.

![Feature](images/hldReleaseNotesDHC/feature.svg) Transition of public cloud endpoint feature from Beta to production feature with support for Azure, AWS and GCP

# Digital Hybrid Cloud Release V1.0

This is the initial release of DHC and the first in the chain of Release documentation.

| Item                                 | Details    |
| :----------------------------------- | :--------- |
| External Release Version             | DHC V1.0   |
| Release Date                         | 19/12/2019 |
| Prior Releases                       | N/A        |
| Planned Upcoming Release             | DHC V1.1   |
| Support Expires on release of:       | Release 1.3|
| Notes Last Updated                   | 06/01/2020 |

## Release Summary and New Features V1.0

This release includes the following new features:

![Feature](images/hldReleaseNotesDHC/feature.svg) Define the IaaS stack and architecture for the product moving forward

![Feature](images/hldReleaseNotesDHC/feature.svg) Enable DHC to be installed and deployed to customer site on Hyperconverged infrastructure

- i.e. Initial Release

![Feature](images/hldReleaseNotesDHC/feature.svg) Provide automation of the DHC install, bring up and configuration process with minimal user interaction from RS&D

- For VCF, DHC Management and associated Workload Domains

![Feature](images/hldReleaseNotesDHC/feature.svg) Provide automated rollback of install and configuration to the last known good point to allow remediation of bad configuration file parameters with minimal disruption

![Feature](images/hldReleaseNotesDHC/feature.svg) Enable version control of customer configuration via the use of "DHC install as configuration item" via the use of Ansible plus Gitlab as the command and control mechanism for all DHC installs

![Feature](images/hldReleaseNotesDHC/feature.svg) Enable a VVD compliant install and configuration of the base IaaS layer (VCF) with full management via DHC and conforming to all usual security and design best practices

![Feature](images/hldReleaseNotesDHC/feature.svg) Enable the development and update pipeline to allow for LCM in an automated fashion (DHC Push)

![Feature](images/hldReleaseNotesDHC/feature.svg) Integrate testing into the development and provide automation for evidence gathering required for Atos ToS procedures

![Feature](images/hldReleaseNotesDHC/feature.svg) Enable Workload migration to DHC via the use of VMware HCX tool and specific onboarding automation

![Feature](images/hldReleaseNotesDHC/feature.svg) Enable integration with Atos CMP2.0 CMP and ITSM frontend

![Feature](images/hldReleaseNotesDHC/feature.svg) Enable Stretched Cluster support for the DHC management cluster to support Active/Active deployments

![Feature](images/hldReleaseNotesDHC/feature.svg) Enable customers to deploy new VMs from within DHC on Prem

![Feature](images/hldReleaseNotesDHC/feature.svg) Enable CMDB synchronisation with Atos ATF 2.0

![Feature](images/hldReleaseNotesDHC/feature.svg) Enable IPAM capability using InfoBlox (Industry standard)

![Feature](images/hldReleaseNotesDHC/feature.svg) Enable full monitoring and logging integration with the ITSM of choice

![Feature](images/hldReleaseNotesDHC/feature.svg) Enable metering and charging capability for DHC

![Feature](images/hldReleaseNotesDHC/feature.svg) Enable backup integration with DHC and CEB (Avamar Default)

![Feature](images/hldReleaseNotesDHC/feature.svg) Enable AV integration with BDS Deep security product to provide management cluster endpoint protection

![Beta](images/hldReleaseNotesDHC/beta.svg) Enable PKS functionality in DHC as a BETA capability with no SLA or managed service attached.

![Beta](images/hldReleaseNotesDHC/beta.svg) Enable the ability to use application blueprints within DHC in an unmanaged fashion in the beta preview mode

![Beta](images/hldReleaseNotesDHC/beta.svg) Enable Public Cloud Endpoints as targets for VM provisioning

# DHC Software Stack Versions V1.0

DHC is based on a number of software components. The current versions are listed in the table below:

| Component                                                             | Version         | Build    | Notes                        |
| :-------------------------------------------------------------------- | :-------------- | :------- | :--------------------------- |
| Cloud Builder VM                                                      | 2.2.0.0         | 14866160 | N/A                          |
| SDDC Manager                                                          | 3.9             | 14866160 | N/A                          |
| VMware vCenter Server Appliance                                       | 6.7 Update 3    | 14367737 | N/A                          |
| VMware ESXi                                                           | 6.7 Update 3    | 14320388 | N/A                          |
| VMware vSAN                                                           | 6.7 Update 3    | 14263135 | N/A                          |
| VMware NSX Data Center for vSphere                                    | 6.4.5           | 13282012 | N/A                          |
| VMware NSX-T Data Center                                              | 2.5             | 14663974 | N/A                          |
| VMware Enterprise PKS                                                 | 1.5             | 14878150 | N/A                          |
| VMware vRealize Suite Lifecycle Manager                               | 2.1 Patch 2     | 14062628 | N/A                          |
| VMware vRealize Log Insight                                           | 4.8             | 14062628 | N/A                          |
| vRealize Log Insight Content Pack for NSX for vSphere                 | 3.9             | N/A      | N/A                          |
| vRealize Log Insight Content Pack for Linux                           | 1.0             | N/A      | N/A                          |
| vRealize Log Insight Content Pack for vRealize Orchestrator 7.0.1+    | 2.1             | N/A      | N/A                          |
| vRealize Log insight Content Pack for NSX-T                           | 3.8             | N/A      | N/A                          |
| vSAN Content Pack for Log Insight                                     | 2.1             | N/A      | N/A                          |
| vRealize Operations Manager                                           | 7.5             | 13165949 | N/A                          |
| Windows Servers                                                       | Server 2016     | N/A      | Patched by MGMT Cluster      |
| Ubuntu Server                                                         | 18.04.3 LTS     | N/A      | Supported By Canonical       |
| Hashicorp Vault                                                       | 1.3.0           | N/A      | Supported by Canonical       |
| CloudLink KMS (Optional)                                              | 6.5             | N/A      | For vSAN Encryption          |
| CEB (AVAMAR)                                                          | 18.2            | N/A      | N/A                          |
| Trend Micro Deep Security                                             | 12.x            | N/A      | N/A                          |
| Ansible                                                               | 2.8.5           | N/A      | N/A                          |
| Nessus                                                                | 8.8.0           | N/A      | N/A                          |

# Known Limitations V1.0

Several features within this DHC release are marked as BETA release features. These functionalities do not come with any associated SLA and are intended as technical previews only.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) HCX: In this release the deployment of HCX is manual (not automated). Vendor Limitation

![Limitation](images/hldReleaseNotesDHC/limitation.svg) NSX: Connectivity to HCX is currently enabled via a workaround.  Vendor bug.

![Limitation](images/hldReleaseNotesDHC/limitation.svg) Cloud Health import data to Atos FIT is currently manual (Time limitation)

![Limitation](images/hldReleaseNotesDHC/limitation.svg) Automation: Due to a lack of, or buggy, API the following bring up tasks are currently manually done in DHC.  These are all being rectified with the appropriate vendor.
    - Enable vSAN encryption on cluster
    - RBAC implementation for vRLI, vROps and SDDC Manager
    - Enabling stretched cluster for WORKLOAD Domain
    - Replacing Self-signed vIDM Certificate
    - Tag creation in vRA Cloud for cluster/Resource pool

# Bugs and Known Issues V1.0

This section details the known issues in this release and their relevant JIRA Items.

![Issue](images/hldReleaseNotesDHC/issue.svg) DPC-19569 - Changelog not available on Debian based Linux VM images (Vendor to fix).

![Issue](images/hldReleaseNotesDHC/issue.svg) DPC-20312 - Incorrect vROPS MGMT packs being automatically downloaded during setup.  Known VMware Marketplace bug.  Workaround is manual download of correct version during setup.

![Issue](images/hldReleaseNotesDHC/issue.svg) DPC-20520 - Some automatically created storage policies can be incompatible with vSAN datastore when created as part of a new Workload Domain.  Workaround available (Manual recreation).

# Planned and Upcoming Improvements (V1.1)

The Following features or capabilities did not make it into the current DHC version.  They are, however, planned for inclusion soon.

![Feature](images/hldReleaseNotesDHC/feature.svg) Google Anthos on DHC: A technology limitation (requires ESXi6.5) meant that Anthos has missed the date for inclusion in DHC.  This is now planned and expected to be a part of the next version of DHC as support for ESXi 6.7 is expected at the end of December 2019.

![Feature](images/hldReleaseNotesDHC/feature.svg) Transition of PKS on DHC from BETA to V1.0 product with the inclusion of relevant SLAs

![Feature](images/hldReleaseNotesDHC/feature.svg) Transition of public cloud endpoint feature from Beta to production feature with support for Azure, AWS and GCP

# Contacts and Further Information

All DHC product Details can be found at the following links:

Detailed DHC service documentation can be found here: [Sales Documentation](https://salesservice.myatos.net/overall/en/index.cfm?obj=191352&e)

DHC MAIA Model (For Solutioning): [MAIA Model](https://sp2013.myatos.net/ms/gst/maia/Pages/MaiaHome.aspx)

Aha! Feature Request Portal: [Request a Feature](https://atos-cloud.ideas.aha.io/)

Detailed DHC design information can be found here: [DHC Design Documentation](.)

For more information on DHC please contact:

- Portfolio: Milena Rauti *`milena.rauti@atos.net`*
- Product Owner: Oliver Scholle *`oliver.scholle@atos.net`*
- Lead Technical Architect: Alec Dunn *`alec.dunn@atos.net`*
