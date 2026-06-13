# Table of Contents  

- [Table of Contents](../workInstructions/wiLifeCycleManagement-VCS2.1.md#table-of-contents)  
- [Title](../workInstructions/wiLifeCycleManagement-VCS2.1.md#title-wifeaturereleasedhc-2100)  
- [List of Changes](../workInstructions/wiLifeCycleManagement-VCS2.1.md#list-of-changes)  
- [Introduction](../workInstructions/wiLifeCycleManagement-VCS2.1.md#introduction)  
- [Scope](../workInstructions/wiLifeCycleManagement-VCS2.1.md#scope)  
- [LCM code update](../workInstructions/wiLifeCycleManagement-VCS2.1.md#lcm-code-update)  
- [New Code Update Process](../workInstructions/wiLifeCycleManagement-VCS2.1.md#new-code-update-process)  
- [Work Instructions for Features in Scope](../workInstructions/wiLifeCycleManagement-VCS2.1.md#work-instructions-for-features-in-scope)  
- [Feature: Improve ESXi / vCenter patching process DHC 2.0](../workInstructions/wiLifeCycleManagement-VCS2.1.md#featurebase-improve-esxi--vcenter-patching-process-dhc-20)  
- [Feature: Automated Service Integration - Standard Reporting](../workInstructions/wiLifeCycleManagement-VCS2.1.md#featurebase-automated-service-integration---standard-reporting)  
- [Feature: VCS Chargeback for NSX-T Objects](../workInstructions/wiLifeCycleManagement-VCS2.1.md#featurebase-vcs-chargeback-for-nsx-t-objects)  
- [Feature: Retest and adjust ARIA Automation Remote Console action](../workInstructions/wiLifeCycleManagement-VCS2.1.md#featurebase-retest-and-adjust-aria-automation-remote-console-action)  
- [Feature: Include service account changes in LCM release](../workInstructions/wiLifeCycleManagement-VCS2.1.md#featurebase-include-service-account-changes-in-lcm-release)  
- [Feature: DHC AD security enhancement Q4/2024 & Q1/2025](../workInstructions/wiLifeCycleManagement-VCS2.1.md#featuremandatory-dhc-ad-security-enhancement-q42024--q12025)  
- [Feature: SOXDB integration](../workInstructions/wiLifeCycleManagement-VCS2.1.md#featuremandatory-soxdb-integration)  
- [Feature: Deliver DHC management K8s platform](../workInstructions/wiLifeCycleManagement-VCS2.1.md#featureoptional-deliver-dhc-management-k8s-platform)  
- [Feature: vCF 5/vSphere 8 adjustments for vSphere with Tanzu / TGK](../workInstructions/wiLifeCycleManagement-VCS2.1.md#featurebase-vcf-5vsphere-8-adjustments-for-vsphere-with-tanzu--tgk)  
- [Feature: Integrate C&S OS management service](../workInstructions/wiLifeCycleManagement-VCS2.1.md#featureoptional-integrate-cs-os-management-service)  
- [Feature: Web Application Firewall & Advanced Load Balancer](../workInstructions/wiLifeCycleManagement-VCS2.1.md#featureoptional-web-application-firewall--advanced-load-balancer)  
- [Feature: IaaS+ virtual web services](../workInstructions/wiLifeCycleManagement-VCS2.1.md#featureoptional-iaas-virtual-web-services)  
- [Feature: SDN NSX-T SSR: Manage NSX-T Tags for VM](../workInstructions/wiLifeCycleManagement-VCS2.1.md#featureoptional-sdn-nsx-t-ssr-manage-nsx-t-tags-for-vm)  
- [Feature: NSX-T SSR: Manage Loadbalancers](../workInstructions/wiLifeCycleManagement-VCS2.1.md#featureoptional-nsx-t-ssr-manage-loadbalancers)  
- [Feature: NSX-T SSR: Manage Loadbalancers Multi Tenancy Support](../workInstructions/wiLifeCycleManagement-DHC1.9.0.md#featureoptional-distributed-firewall-security-group-reporting)  
- [Feature: NSX Intelligence - VM traffic Analysis and visualization](../workInstructions/wiLifeCycleManagement-VCS2.1.md#featureoptional-nsx-intelligence---vm-traffic-analysis-and-visualization)  
- [Feature: End user provided post deployment script for ARIA Deploy VM Catalog item](../workInstructions/wiLifeCycleManagement-VCS2.1.md#featureoptional-end-user-provided-post-deployment-script-for-aria-deploy-vm-catalog-item)  
- [Feature: Enhance VCS customer consumption Portals](../workInstructions/wiLifeCycleManagement-VCS2.1.md#featureoptional-enhance-vcs-customer-consumption-portals)  
- [Conclusion](../workInstructions/wiLifeCycleManagement-DHC1.9.0.md#conclusion)  
- [Contact Information](../workInstructions/wiLifeCycleManagement-DHC1.9.0.md#contact-information)

## Title: wiFeatureReleaseDHC-2.1.0.0  

## List of Changes  

| Date       | Issue    | Author          |  TOS    | Description |  
| ---------- | -------- | --------------- | ------  | ---------------------- |  
| 20/10/2025 |          | Jijeesh Valappil|  2.1.0.0 | Consolidated WI for 2.1.0.0 release scope  |  

## Introduction  

This document consolidates the work instruction links for each feature within the scope of the DHC-2.1.0.0 release.  
It serves as a single source of reference for teams involved in the release process to ensure smooth deployment and integration of each feature.  

## Scope

The scope of this document covers the work instructions for all features in the DHC-2.1.0.0 release. These features are listed below with links to their respective detailed work instructions.

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
git checkout DHC-2.1-20250617 ## Kindly check the latest release date as tag
git submodule update --init update
cd update  ## now we can see the all playbooks under the update branch
```  

## Work Instructions for Features in Scope  

### Feature(**BASE**): Improve ESXi / vCenter patching process DHC 2.0

   **Work Instruction Link:**  [dhcAsyncPatchTool](../workInstructions/dhcAsyncPatchTool.md)  
   **Description:**  This feature introduces automation for installing asynchronous patches on vCenter and ESXi components within the VMware Cloud Foundation (VCF) environment managed by VCS 2.0. It streamlines the patching lifecycle by reducing manual steps and enhancing reporting and upgrade readiness.  
   **Associated Jira Epic:**  [VCS-15010](https://msdevopsjira.fsc.atos-services.net/browse/VCS-15010)  
   **CodeRepo:** DHC-Manage , DHC-Collections

### Feature(**BASE**): Automated Service Integration - Standard Reporting

   **Work Instruction Link:**  [wiStandardReporting](../workInstructions/wiStandardReporting.md)  
   **Description:**  This feature introduces Standard Reporting under the Automated Service Integration initiative. This feature centralizes performance and capacity reports from various sources into a unified, secure reporting interface powered by Aria Operations (vROps). It enhances visibility, simplifies access, and improves report security across the VCS environment.  
   **Associated Jira Epic:**  [VCS-73](https://msdevopsjira.fsc.atos-services.net/browse/VCS-73)  
   **CodeRepo:** DHC-Manage , DHC-Firewall , DHC-Version-matrix

### Feature(**BASE**): VCS Chargeback for NSX-T Objects

   **Work Instruction Link:**  [wiConfigureBilling](../workInstructions/wiConfigureBilling.md)  
   **Description:**  The VCS Chargeback for NSX-T Objects feature introduces the ability to perform chargeback and cost allocation for NSX-T networking objects in both single-tenant and multi-tenant environments. This enhancement empowers tenants and administrators with greater visibility and control over network-related resource usage and associated costs.  
   **Associated Jira Epic:**  [VCS-12396](https://msdevopsjira.fsc.atos-services.net/browse/VCS-12396)  
   **CodeRepo:** DHC-Manage

### Feature(**BASE**): Retest and adjust ARIA Automation Remote Console action

   **Work Instruction Link:**  [wiRemoteConsoleAccess](../workInstructions/wiRemoteConsoleAccess.md)  
   **Description:**  This release introduces an enhanced capability for accessing the remote console of virtual machines (VMs) directly from the Aria Automation portal, eliminating the need for users to access the underlying vCenter or ESXi host.  
   **Associated Jira Epic:**  [VCS-14892](https://msdevopsjira.fsc.atos-services.net/browse/VCS-14892)  
   **CodeRepo:** DHC-Manage

### Feature(**BASE**): Include service account changes in LCM release

   **Work Instruction Link:**  [wiEnableNewServiceAccounts](../workInstructions/wiEnableNewServiceAccounts-VCS2.1.md)  
   **Description:** This release introduces automated configuration and enablement of dedicated service accounts within the Lifecycle Manager (LCM) workflow to support improved integration stability and security for Aria Operations and NSX environments.  
   **Associated Jira Epic:**  [VCS-16100](https://msdevopsjira.fsc.atos-services.net/browse/VCS-16100)  
   **CodeRepo:** DHC-Manage , DHC-Update , DHC-Collections

### Feature(**Mandatory**): DHC AD security enhancement Q4/2024 & Q1/2025

   **Work Instruction Link:**  [dhcAdSecurityEnhancement.md](../workInstructions/dhcAdSecurityEnhancement.md)  
   **Description:**  This release delivers a series of Active Directory (AD) security improvements across all VCS environments, implemented during Q4 2024 and Q1 2025. The enhancements aim to correct object ownership, enforce privileged identity isolation, reduce excessive permissions, and improve overall AD hygiene and access control.  
   **Associated Jira Epic:** [VCS-14389](https://msdevopsjira.fsc.atos-services.net/browse/VCS-14389) & [VCS-14940](https://msdevopsjira.fsc.atos-services.net/browse/VCS-14940)  
   **CodeRepo(Ansible):** DHC-Manage

### Feature(**Mandatory**): SOXDB integration  

   **Work Instruction Link:**  [VCS OnboardSOXDB](../workInstructions/dhcOnboardSOXDB.md) & [VCSQuarterlyAccesReviewSoxDB](../workInstructions/dhcQuarterlyAccesReviewSoxDB.md) & [wiSoxDBIntegration](../workInstructions/wiSoxDBIntegration.md)  
   **Description:**  This new feature introduces automated SOXDB integration for enhanced compliance reporting within DHC environments. It enables scheduled and on-demand scanning of user accounts across critical systems, generates compliance reports, and securely uploads them to designated SOXDB endpoints.
   **Associated Jira Epic:** [VCS-12677](https://msdevopsjira.fsc.atos-services.net/browse/VCS-12677)  
   **CodeRepo:** DHC-Manage

### Feature(**Optional**): Deliver DHC management K8s platform

   **Work Instruction Link:**  [wiVsphereTanzuCmpBuildGuide](../workInstructions/wiVsphereTanzuCmpBuildGuide.md)  
   **Description:**  This release introduces automation to deploy and configure Tanzu Kubernetes Grid (TKG) functionality on the management domain of a VMware Cloud Foundation (VCF) environment.  
   **Associated Jira Epic:**  [VCS-12866](https://msdevopsjira.fsc.atos-services.net/browse/VCS-12866)  
   **CodeRepo:** DHC-Manage , DHC-Collections , DHC-Firewall

### Feature(**Optional**): vCF 5/vSphere 8 adjustments for vSphere with Tanzu / TGK

   **Work Instruction Link:**  [wiVsphereTanzuCmpBuildGuide](../workInstructions/wiVsphereTanzuCmpBuildGuide.md)  
   **Description:**  This feature introduces automation support for deploying vSphere with Tanzu (TGK) on VMware Cloud Foundation (VCF) 5.x with vSphere 8.x, enabling streamlined enablement of Kubernetes workloads across workload domains within the VCS platform.  
   **Associated Jira Epic:**  [VCS-12168](https://msdevopsjira.fsc.atos-services.net/browse/VCS-12168)  
   **CodeRepo:** DHC-Manage , DHC-Collections , DHC-Firewall

### Feature(**Optional**): Integrate C&S OS management service

   **Work Instruction Link:**  [wiEnableManagedOs](../workInstructions/wiEnableManagedOs.md)  
   **Description:**  This feature introduces the ability to provision a fully Atos Managed server within the VCS service, delivering a seamless integration of VM deployment and OS management. It leverages standard tooling to automate and unify VM and OS lifecycle management under a single orchestration layer, allowing centralized operational control by one support team.
   **Associated Jira Epic:**  [VCS-5271](https://msdevopsjira.fsc.atos-services.net/browse/VCS-5271)  
   CodeRepo:** DHC-Manage , VRO-Workflow

### Feature(**Optional**): Web Application Firewall & Advanced Load Balancer

   **Work Instruction Link:**  [wiAutomatedDeployAdvancedLoadBalancer](../workInstructions/wiAutomatedDeployAdvancedLoadBalancer.md)  
   **Description:**  The Advanced Load Balancer is the next-generation successor to the Native NSX Load Balancer, currently in phase-out and scheduled for deprecation. This feature rollout introduces the Web Application Firewall (WAF) functionality within the VCS platform as part of the Advanced Load Balancer capabilities.
   **Associated Jira Epic:**  [VCS-10027](https://msdevopsjira.fsc.atos-services.net/browse/VCS-10027)  
   **CodeRepo:** DHC-Manage , DHC-Firewall , DHC-Collections , DHC-Version-matrix

### Feature(**Optional**): IaaS+ virtual web services

   **Work Instruction Link:**  [wiEnableManagedOs](../workInstructions/wiEnableManagedOs.md)  
   **Description:**  This feature introduces the IaaS+ Virtual Web Services feature, enabling automated deployment of Atos-managed IIS (Internet Information Services) web servers on top of the VCS platform. Leveraging TSSA automation, this feature delivers a fully operational IIS-enabled Windows server as part of the virtual machine provisioning process.  
   **Associated Jira Epic:**  [VCS-14934](https://msdevopsjira.fsc.atos-services.net/browse/VCS-14934)  
   **CodeRepo:** DHC-Manage , VRO-Workflow

### Feature(**Optional**): SDN NSX-T SSR: Manage NSX-T Tags for VM

   **Work Instruction Link:**  [wiTenantBuilderVraOnPremMultiTenancy](../workInstructions/wiTenantBuilderVraOnPremMultiTenancy.md)  
   **Description:**  This feature introduces enhanced functionality in the Service Broker portal, allowing users to manage NSX-T tags for virtual machines (VMs) through the NSX-T Self-Service Requests (SSR).  
   **Associated Jira Epic:**  [VCS-14368](https://msdevopsjira.fsc.atos-services.net/browse/VCS-14368)  
   **CodeRepo:** DHC-Manage , VRO-Workflow

### Feature(**Optional**): NSX-T SSR: Manage Loadbalancers

   **Feature Dependency:** [Web Application Firewall & Advanced Load Balancer](../workInstructions/wiLifeCycleManagement-VCS2.1.md#featureoptional-web-application-firewall--advanced-load-balancer)  
   **Work Instruction Link:**  [wiTenantBuilderVraOnPremMultiTenancy](../workInstructions/wiTenantBuilderVraOnPremMultiTenancy.md)  
   **Description:**  This functionality empowers users to create, manage, and delete AVI Load Balancers through a self-service interface, simplifying the deployment and operation of load balancing services.  
   **Associated Jira Epic:**  [VCS-9736](https://msdevopsjira.fsc.atos-services.net/browse/VCS-9736)  
   CodeRepo:** DHC-Manage , VRO-Workflow

### Feature(**Optional**): NSX-T SSR: Manage Loadbalancers Multi Tenancy Support

   **Work Instruction Link:**  [enable-avi-load-balancer-ssrs-in-vra](../workInstructions/wiTenantBuilderVraOnPremMultiTenancy.md#enable-avi-load-balancer-ssrs-in-vra)  
   **Description:**  This feature introduces Multi-Tenancy support for AVI Load Balancer management via NSX-T Self-Service Requests (SSRs) in the Aria Automation Service Broker portal. The feature enables tenants to independently create, manage, and delete AVI Load Balancers within the boundaries of their allocated resources.  
   **Associated Jira Epic:**  [VCS-14861](https://msdevopsjira.fsc.atos-services.net/browse/VCS-14861)  
   **CodeRepo:** DHC-Manage , VRO-Workflow

### Feature(**Optional**): NSX Intelligence - VM traffic Analysis and visualization

   **Feature Dependency:** [Deliver DHC management K8s platform](../workInstructions/wiLifeCycleManagement-VCS2.1.md#featureoptional-deliver-dhc-management-k8s-platform)  
   **Work Instruction Link:**  [wiNsxAppliancePlatform](../workInstructions/wiNsxAppliancePlatform.md) & [wiReverseProxyforNSXTIntelligence](../workInstructions/wiReverseProxyforNSXTIntelligence.md) & [wiDeployNsxNapp](../workInstructions/wiDeployNsxNapp.md)  
   **Description:**  This feature introduces VM Traffic Analysis and Visualization capabilities through NSX Intelligence, delivering deeper visibility into network traffic flows within the virtualized infrastructure. This feature helps customers analyze VM-to-VM communications, understand application dependencies, and optimize security policies with data-driven insights.  
   **Associated Jira Epic:**  [VCS-10029](https://msdevopsjira.fsc.atos-services.net/browse/VCS-10029)  
   **CodeRepo:** NA (Only Manual deployement in this release)

### Feature(**Optional**): End user provided post deployment script for ARIA Deploy VM Catalog item

   **Work Instruction Link:**  [wiTenantBuilderVraOnPremMultiTenancy](../workInstructions/wiTenantBuilderVraOnPremMultiTenancy.md)  
   **Description:**  This feature introduces support for end-user provided post-deployment scripts in the VM Catalog Item within Aria Deploy. It allows users to inject and execute custom scripts during the provisioning process, enabling VM-level customization immediately after deployment.  
   **Associated Jira Epic:**  [VCS-15157](https://msdevopsjira.fsc.atos-services.net/browse/VCS-15157)  
   **CodeRepo:** DHC-Manage

### Feature(**Optional**): Enhance VCS customer consumption Portals

   **Work Instruction Link:**  [wiTenantBuilderVraOnPremMultiTenancy](../workInstructions/wiTenantBuilderVraOnPremMultiTenancy.md)  
   **Description:**  This release introduces a set of enhancements to the VCS Customer Consumption Portal within Service Broker, aimed at improving the overall user experience, efficiency, and visibility into provisioned resources. These updates provide more control, transparency, and guidance for customers managing their virtual infrastructure.  
   **Associated Jira Epic:**  [VCS-15330](https://msdevopsjira.fsc.atos-services.net/browse/VCS-15330)  
   **CodeRepo:** DHC-Manage , VRO-Workflow

## Conclusion  

  This document serves as a centralized reference for the VCS-2.1.0.0 release work instructions. The links provided will guide the teams through the necessary steps for deploying, testing, and validating each feature. Ensure that all involved parties have access to these work instructions and follow the procedures as outlined for a successful release.  

## Contact Information  

For any questions or clarifications regarding the work instructions or the release process,  
Please contact:  
**Release Manager:** Jijeesh Valappil [Email](mailto:Jijeesh.valapppil@atos.net)  
