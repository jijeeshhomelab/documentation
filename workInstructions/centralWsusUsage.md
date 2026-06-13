# centralWSUSRepositoryUsage

## Changelog

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1     | 10/12/2024 | Adrian Giurgiu | Initial version |

## Introduction

### Design and Workflow of the Internal WSUS Repository System

The internal WSUS (Windows Server Update Services) repository, hosted on WSUS001, is the core system for managing Windows updates, patches, and feature packs. This repository enables efficient synchronization, storage, and distribution of updates from an upstream WSUS server or proxy, ensuring controlled and reliable patch management across the Windows infrastructure.

### Key Components and Architecture

**WSUS Server (WSUS001):**

- Acts as the primary repository for managed hosts, synchronizing updates either directly from Microsoft or through an upstream WSUS server.
- Configured to download and store only approved updates, optimizing storage and bandwidth usage.

**Upstream WSUS Server (Optional):**

- For environments requiring additional control, WSUS001 can use an upstream WSUS server as its source of updates instead of synchronizing directly with Microsoft.
- This feature enhances compliance and policy adherence in complex IT environments.

**Group Policy Integration:**

- Managed Windows hosts are configured through Group Policy Objects (GPOs) to use WSUS001 for updates. This ensures updates are sourced from the internal WSUS repository.

**Automation and Reporting:**

- WSUS001 is integrated with automation tools (e.g., PowerShell scripts) for streamlined approval workflows and patch deployment tracking.
- Regular reports on update statuses, compliance levels, and host health are generated for auditing purposes.

### Workflow Overview

1. **Update Synchronization:**
   - WSUS001 synchronizes updates from either an upstream WSUS server or directly from Microsoft’s update catalog.
   - Administrators can configure synchronization schedules and select specific update categories and products.

2. **Approval Process:**
   - Updates are reviewed and approved manually or automatically based on predefined rules.
   - Approved updates are made available to target hosts via WSUS001.

3. **Host Configuration:**
   - Managed Windows hosts receive configuration through GPOs, specifying WSUS001 as their update source.
   - Hosts periodically contact WSUS001 to check for and download approved updates.

4. **Patch Deployment:**
   - Updates are installed on target hosts based on their local configuration or deployment schedules.
   - Automation tools can be used to monitor and enforce compliance.

5. **Reporting and Maintenance:**
   - Regular reports provide visibility into patch status and compliance.
   - Maintenance tasks, such as cleanup of old updates and database optimization, ensure repository performance.

### Benefits of the Design

- **Reliability:** Centralized update management mitigates dependency on external internet connectivity for patching.
- **Control:** Administrators can review and approve updates before deployment, reducing the risk of disruptions caused by untested updates.
- **Scalability:** The system is designed to support a growing number of managed Windows hosts without performance degradation.
- **Compliance:** Detailed reporting ensures alignment with regulatory and organizational patch management policies.

## Purpose

Create and manage an internal WSUS repository that supports direct or upstream synchronization, providing a scalable and controlled solution for patch management.
Basically the scripts were adjusted so we can integrate an upstream WSUS ( in case in the future for example we will have a central management stack or simply the customer provides their own WSUS which we can make use of it as an upstream WSUS for our VCS product. )
All of the automation and reporting are also covered by the Windows patching and their afferent documentation and processes.

## Audience

- VCS Engineering
- VCS Operations

## Playbook usage

If we have a central WSUS already configured then proceed the deployment with the extra parameters shown below. This approach will create a variable in groupvars ( if empty, will go with the default configuration, if not it will go with custom configuration )

```bash
ansible-playbook createWsusHost.yml -e "updateSource=IP or FQDN"
```

If we go with the default WSUS design ( as the previous VCS versions ) then proceed with the default run.

```bash
ansible-playbook createWsusHost.yml
```

### Working Directory

All the playbooks must be executed from the `/opt/dhc/deploy` directory.

To navigate there execute:

```bash
cd /opt/dhc/deploy
```

To check which directory you are in execute:

```bash
pwd
```

## Conclusion

The WSUS repository, with its support for upstream synchronization, provides a robust and scalable solution for managing Windows updates. By centralizing update distribution and leveraging automation, it ensures reliable and efficient patch management within the VCS environment.
