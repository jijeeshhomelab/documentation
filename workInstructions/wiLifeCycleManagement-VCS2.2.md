# Table of Contents

[Table of Contents](wiLifeCycleManagement-VCS2.2.md#table-of-contents)  
[Title](wiLifeCycleManagement-VCS2.2.md#title-wifeaturereleasevcs-2200)  
[List-of-changes](wiLifeCycleManagement-VCS2.2.md#list-of-changes)  
[Introduction](wiLifeCycleManagement-VCS2.2.md#introduction)  
[Scope](wiLifeCycleManagement-VCS2.2.md#scope)  
[Lcm-code-update](wiLifeCycleManagement-VCS2.2.md#lcm-code-update)  
[Work-instructions-for-features-in-scope](wiLifeCycleManagement-VCS2.2.md#work-instructions-for-features-in-scope)  
[Customer Feature: Customer-self-managed-encryption-keys---byok](wiLifeCycleManagement-VCS2.2.md#featureoptional-customer-self-managed-encryption-keys---byok)  
[Customer Feature: Activeactive-stretched-cluster-design-fix-for-independent-witness-connections](wiLifeCycleManagement-VCS2.2.md#featureoptional-activeactive-stretched-cluster-design-fix-for-independent-witness-connections)  
[Customer Feature: Automated-day-2-zero-trust-firewalls-management-using-config-as-code](wiLifeCycleManagement-VCS2.2.md#featureoptional-automated-day-2-zero-trust-firewalls-management-using-config-as-code)  
[Customer Feature: Advanced-load-balancers-with-ipv6-and-db-monitoring](wiLifeCycleManagement-VCS2.2.md#featureoptional-advanced-load-balancers-with-ipv6-and-db-monitoring)  
[Customer Feature: Vcf-5vsphere-8-adjustments-for-fibre-channel-storage](wiLifeCycleManagement-VCS2.2.md#featureoptional-vcf-5vsphere-8-adjustments-for-fibre-channel-storage)  
[LCM: Automate-patching-for-hashivault-and-reverse-proxy](wiLifeCycleManagement-VCS2.2.md#lcm-base-automate-patching-for-hashivault-and-reverse-proxy)  
[Secutiry: AD-security-enhancement-q2-2025](wiLifeCycleManagement-VCS2.2.md#security-mandatory-dhc-ad-security-enhancement-q22025)  
[Secutiry: Security-remediations-2026-pi1](wiLifeCycleManagement-VCS2.2.md#security-mandatory-security-remediations-2026-pi1)  
[Engineering Only: Unified-testing-framework - phase-1](wiLifeCycleManagement-VCS2.2.md#feature-engineering-only-unified-testing-framework---design--build---phase-1)  
[Conclusion](wiLifeCycleManagement-VCS2.2.md#conclusion)  
[Contact-information](wiLifeCycleManagement-VCS2.2.md#contact-information)  

## Title: wiFeatureReleaseVCS-2.2.0.0

## List of Changes

| Date       | Issue    | Author          |  TOS    | Description |  
| ---------- | -------- | --------------- | ------  | ---------------------- |  
| 7/5/2026 |          | Jijeesh Valappil|  2.2.0.0 | Consolidated WI for 2.1.0.0 release scope  |

## Introduction

This document consolidates the work instruction links for each feature within the scope of the VCS-2.2.0.0 release.  
It serves as a single source of reference for teams involved in the release process to ensure smooth deployment and integration of each feature.  

## Scope

The scope of this document covers the work instructions for all features in the VCS-2.2.0.0 release. These features are listed below with links to their respective detailed work instructions.

### LCM code update

Please check if new/updated playbook versions are available. See the `manageDhcRepository.yml` playbook for more information.

#### Code Update Process

Please use following steps to clone DHC repository. Execute all commands as *domain user* logged on Ansible Host. Change:

```bash
axxxxx@ans001:~$ cd
```

Configure git and clone DHC repository:

```bash
git config --global user.name 'User Name' && git config --global user.email 'email.address@atos.net'
git config --global core.hookspath 'hooks' && git config --global credential.helper store && git config --global submodule.recurse true
git config --global http.proxy 'http://< locationCode >pxy001.< customerCode >dhc01.next:3128'
git clone https://github.com/GLB-CES-PrivateCloud/dhc.git --recurse-submodules
cd DHC
git tag
git checkout VCS-2.2.0.0-20260601 ## Kindly check the latest release date as tag
git submodule update --init update
cd update  ## now we can see the all playbooks under the update branch
```  

## Work Instructions for Features in Scope

### Feature(**Optional**): Customer self managed encryption keys - BYOK

   **Work Instruction Link:**  [wiCustomerSelfManagedEncryptionKeys](wiCustomerSelfManagedEncryptionKeys.md) , [onboardingCustomerManagedKMS](onboardingCustomerManagedKMS.md) , [wiBYOKOperationalHealthChecks](wiBYOKOperationalHealthChecks.md)  
   **Design Link:** [lldCustomerSelfManagedEncryptionKeys](../design/lldCustomerSelfManagedEncryptionKeys.md)  
   **Description:**  This release enables customer-owned encryption (BYOK/vSAN DARE) with clear operational guidelines to ensure data sovereignty and regulatory compliance.  
   **Associated Jira Epic:**  [VCS-16550](https://msdevopsjira.fsc.atos-services.net/browse/VCS-16550)  
   **Repo:** DHC-Documentation

### Feature(**Optional**): VCS security scan - CrowdStrike/Tenable.io

   **Work Instruction Link:**  N/A  
   **Design Link:** [lldSoftwareDefinedNetworksFirewall](../design/lldSoftwareDefinedNetworksFirewall.md) , [lldSecurityPosture](../design/lldSecurityPosture.md) , [lldVulnerabilityManagement](../design/lldVulnerabilityManagement.md)  
   **Description:**  This release enables continuous, centralized security posture assessment for the shared platform with automated scanning, unified reporting across tenants, and seamless integration with the existing DHC multi-tenant infrastructure and operational model.  
   **Associated Jira Epic:**  [VCS-17561](https://msdevopsjira.fsc.atos-services.net/browse/VCS-17561)  
   **Repo:** DHC-Documentation , DHC-Firewall

### Feature(**Optional**): Active/Active stretched cluster design fix for independent witness connections

   **Work Instruction Link:**  [dhcStretchedClusterWitnessTrafficSeparation](dhcStretchedClusterWitnessTrafficSeparation.md) , [dhcBuildGuide](dhcBuildGuide.md)  
   **Design Link:** [lldInfrastructure](../design/lldInfrastructure.md)  
   **Description:**  Eliminates site failover outages by using vSAN Witness Traffic Separation and dedicated routing to remove IDCL dependencies, ensuring connectivity remains within the <5-second SLA.  
   **Associated Jira Epic:**  [VCS-318](https://msdevopsjira.fsc.atos-services.net/browse/VCS-318)  
   **Repo:** DHC-Documentation

### Feature(**Optional**): Automated Day 2 zero trust firewalls management using config as code

   **Work Instruction Link:**  [wiZeroTrustFirewalls](wiZeroTrustFirewalls.md)  
   **Design Link:** [lldZeroTrustFirewalls](../design/lldZeroTrustFirewalls.md)  
   **Description:**  Implements Zero Trust in VCS through NSX-T Distributed Firewall automation and "Configuration-as-Code," featuring real-time monitoring, automated delta reporting, and streamlined baseline restoration for improved consistency and auditability.  
   **Associated Jira Epic:**  [VCS-15391](https://msdevopsjira.fsc.atos-services.net/browse/VCS-15391)  
   **Repo:** DHC-Documentation , DHC-Manage , DHC-Collection , DHC-Deploy

### Feature(**Optional**): Advanced Load Balancers with ipv6 and db monitoring

   **Work Instruction Link:**  [wiConfigureALBIPv6](wiConfigureALBIPv6.md)  
   **Design Link:** [lldAdvancedLoadBalancer](../design/lldAdvancedLoadBalancer.md)  
   **Description:**  Delivers advanced dual-stack (IPv4/IPv6) load balancing for SVB migration using AVI ALB, featuring a custom PostgreSQL health monitor with automated Ansible deployment and fixes for NSX IPv6 address assignment.  
   **Associated Jira Epic:**  [VCS-14325](https://msdevopsjira.fsc.atos-services.net/browse/VCS-14325)  
   **Repo:** DHC-Documentation , DHC-Manage

### Feature(**Optional**): vCF 5/vSphere 8 adjustments for fibre channel storage

   **Work Instruction Link:**  [wiUpgradeHBAFirmwareAndDrivers](wiUpgradeHBAFirmwareAndDrivers.md)  
   **Design Link:** [lldInfrastructure](../design/lldInfrastructure.md#physical-storage-design)  
   **Description:**  Enables Fibre Channel (FC) block storage as primary storage for workload domains.  
   **Associated Jira Epic:**  [VCS-9846](https://msdevopsjira.fsc.atos-services.net/browse/VCS-9846)  
   **Repo:** DHC-Documentation

### LCM (**BASE**): Automate patching for HashiVault and reverse proxy

   **Work Instruction Link:**  [wiUpgradeOfReverseProxyapp](wiUpgradeOfReverseProxyapp.md) , [wiVaultUpgrade](wiVaultUpgrade.md)  
   **Design Link:** N/A  
   **Description:**  Automates monthly security patching for DHC 2.x NGINX Reverse Proxies and HashiCorp Vault, featuring version-aware playbooks, automated health validation, and post-upgrade Tenable Nessus scanning.  
   **Associated Jira Epic:**  [VCS-15527](https://msdevopsjira.fsc.atos-services.net/browse/VCS-15527)  
   **Repo:** DHC-Documentation , DHC-Update

### Security (**Mandatory**): DHC AD security enhancement Q2/2025

   **Work Instruction Link:**  [dhcAdSecurityEnhancement](dhcAdSecurityEnhancement.md)  
   **Design Link:** [lldADSecurityEnhancement2024](../design/lldADSecurityEnhancement2024.md)  
   **Description:**  SSL Medium Strength Cipher Suites Supported (SWEET32) , Insecure cyphers are removed from the local machine registry through a Group Policy Object.  
   **Associated Jira Epic:**  [VCS-15437](https://msdevopsjira.fsc.atos-services.net/browse/VCS-15437)  
   **Repo:** DHC-Documentation , DHC-Manage

### Security (**Mandatory**): Security remediations 2026-PI1

   **Work Instruction Link:**  [wiComplianceOverview](wiComplianceOverview.md)  
   **Design Link:** N/A  
   **Description:**  Generic security improvements from security scanning ,Tosca findings etc.  
   **Associated Jira Epic:**  [VCS-6740](https://msdevopsjira.fsc.atos-services.net/browse/VCS-6740)  
   **Repo:** DHC-Documentation , DHC-Manage , DHC-Update

### Feature (**Engineering Only**): Unified Testing Framework - Design & Build - Phase 1

   **Work Instruction Link:**  [dhcOnboardingUnifiedTestingFramework](dhcOnboardingUnifiedTestingFramework.md)  
   **Design Link:** [lldUnifiedTestingFramework](lldUnifiedTestingFramework.md)  
   **Description:**  Establishes a centralized Pytest and Ansible framework for VCS, replacing fragmented testing with a unified GitHub Actions pipeline that supports secure, remote execution across multiple sites, comprehensive reporting (JUnit/ALM Octane), and scalable self-hosted runners.  
   **Associated Jira Epic:**  [VCS-17074](https://msdevopsjira.fsc.atos-services.net/browse/VCS-17074)  
   **Repo:** DHC-Documentation , DHC-Manage , DHC-Tests

## Conclusion

This document serves as a centralized reference for the VCS-2.2.0.0 release work instructions. The links provided will guide the teams through the necessary steps for deploying, testing, and validating each feature. Ensure that all involved parties have access to these work instructions and follow the procedures as outlined for a successful release.  

## Contact Information

For any questions or clarifications regarding the work instructions or the release process,  
Please contact:  
**Release Manager:** Jijeesh Valappil [Email](mailto:Jijeesh.valapppil@atos.net)  
