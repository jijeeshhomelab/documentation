# Firewall rules for Regional vRA On-Prem

Table of Contents

- [Firewall rules for Regional vRA On-Prem](#firewall-rules-for-regional-vra-on-prem)
  - [Changelog](#changelog)
  - [Introduction](#introduction)
    - [Purpose](#purpose)
    - [Audience](#audience)
    - [Scope](#scope)
  - [Prerequisites](#prerequisites)
  - [Procedure](#procedure)
    - [Master site NSX-T](#master-site-nsx-t)
    - [Physical Firewall rules](#physical-firewall-rules)
    - [Client site NSX-T](#client-site-nsx-t)

## Changelog

|    Date    |   TOS   |   Issue   | Author | Description |
|------------|---------|-----------|--------|-------------|
| 24.01.2023 | VCS 1.7 |           | Bhalchandra Gavhane | Initial document creation |
| 20.03.2023 | VCS 1.7 |           | Bhalchandra Gavhane |Updated AD sync rules and some corrections |

## Introduction

In Regional vRA On-Prem, the master site vRA makes the deployment from Master Site to the Client Site. Thus it is necessary that there should be a connectivity / reachability from Master to Client Site.

This Work Instruction document explains the firewall rules required for Regional vRA On-Prem deployment. In regional vRA on-prem we have to configure the firewall rules manually on below firewalls:

- Master Site NSX-T Distributed Firewall (DFW)
- Client site NSX-T Distributed Firewall (DFW)
- Physical firewalls

### Purpose

Configure NSX firewall rules for regional vRA.

### Audience

- VCS Operations

### Scope

The work instruction is intended to cover below tasks:

1. Master and Client site NSX-T DFW (Distributed Firewall) rules
2. The firewall rules on physical firewalls.

## Prerequisites

Following are the prerequisites to achieve the Regional vRA On-Prem deployment:

- There should be a reachability between the sites.
- Identify the firewalls between Master and Client sites.

## Procedure

Regional vRA On-Prem deployment need the firewall ports to be open at Master site NSX-T DFW, Physical firewalls and Client site NSX-T DFW. It is also require to create the Security Groups of other site, it is also required to create the Security Group ClientEsxx (Multiple ESXi servers) at Master Site.

### Master site NSX-T

Please create below Security Groups and Firewall rules at Master site NSX-T.

**Security Groups**

**Note**: Please replace the Client to *ClientName*

| Description             | Name                     | Members                                                      |
| ----------------------- | ------------------------ | ------------------------------------------------------------ |
| Client LDAP             | {{ customerCode }}seg053 | {{ networkMgmt.cidr }}.24, {{ networkMgmt.cidr }}.25         |
| Client TSS              | {{ customerCode }}seg068 | {{ networkMgmt.cidr }}.22, {{ networkMgmt.cidr }}.23         |
| Client ESXI servers     | ClientEsxx               | {{ networkMgmt.cidr }}.111, {{ networkMgmt.cidr }}.112, {{ networkMgmt.cidr }}.113, {{ networkMgmt.cidr }}.114 |
| Client Infoblox servers | ClientInfx               | {{ networkAvnLocalRegion.cidr }}.61, {{ networkAvnLocalRegion.cidr }}.62, {{ networkAvnLocalRegion.cidr }}.63, {{ networkAvnLocalRegion.cidr }}.64, {{ networkAvnLocalRegion.cidr }}.65, {{ networkAvnLocalRegion.cidr }}.66 |
| Client NSX              | ClientNsxx               | {{ networkMgmt.cidr }}.56, {{ networkMgmt.cidr }}.57, {{ networkMgmt.cidr }}.58, {{ networkMgmt.cidr }}.61 |
| Client Vcenter          | ClientVcsx               | {{ networkMgmt.cidr }}.60                                    |
| Client Ansible          | ClientAnsx               | {{ networkAvnLocalRegion.cidr }}.37, {{ networkMgmt.cidr }}.39 |

**Distributed Firewall rules**

| RuleName       | SectionName | Source                   | Destination              | Service | Action | ApplyTo                          |
| -------------- | ----------- | ------------------------ | ------------------------ | ------- | ------ | -------------------------------- |
| VraToEsxiHosts | VraToClient | {{ customerCode }}seg067 | ClientEsxx               | TCP902  | ALLOW  | {{ customerCode }}seg067_APPLYTO |
|                |             |                          |                          |         |        |                                  |
| VraToVcs       | VraToClient | {{ customerCode }}seg067 | ClientVcsx               | HTTPS   | ALLOW  | {{ customerCode }}seg067_APPLYTO |
|                |             |                          |                          |         |        |                                  |
| VraToNsx       | VraToClient | {{ customerCode }}seg067 | ClientNsxx               | HTTPS   | ALLOW  | {{ customerCode }}seg067_APPLYTO |
|                |             |                          |                          |         |        |                                  |
| ClientTssToVraIdmLcm      | VraToClient | {{ customerCode }}seg068 | {{ customerCode }}seg008 | HTTPS   | ALLOW  | {{ customerCode }}seg067_APPLYTO |
| ClientTssToVraIdmLcm      |             |                          | {{ customerCode }}seg032 |         |        | {{ customerCode }}seg032_APPLYTO |
| ClientTssToVraIdmLcm      |             |                          | {{ customerCode }}seg067 |         |        | {{ customerCode }}seg008_APPLYTO |
|                |             |                          |                          |         |        |                                  |
| IdmToCustAd    | VraToClient | {{ customerCode }}seg032 | {{ customerCode }}seg053 | LDAP    | ALLOW  | {{ customerCode }}seg032_APPLYTO |
|                |             |                          |                          |         |        |                                  |
| VraToClientInfoblox  | VraToClient | {{ customerCode }}seg067 | ClientInfx               | HTTPS   | ALLOW  | {{ customerCode }}seg067_APPLYTO |
|                |             | {{ customerCode }}seg069 |                          |         |        |                                  |
|                |             |                          |                          |         |        |                                  |
| MasterClientAdSync         | VraToClient | {{ customerCode }}seg006 | {{ customerCode }}seg053 | DNS     | ALLOW  | {{ customerCode }}seg006_APPLYTO |
|                |             | {{ customerCode }}seg053 | {{ customerCode }}seg006 | DNS-UDP |        |                                  |
|                |             |                          |                          | LDAP    |        |                                  |
|                |             |                          |                          |         |        |                                  |
| ClientAnsToVra | VraToClient | ClientAnsx               | {{ customerCode }}seg067 | HTTPS   | ALLOW  | {{ customerCode }}seg067_APPLYTO |
|                |             |                          |                          | SSH     |        |                                  |

### Physical Firewall rules

In order to make reachability between Master and Client site, check and identify all available physical firewalls and create below rules on them.

| RuleName             | Service | Action |
| -------------------- | ------- | ------ |
| MasterVraToClientEsxiHosts | TCP902  | ALLOW  |
|                      |         |        |
| MasterVraToClientVcs       | HTTPS   | ALLOW  |
| MasterVraToClientNsx       | HTTPS   | ALLOW  |
| MasterVraToClientInfoblox  | HTTPS   | ALLOW  |
|                      |         |        |
| ClientTssToMasterLcm       | HTTPS   | ALLOW  |
| ClientTssToMasterIdm       | HTTPS   | ALLOW  |
| ClientTssToMasterVra       | HTTPS   | ALLOW  |
|                      |         |        |
| MasterIdmToClientAd        | LDAP    | ALLOW  |
|                      |         |        |
| MasterAdToClientAd         | DNS     | ALLOW  |
| MasterAdToClientAd         | DNS-UDP | ALLOW  |
| MasterAdToClientAd         | LDAP    | ALLOW  |
|                      |         |        |
| ClientAdToMasterAd         | DNS     | ALLOW  |
| ClientAdToMasterAd         | DNS-UDP | ALLOW  |
| ClientAdToMasterAd         | LDAP    | ALLOW  |
|                      |         |        |
| ClientAnsToMasterVra       | HTTPS   | ALLOW  |
| ClientAnsToMasterVra       | SSH     | ALLOW  |

### Client site NSX-T

Please create below Security Groups and Firewall rules at Client site NSX-T.

**Note**: Please replace the Master to *MasterName*

**Security Groups**

| Description     | Name       | Members                                                      |
| --------------- | ---------- | ------------------------------------------------------------ |
| Master Site AD  | MasterAd   | {{ networkMgmt.cidr }}.24, {{ networkMgmt.cidr }}.25         |
| Master Site IDM | MasterIdmx | {{ networkAvnCrossRegion.cidr }}.11, {{ networkAvnCrossRegion.cidr }}.19, {{ networkAvnCrossRegion.cidr }}.24, {{ networkAvnCrossRegion.cidr }}.25, {{ networkAvnCrossRegion.cidr }}.26 |
| Master Site LCM | MasterLcmx | {{ networkAvnCrossRegion.cidr }}.13                          |
| Master Site vRA | MasterVrax | {{ networkAvnCrossRegion.cidr }}.20 ,{{ networkAvnCrossRegion.cidr }}.21, {{ networkAvnCrossRegion.cidr }}.22, {{ networkAvnCrossRegion.cidr }}.23 |

**Distributed Firewall rules**

| RuleName       | SectionName | Source                   | Destination              | Service | Action | ApplyTo                          |
| -------------- | ----------- | ------------------------ | ------------------------ | ------- | ------ | -------------------------------- |
| MasterVraToEsxiHosts | ToVra       | MasterVrax                  | {{ customerCode }}seg035 | TCP902  | ALLOW  | {{ customerCode }}seg035_APPLYTO |
|                |             |                          |                          |         |        |                                  |
| MasterVraToVcs       | ToVra       | MasterVrax                  | {{ customerCode }}seg013 | HTTPS   | ALLOW  | {{ customerCode }}seg013_APPLYTO |
|                |             |                          |                          |         |        |                                  |
| MasterVraToNsx       | ToVra       | MasterVrax                  | {{ customerCode }}seg003 | HTTPS   | ALLOW  | {{ customerCode }}seg003_APPLYTO |
|                |             |                          |                          |         |        |                                  |
| ClientTssToMasterVraIdmLcm | ToVra       | {{ customerCode }}seg004 | MasterIdmx                  | HTTPS   | ALLOW  | {{ customerCode }}seg004_APPLYTO |
| ClientTssToMasterVraIdmLcm |             |                          | MasterLcmx                  |         |        |                                  |
| ClientTssToMasterVraIdmLcm |             |                          | MasterVrax                  |         |        |                                  |
|                |             |                          |                          |         |        |                                  |
| MasterIdmToClientAd        | ToVra       | MasterIdmx                  | {{ customerCode }}seg006 | LDAP    | ALLOW  | {{ customerCode }}seg006_APPLYTO |
|                |             |                          |                          |         |        |                                  |
|                |             |                          |                          |         |        |                                  |
| MasterVraToInfoblox  | ToVra       | MasterVrax                  | {{ customerCode }}seg042 | HTTPS   | ALLOW  | {{ customerCode }}seg067_APPLYTO |
|                |             |                          |                          |         |        | {{ customerCode }}seg042_APPLYTO |
|                |             |                          |                          |         |        |                                  |
| MasterClientAdSync         | ToVra       | MasterAd                    | {{ customerCode }}seg006 | DNS     | ALLOW  | {{ customerCode }}seg006_APPLYTO |
|                |             | {{ customerCode }}seg006                         | MasterAd                         | DNS-UDP |        |                                  |
|                |             |                          |                          | LDAP    |        |                                  |
|                |             |                          |                          |         |        |                                  |
| ClientAnsToMasterVra       | ToVra       | {{ customerCode }}seg007 | MasterVrax                  | HTTPS   | ALLOW  | {{ customerCode }}seg007_APPLYTO |
|                |             |                          |                          | SSH     |        |                                  |
