# Onboarding Tasks

## Table of contents

- [Onboarding Tasks](#onboarding-tasks)
  - [Table of contents](#table-of-contents)
  - [Changelog](#changelog)
  - [1 Introduction](#1-introduction)
    - [1.1 Purpose](#11-purpose)
    - [1.2 Audience](#12-audience)
    - [1.3 Scope](#13-scope)
    - [1.4 Related Documents](#14-related-documents)
  - [2 Onboarding Tasks](#2-onboarding-tasks)
    - [2.1 Onboard SNOW](#21-onboard-snow)
    - [2.2 CMP (optional)](#22-cmp-optional)
    - [2.3 vRA](#23-vra)
    - [2.4 Setup AntiVirus](#24-setup-antivirus)
    - [2.5 CEB](#25-ceb)
    - [2.6 HTTP Gateway](#26-http-gateway)

## Changelog

| Version | Date       |   TOS   |   Issue   | Description              | Author(s)       |
| ------- | ---------- |  ------- | --------- |  ------------------------ | --------------- |
| 0.1     | 2020-05-13 |          |           |   Initial draft creation   | Brian Gerrard |
| 0.2     | 2021-01-19 |           |          |  Updating Onboard SNOW chapter   | Marcin Kujawski |
| 0.4     | 2021-03-19 |           |           | Updating related documents chapter to add link into VCS runbook(DHC-1373)   | Tomasz Korniluk |
| 0.5     | 2021-05-28 |           |           |  Removing onboard mgmt CI chapter and updating vRA and CMP chapter for TOS 1.3 (DHC-2134) | Marcin Kujawski |
| 0.6     | 2021-07-13 |           |           |  Adding dhcOnboardingAntiVirus work instruction (DHC-2275) | Marcin Kujawski |
| 0.7     | 2024-03-06 |           |           | Updated Document according to VCS standard  | Kanchan Pardeshi |

## 1 Introduction

This instruction shows at a high level, the steps involved to onboard VCS. The document contains references to other procedures and VCS does not hold ownership over other areas of Atos, whose processes may change. VCS deployment is mostly an automated process. More information on step by step build of the management cluster can be found within the [VCS Build Guide](dhcBuildGuide.md)

### 1.1 Purpose

Onboard new customers in VCS.

### 1.2 Audience

- VCS Engineers
- VCS Architects

### 1.3 Scope

- Description of onboarding VCS
- Description of any inputs required for overall steps
- Details of who is responsible for each step

### 1.4 Related Documents

This document is a subset of Atos Technology Lifecycle Management (ATLM) artefacts. All documents are stored in the VCS documentation repository. Documents referenced in this document can be found in the Global documentation location

| VCS runbook location |
|:---:|
| [VCS runbook location](https://sp2013.myatos.net/ms/gd/cloud/eso/pp/dpc/dpc_dev/DPC%20Main/00.%20DHC%20(was%20DPC.Next)/Service%20Management/MSW-S74-0002%20DHC%20Customer%20Runbook%20Setup.xlsx) |

## 2 Onboarding Tasks

### 2.1 Onboard SNOW

SNOW onboarding is performed by SNOW Architects. The following are required to be created and made available to the VCS integration Architect

- Functional Organization
- Groups
- Categories
- Tasks Data Lookups (for incident routing)
- Event Sender
- Integration User (for monitoring)
- SLA Management
- Event Management

### 2.2 CMP (optional)

CMP Architects will be responsible for supplying the following to CMP engineers

- Backup Policy Definitions
- Corresponding Tags. Key/values are required for import to relevant tables

VCS Architects require to get the following from CMP

- SNOW credentials (Required for MID Server creation)

### 2.3 vRA

A new vRA Cloud Organization is required. This can only be done by creating new VMware organization within Cloud Partner Navigator. Integration Architect should request this from the VMware Technical Account Manager or contact person who has proper rights to create this. The Integration Architect should provide dedicated for VCS build VMware account (atos.net email address). Once the Organization has been created and the account has to be associated with a newly created organization.

The whole process is described in a detail in separate work instruction.

Follow the process with document: [VCS Enable Multitenancy](dhcEnableMultitenancy.md)

### 2.4 Setup AntiVirus

Integration Architects must gather the following inputs to be executed in the Build Automation. The information should be obtained from the BDS team after the onboarding into BDS service will take place. Below data is required and should be provided by BDS team once onboarding is done:

- Deep Security Tenant ID
- Deep Security Token
- Deep Security Linux Policy ID
- Deep Security Linux Policy ID
- Deep Security Windows Policy ID

Follow the process with document: [VCS Onboarding Antivirus](dhcOnboardingAntivirus.md)

### 2.5 CEB

Integration Architects must gather the following inputs to be executed in the Build Automation:

- Number of Avamar Proxy Agents to be installed. Every 50 VMs will require a proxy
- CEB Architect should provide Backup Avamar Server FQDN
- CEB Architect should provide an IP address for the Avamar Server
- CEB Architect should provide Data Domain Appliance FQDN
- CEB Architect should provide IP address for Data Domain Appliance
- Understand if Proxy Agents will be deployed on Customer Workload Domain as well as Management Domain

Follow the process with the CEB VCS OLA 4.1 document.

### 2.6 HTTP Gateway

An HTTP Gateway server is used to consume and pass on information between VCS and ServiceNow platforms.
