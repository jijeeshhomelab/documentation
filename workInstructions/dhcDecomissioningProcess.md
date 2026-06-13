# Decommissioning Process

# Changelog

| Version | Date       | Description                                          | Author               |
| ------- | ---------- | ---------------------------------------------------- | -------------------- |
| 0.1     | 29/04/2020 | First version                                        | Alec Dunn            |

## Introduction

This instruction describes the overall process used to decommission a VCS. It focuses on the elements to consider and the parties that need to be engaged with.  
It is **not** a step by step guide on how to decommission a VCS as this will be subtly different for each customer configuration.  This is not a document with a single author, This page combines input from Process architecture, ITSM and Engineering to one place.

### Purpose

Describe the generic process of VCS decommissioning.

### Audience

This page is intended for account teams and operations personnel that are tasked with decommissioning a VCS environment for a customer.

### Scope

This page covers the high level areas that need to be decommissioned. As every customer is different it does not contain the order of steps.  It does not contain step-by-step user instructions on how to perform each decommissioning element.  Indeed, these steps are likely to have their own documentation owned by their respective operations departments (e.g. SNOW decommissioning UIG from SNOW team, CEB from CEB team etc.).

# Decommissioning Time

Complete decommissioning of VCS can take 5-6 days considering all passwords, network information, service now intake forms etc are available at starting of decommissioning process.

This time estimation is given considering there is single local POD with an integrated management domain, if there are multiple pods at different locations then this decommissioning time will increase. Due diligence should be done to ensure decommissioning time is as accurate as possible.

| Component or Task                                                 | Time (h) |
| ----------------------------------------------------------------- | -------- |
| Preparation of Environment to allow for Decommission              |          |
| Securing relevant data (Compliance logs, backups etc)             |          |
| Decommission of SW components                                     |          |
| Decommission integrations (Standard)                              |          |
| Decommission Hardware                                             |          |
| Decommission ITSM integration                                     |          |
| Decommission front end  and Portal (with associated SaaS accounts)|          |

# Software Licence Recovery

VCS has software elements that are billed via differing methods.  These are split out and shown below.

## Perpetual Licences

These are one time cost licences that are paid for up front. They keys from this licence type can be recovered for re-use elsewhere whe a VCS is decommissioned.

| Component      | Count  | Description                                          |
| -------------- | ------ | ---------------------------------------------------- |
| Cloudlink      | 1      | KMS server licence covering MGMT and WL              |
| AlienVault USM | 1      | OPTIONAL Component for CPI Compliance                |

## Paid Monthly Licences

These are monthly cost licences billed on a recurring basis.  The licences for these cannot be recovered but the charge associated with them should be stopped.  This type of licence should also be stopped if there is a partial decommissioning process happening (i.e. 5 hosts removed allows a portion of the software to stop being billed).

| Component               | Count          | Description                                          |
| ----------------------- | -------------- | ---------------------------------------------------- |
| VMware Cloud Foundation | Per CPU Socket | Mandatory for each host in cluster (WL and MGMT)     |
| VMware vCenter Server   | 1 per VCS PoD  | One for each PoD                                     |
| VMware SRM Enterprise   | Packs of 25    | optional for DR protected VMs                        |
| Windows Server          | 16             | Core licencing for MGMT domain Windows Licences     |
| SNOW Licences           | Variable       | (Optional) If SNOW Plugin used for ITSM              |
| CMP 2.0 Licences        | Variable       | (Optional) CMP 2.0 is used For ITSM                  |

## SaaS Licencing

Some VCS elements use a SaaS based licencing model. Typically these are either **on demand** or  **defined commitment** type licences.  They cannot be recovered during a decommission process. rather, they cease to be billed after the next cycle.  Note, in the event of a 3 year committed licence this could involve a cost write off.

| Component           | Count  | Description                                                      |
| ------------------- | ------ | ---------------------------------------------------------------- |
| Trinzic DDI Support | Per VM | Support for Infoblox DDI IPAM WL and MGMT                        |
| CEB Backup          | Per TB | Licencing cost per TB of backup allocated                         |
| VMware vRA-Cloud    | per VM | per VM provisioned to WL cluster via vRA-Cloud (1-3 Year Commit) |

## Support Subscriptions

Some elements of VCS software infrastructure have separate support agreements.  These should be terminated or set to not renew upon decommissioning.

| Component                        | Count  | Description                                          |
| -------------------------------- | ------ | ---------------------------------------------------- |
| Canonical Ubuntu Support         | 4      | Supports MGMT domain Linux Licences                  |
| Canonical Application Advantage  | 4      | Supports MGMT Domain Linux apps                      |
| Pro Support For Cloudlink        | 1      | Support for KMS infra                                |

# Decommissioning Process

The Decommissioning process should never be performed without the relevant approvals and agreements from the account. This is not for fixing installs, this is for the release of a customer from a VCS.

## Secure relevant Data

MGMT and WL logs, passwords, backups

## Removal of Workload from VCS

- Ensure backups of VMs secure
- Migrate VMS off workload domain.
- ensure root PWDs known for hosts
- Validate all config moved and ok.
- Clean up vCenter
- Remove workload domain
- Repeat for other workloads

## Removal of Workload and MGMT VMs from backup

- Removal from CEB or other

## Get Root Passwords

- Ensure all machine and MGMT root passwords are secured
- Extract from Vault

## Power Down and Remove MGMT

- Secure all data required for compliance (export of backup)
- vRLI logs etc
- Power down MGMT VMs
- Clean and decommission vCenter
- Wipe ESXi Configuration

## Decommission Hardware

- Wipe HDD and configuration (Secure)
- Decommission ESXi boot
- Power Down and De cable HW
- Remove from DC

# Release IP back to pools

- Decommission from DDI instance and release to customer pools
- Validate all config removed from vCenter

# Decommission ITSM / Front End Integration

- CIs in CMDB to be set to "disposed" for all CUST and system CIs
- Release / stop CMDB / SNOW licencing
- Make relevant changes in SNOW
  - Is Monitored must be set on “false”
  - Is the CI Supported must be set on “false”
  - Operational Status must be set on “Disposed”
  - Reason CI is not Monitored must be set on “Disposed”
  - Support Status must be changed to “Not Supported”
- Decommission CMP 2.0
  - Deactivate VCS related Service Level configurations for Incident Management and CPRs
  - Delete VCS Category-assignment group lookup relations
  - De activate VCS ServiceNow related categories
  - Unassign VCS related assignment groups
