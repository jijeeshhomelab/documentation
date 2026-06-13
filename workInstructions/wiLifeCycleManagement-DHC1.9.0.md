# Table of Contents  

- [Table of Contents](../workInstructions/wiLifeCycleManagement-DHC1.9.0.md#table-of-contents)  
- [Title](../workInstructions/wiLifeCycleManagement-DHC1.9.0.md#title)  
- [List of Changes](../workInstructions/wiLifeCycleManagement-DHC1.9.0.md#list-of-changes)  
- [Introduction](../workInstructions/wiLifeCycleManagement-DHC1.9.0.md#introduction)  
- [Scope](../workInstructions/wiLifeCycleManagement-DHC1.9.0.md#scope)  
- [LCM code update](../workInstructions/wiLifeCycleManagement-DHC1.9.0.md#lcm-code-update)  
- [New Code Update Process](../workInstructions/wiLifeCycleManagement-DHC1.9.0.md#new-code-update-process)  
- [Work Instructions for Features in Scope](../workInstructions/wiLifeCycleManagement-DHC1.9.0.md#work-instructions-for-features-in-scope)  
- [Feature: NSX-T SSR: Manage Security Groups & Manage Firewall rules](../workInstructions/wiLifeCycleManagement-DHC1.9.0.md#featureoptional-nsx-t-ssr-manage-security-groups--manage-firewall-rules)  
- [Feature: AD security enhancement(Q1) and RBAC split for domain admins](../workInstructions/wiLifeCycleManagement-DHC1.9.0.md#featuremandatory-ad-security-enhancementq1-and-rbac-split-for-domain-admins)  
- [Feature: NSX Edge Firewall TLS packet inspection](../workInstructions/wiLifeCycleManagement-DHC1.9.0.md#featureoptional-nsx-edge-firewall-tls-packet-inspection)  
- [Feature: User based firewalling / Identity Firewall (IDFW)](../workInstructions/wiLifeCycleManagement-DHC1.9.0.md#featureoptional-user-based-firewalling--identity-firewall-idfw)  
- [Feature: Distributed Firewall Security Group reporting](../workInstructions/wiLifeCycleManagement-DHC1.9.0.md#featureoptional-distributed-firewall-security-group-reporting)  
- [Conclusion](../workInstructions/wiLifeCycleManagement-DHC1.9.0.md#conclusion)
- [Contact Information](../workInstructions/wiLifeCycleManagement-DHC1.9.0.md#contact-information)

## Title: wiFeatureReleaseDHC-1.9.0  

## List of Changes  

| Date       | Issue    | Author          |  TOS   | Description |  
| ---------- | -------- | --------------- | ------ | ---------------------- |  
| 29/01/2025 |          | Jijeesh Valappil|  1.9.0 | Consolidated WI for 1.9.0 release scope  |  

## Introduction  

This document consolidates the work instruction links for each feature within the scope of the DHC-1.9.0 release.  
It serves as a single source of reference for teams involved in the release process to ensure smooth deployment and integration of each feature.  

## Scope

The scope of this document covers the work instructions for all features in the DHC-1.9.0 release. These features are listed below with links to their respective detailed work instructions.

### LCM code update  

Please check if new/updated playbook versions are available. See the `manageDhcRepository.yml` playbook for more information.  

#### New Code Update Process  

---
Note: During TOS manually change the branch to DHC-1.9 in `opt/dhc/version-matrix` , `opt/dhc/manage` and `opt/dhc/VRO-Workflows`  
example:  

```bash
/opt/dhc/version-matrix: git checkout DHC-1.9  
/opt/dhc/manage: git checkout DHC-1.9  
```

DHC 1.6 introduced a new way of updating the local git repository on the ansible server, that skips the git001 VM/local gitlab.  

To upgrade the code execute the playbook on *ans001* server from */opt/dhc/manage/* directory:  

```bash
ansible-playbook manageDhcRepository.yml  
```

The `manageDhcRepository.yml` playbook is available from version `DHC-1.5-latest` and later.  

Familiarize yourself with the playbook description and arrange pre-requisites:  

- Internet connection (at least to github.com) is required.  
- Account on *github.com* with at least a read-only access to the DHC repositories is required.  
- A GitHub access token with at least read privileges is required.  

The playbook will prompt the user to input a release tag to upgrade the code to. The tags can be found at <https://github.com/GLB-CES-PrivateCloud/DHC/tags>. For a given DHC version, i.e. DHC 1.9.0, the latest available tag for that version should be chosen.  
Example, the available tags are `DHC-1.9.0-20240201` and `DHC-1.9.0-20250301`. The last part is a release date in YYYYMMDD format, therefore the later one should be preferred.  

>Note, **the first run will fail by design**, as the playbook backs up the existing code as a first step. **You will be prompted to execute this playbook from a backup location.**  
>
>By following the prompts you should end up with code updated to the desired release.  

New code upgrade process updates the version Matrix file which is stored in *`/opt/dhc/version-matrix/versionMatrix.json`*. This is default location for both *manage* and *update* playbooks.  

>Note, the old version Matrix json files located in *manage/group_vars/* and *update/group_vars/* folders become depreciated, not used and might be removed manually.  

## Work Instructions for Features in Scope  

### Feature(**Optional**): NSX-T SSR: Manage Security Groups & Manage Firewall rules  

   **Work Instruction Link:**  [NSX-T SSR](../workInstructions/wiTenantBuilderVraOnPremMultiTenancy.md#enable-nsx---t-ssrs-in-vra)  
   **Description:**  This feature allows customers to manage firewall security groups and rules through the Service Broker portal using SSRs . By enabling NSX-T SSRs, customers gain full control over SDN microsegmentation, managing Day 1 and Day 2 operations seamlessly via Aria Automation. This unified interface eliminates the need to access the NSX-T portal, providing a streamlined and efficient experience for managing security settings and microsegmentation.  
   **Associated Jira Epic:** [VCS-9733](https://msdevopsjira.fsc.atos-services.net/browse/VCS-9733) & [VCS-9732](https://msdevopsjira.fsc.atos-services.net/browse/VCS-9732)  
   **CodeRepo(Ansible):** DHC-Manage  
   **CodeRepo(vRO Workflow):** VRO-Workflows  

### Feature(**Mandatory**): AD security enhancement(Q1) and RBAC split for domain admins  

   **Work Instruction Link:**  [AD Security Enhancement(Q1)](../workInstructions/dhcAdSecurityEnhancement.md)  
   **Description:**  This feature addresses security vulnerabilities identified in Active Directory within the Paris 2024 DHC environment. It includes fixes to strengthen AD security and implements a Role-Based Access Control (RBAC) split for domain admins, ensuring more granular control over administrative access and enhancing overall security management.  
   **Associated Jira Epic:** [VCS-11769](https://msdevopsjira.fsc.atos-services.net/browse/VCS-11769) & [VCS-11882](https://msdevopsjira.fsc.atos-services.net/browse/VCS-11882)  
   **CodeRepo:** DHC-Manage  

### Feature(**Optional**): NSX Edge Firewall TLS packet inspection  

   **Work Instruction Link:**  [TLS Packet Inpection](../workInstructions/wiTlsInspection.md)  
   **Description:**  This feature enables TLS packet inspection on the NSX-T Edge firewall, allowing for deeper visibility and analysis of encrypted traffic. By inspecting TLS traffic, it helps identify potential security threats within encrypted sessions, ensuring better protection against hidden vulnerabilities and attacks while maintaining the integrity of secure communications.  
   **Associated Jira Epic:**  [VCS-10972](https://msdevopsjira.fsc.atos-services.net/browse/VCS-10972)  

### Feature(**Optional**): User based firewalling / Identity Firewall (IDFW)  

   **Work Instruction Link:**  [Identity Firewall (IDFW)](../workInstructions/wiNsxtIdentityFirewall.md)  
   **Description:**  This feature enables firewall rules to be dynamically applied based on user identity, rather than just IP addresses. By integrating with directory services like Active Directory, Identity Firewall (IDFW) allows for more granular control, ensuring that security policies are enforced according to user roles and groups. This enhances security by providing context-aware protection, tailored to the specific needs of individual users or teams.  
   **Associated Jira Epic:**  [VCS-9819](https://msdevopsjira.fsc.atos-services.net/browse/VCS-9819)  

### Feature(**Optional**): Distributed Firewall Security Group reporting  

   **Work Instruction Link:**  [Distributed Firewall Security Group reporting](../workInstructions/wiDistributedFirewallReport.md)  
   **Description:**  This feature enhances security visibility and management within the environment by implementing detailed reporting for Distributed Firewall Security Groups. It provides comprehensive insights into the security group configurations, policies, and their associated traffic flows, allowing for better monitoring and analysis of the network security posture. The feature ensures improved security auditing and helps identify potential vulnerabilities within the security group configurations, enhancing the overall firewall security management.  
   **Associated Jira Epic:**  [VCS-13752](https://msdevopsjira.fsc.atos-services.net/browse/VCS-13752)  
   **CodeRepo:** DHC-Manage  

## Conclusion  

  This document serves as a centralized reference for the DHC-1.9 release work instructions. The links provided will guide the teams through the necessary steps for deploying, testing, and validating each feature. Ensure that all involved parties have access to these work instructions and follow the procedures as outlined for a successful release.  

## Contact Information  

For any questions or clarifications regarding the work instructions or the release process,  
Please contact:  
**Release Manager:** Jijeesh Valappil [Email](mailto:Jijeesh.valapppil@atos.net)  
