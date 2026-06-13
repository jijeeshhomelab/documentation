# L1 Monitoring Guide

## Table of contents

- [L1 Monitoring Guide](#l1-monitoring-guide)
  - [Table of contents](#table-of-contents)
  - [Changelog](#changelog)
  - [Introduction](#introduction)
    - [Purpose](#purpose)
    - [Audience](#audience)
    - [Scope](#scope)
  - [Related Documents](#related-documents)
  - [Infrastructure Requirements](#infrastructure-requirements)
  - [Assumptions](#assumptions)
  - [VCS DevSecOps ServiceNOW queue](#vcs-devsecops-servicenow-queue)
  - [Three strike rule](#three-strike-rule)
  - [VCS DevSecOps Standby Engineer](#vcs-devsecops-standby-engineer)
  - [VCS DevSecOps Escalation Matrix](#vcs-devsecops-escalation-matrix)

## Changelog

| Date       | Author           | Issue    | Description     |
| ---------- | ---------------- | -------- | --------------- |
| 17.08.2021 | Shyjin Varaprath | VCS 2560 | Initial Version |
| 31.08.2021 | Shyjin Varaprath | VCS 2560 | Final Version   |
| 24.01.2022 | Pawel Osial      | VCS 4015 | Updates based on Patrycja's input   |
| 02.08.2022 | Pawel Osial      | CESDHC-599 | Updates based on Bridge remarks and outdate data   |
| 31.07.2023 | Radoslaw Dabrowski | VCS-10399 | Update alert list to new format and links in the documentation |
| 12.12.2023 | Vani Tatipamula | VCS-10309 | improved indentation and indexation |

## Introduction

### Purpose

Provide detailed instructions for the L1 Bridge/Monitoring team around the types of alerts & tickets that they should engage the standby support engineer during off-business hours & holidays (including weekends). Describe the process of triggering 2nd line standby in case of MI, P1/P2 tickets outside of monitoring.

### Audience

- VCS L1 Bridge team
- VCS Monitoring team

### Scope

There are "times" or "hours" in a day referred to as "Off-Business hours" (including Weekends and Public Holidays) when the core support team (DevSecOps) engineers would not be "actively" monitoring the VCS platform alerts/tickets. This work instruction dictates the type of alerts/tickets & times when the L1 team should engage the Standby Engineer from the DevSecOps team.

1. Before and after the actual office/business hours (Before 8AM CEST/CET and After 6PM CEST/CET).
2. During the Weekends (Throughout Saturday and Sunday).
3. During Public Holidays.
4. Monitoring incidents P2/P3 but they are mentioned to call standby in the alert list.
5. For all manually created
  a.   For Siemens, all P1's and P2's
  b.   For all other customers P1's and P2's which is potential P1's confirmed with Incident Manager
6. For all MI's
7. For all calls received from 3rd Parties only when ticket is opened for the case.
8. Monitoring Tickets which are P3 but they are mentioned to call standby in the alert list.

## Related Documents

This document is a subset of Atos Technology Lifecycle Management (ATLM) artefacts.

| Document                              | Document Name                                                                         |
| ------------------------------------- | ------------------------------------------------------------------------------------- |
| VCS: Monitoring Alerts Classification | [VCS: Monitoring Alerts Classification](files/wiL1MonitoringGuide/dhcMonitoringAlertsClassification.md) |

## Infrastructure Requirements

1. A readily deployed VCS Infrastructure instance

## Assumptions

It is assumed that the engineer following this work instruction has access to required resources to call the Standby engineer and would be monitoring the alerts & tickets for the customer as part of his/her daily work routine.

## VCS DevSecOps ServiceNOW queue

The following queues are to be monitored for the VCS DevSecOps team in SNOW.

- ZZ.Cloud.DHC-DevSecOps

The SNOW console Incidents panel could be filtered with " All>Active = true > Assignment Group = ZZ.Cloud.DHC-DevSecOps ".

## Three strike rule

There should be at least THREE attempts to call the Standby Engineer with intervals of 5 MINUTES between each attempt. If the Standby Engineer is not reachable then the escalation matrix should be followed to invoke the next level support personnel to address the alert/ticket. Do make sure that the Bridge/Monitoring team engineer should log an entry in the SNOW ticket for every attempt made at invoking the Standby Engineer. **_NO Ticket log would imply no attempts made_**.

## VCS DevSecOps Standby Engineer

To involve the standby engineer call the following number: +48 525866487 which redirects the call to the engineer on standby.

## VCS DevSecOps Escalation Matrix

All information can be found under [PowerApps](https://apps.powerapps.com/play/e/default-33440fc6-b7c7-412c-bb73-0e70b0198d5a/a/53646bc8-a2fd-496a-bf50-0341490d82ad?tenantId=33440fc6-b7c7-412c-bb73-0e70b0198d5a&source=portal) page
