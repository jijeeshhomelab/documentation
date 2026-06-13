# Software Defined Networks LLD

## Changelog

| Date       | Issue       | Author              | TOS     | Description                                                                                                                                                                      |
|------------|-------------|---------------------|---------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2019-04-02 |             | Radoslaw Dabrowski  |         | Initial draft                                                                                                                                                                    |
| 2019-08-20 |             | Radoslaw Dabrowski  |         | Added information about vROps Edge configuration                                                                                                                                 |
| 2019-08-22 |             | Radoslaw Dabrowski  |         | Converted document into markdown                                                                                                                                                 |
| 2019-09-02 |             | Radoslaw Dabrowski  |         | Added sections about Security and requirements from physical infrastructure                                                                                                      |
| 2019-09-09 |             | Radoslaw Dabrowski  |         | Added information about Physical Firewall ruleset                                                                                                                                |
| 2019-09-13 |             | Radoslaw Dabrowski  |         | Added information about Firewall Rules approach                                                                                                                                  |
| 2019-09-16 |             | Radoslaw Dabrowski  |         | Added information about microsegmentation in Management Domain                                                                                                                   |
| 2019-09-18 |             | Radoslaw Dabrowski  |         | Added information about Load Balancing on Workload Domain                                                                                                                        |
| 2019-09-19 |             | Pawel Zurawski      |         | Added information DR about DR in Management Domain                                                                                                                               |
| 2019-10-31 |             | Pawel Zurawski      |         | Added DC Failure - Stretched Cluster paragraph                                                                                                                                   |
| 2019-11-01 |             | Radoslaw Dabrowski  |         | Removed information about old technologies (no longer in VCS), changed drawing regarding stretched cluster, added information about Workload domain microsegmentation drawings   |
| 2019-11-05 |             | Pawel Zurawski      |         | added VSAN Witness Host BW requirements                                                                                                                                          |
| 2019-11-08 |             | Radoslaw Dabrowski  |         | Added information about RBAC, Availability and Recoverability section                                                                                                            |
| 2019-11-18 |             | Radoslaw Dabrowski  |         | Added section Workload Domain Logical Router Firewall                                                                                                                            |
| 2019-11-18 |             | Radoslaw Dabrowski  |         | Added section Scalability                                                                                                                                                        |
| 2019-11-18 |             | Radoslaw Dabrowski  |         | Added section Multitenancy                                                                                                                                                       |
| 2019-11-18 |             | Radoslaw Dabrowski  |         | Added section NIOC                                                                                                                                                               |
| 2019-11-18 |             | Pawel Zurawski      |         | Added minimum MTU for configured VLANs                                                                                                                                           |
| 2019-12-17 |             | Pawel Zurawski      |         | Added firewall rules for KMS Cloudlink (VSAN Encryption)                                                                                                                         |
| 2020-01-08 |             | Pawel Zurawski      |         | NSX-T design updated                                                                                                                                                             |
| 2020-01-09 |             | Pawel Zurawski      |         | VSAN Witness requirements updated                                                                                                                                                |
| 2020-01-14 |             | Radoslaw Dabrowski  |         | Review done by SME                                                                                                                                                               |
| 2020-01-15 |             | Radoslaw Dabrowski  |         | Modified section related with Logical Switches to match existing design, added information about uplink profiles, corrected drawing for VSAN Witness host.                       |
| 2020-01-22 |             | Radoslaw Dabrowski  |         | Corrected Management Flows Diagram, Added more precise rules into Physical Firewall section, Added information about Split Brain scenario, Corrected proxy DFW related rules.    |
| 2020-02-03 |             | Radoslaw Dabrowski  |         | Corrected DFW ruleset and addressed remarks from CO                                                                                                                              |
| 2020-02-11 |             | Pawel Zurawski      |         | Added NSX-T Edge Transport Nodes backup details                                                                                                                                  |
| 2020-04-02 |             | Radoslaw Dabrowski  |         | Adjusted Firewall Ruleset for Management DFW                                                                                                                                     |
| 2020-04-02 |             | Radoslaw Dabrowski  |         | Adjusted Security Groups for Management DFW                                                                                                                                      |
| 2020-04-06 |             | Radoslaw Dabrowski  |         | Corrected Abbreviations and Definitions table                                                                                                                                    |
| 2020-04-06 |             | Radoslaw Dabrowski  |         | Adjusted Workload DFW ruleset                                                                                                                                                    |
| 2020-04-09 |             | Pawel Zurawski      |         | DHCP server placement details                                                                                                                                                    |
| 2020-04-14 |             | Radoslaw Dabrowski  |         | PKS optional Management DFW Microsegmentation created                                                                                                                            |
| 2020-04-27 |             | Radoslaw Dabrowski  |         | Added Evanios requirement to external Proxy                                                                                                                                      |
| 2020-05-20 |             | Radoslaw Dabrowski  |         | Added Physical firewall rule for SMTP server, added SMTP reference to the DFW ruleset, corrected Security Groups for Management domain, corrected IP sets for management domain  |
| 2020-05-21 |             | Radoslaw Dabrowski  |         | Adjusted information about PKS ruleset, prepared document for TOS                                                                                                                |
| 2020-05-27 |             | Pawel Zurawski      |         | Added Anthos (optional component) , network requirements from VCS                                                                                                                |
| 2020-05-28 |             | Radoslaw Dabrowski  | VCS 1.1 | Added DFW ruleset in Management Domain for Alcatraz reporting                                                                                                                    |
| 2020-06-10 |             | Radoslaw Dabrowski  |         | Modification of the Management Domain NSX-V to NSX-T according to VCF 4.0 build                                                                                                  |
| 2020-07-17 |             | Radoslaw Dabrowski  |         | Correction of some type-o after SME review                                                                                                                                       |
| 2020-07-31 |             | Marcin Gala         |         | Added chapter about network design for disaster recovery purposes                                                                                                                |
| 2020-08-11 |             | Radoslaw Dabrowski  |         | Added chapter regarding ATF ruleset required for VCS purpose                                                                                                                     |
| 2020-08-13 |             | Radoslaw Dabrowski  |         | Small updates after CO review                                                                                                                                                    |
| 2020-09-17 |             | Radoslaw Dabrowski  |         | Disaster Recovery scenario added                                                                                                                                                 |
| 2020-09-17 |             | Radoslaw Dabrowski  |         | Added DFW DR ruleset for Infoblox                                                                                                                                                |
| 2020-09-18 |             | Radoslaw Dabrowski  |         | Added Design Decision to DR section, add diagram showing Independent Area                                                                                                        |
| 2020-09-20 |             | Tomasz Jasionowski  |         | cht ranamed to bil                                                                                                                                                               |
| 2020-11-19 |             | Radoslaw Dabrowski  |         | Changes into DFW for SRM                                                                                                                                                         |
| 2020-11-19 |             | Radoslaw Dabrowski  | VCS 1.2 | TOS 1.2 Ready                                                                                                                                                                    |
| 2020-12-08 | DHC-674     | Paweł Żurawski      |         | Changes for external NTP time source                                                                                                                                             |
| 2021-02-08 | DHC-1219    | Maciej Losek        |         | Moved vLI from xreg to Region A network and vrealize network to xRegion renamed                                                                                                  |
| 2021-03-11 | DHC-1460    | Paweł Żurawski      |         | Added missing elements, correcting confusing elements for vSAN part, as requested by RSD team                                                                                    |
| 2021-04-20 | DHC-1849    | Maciej Losek        |         | Backup configuration updated as external SFTP server is used for backup                                                                                                          |
| 2021-05-05 | DHC-1851    | Radoslaw Dabrowski  |         | Correct IP networks for router link and transit link                                                                                                                             |
| 2021-05-14 | DHC-1977    | Radoslaw Dabrowski  |         | Extract FW ruleset to separate SDN LLD                                                                                                                                           |
| 2021-06-02 | DHC-1121    | Pawel Zurawski      |         | updated PHY FW rule set with CEB rules                                                                                                                                           |
| 2021-06-11 | DHC-2212    | Pawel Zurawski      |         | updated PHY FW rule set with vSAN Witness                                                                                                                                        |
| 2021-06-11 | DHC-1896    | Pawel Zurawski      |         | updated changes in architecture forced by introduction Multi Tenant feature                                                                                                      |
| 2021-06-22 | DHC-2293    | Marcin Kujawski     |         | updated physical security adding port to allow update witness hosts from vcenter update manager                                                                                  |
| 2021-09-02 | DHC-2814    | Abhijeet Janwalkar  |         | Added  ATF ruleset for TSS to Alcatraz for reports upload                                                                                                                        |
| 2021-09-03 | DHC-2580    | Marcin Gala         |         | updated PHY FW ruleset with ICMP traffic to upstream NTP server                                                                                                                  |
| 2021-09-06 | DHC-2588    | Marcin Gala         |         | added information about BGP metric configuration in case single Physical Firewall is deployed on Datacenter side for Management Domain (AVN networks uplinks)                    |
| 2021-10-08 | DHC-3161    | Krystian Bibik      |         | updated PHY FW rule set with AvAgentToAsn                                                                                                                                        |
| 2021-10-11 | DHC-3178    | Marcin Gala         |         | corrected PHY FW rule set - open UDP port 902 from witness hosts to vCenter Servers, naming convention corrected                                                                 |
| 2022-05-23 | AMC-4033    | Pawel Zurawski      |         | DR A/P ports on PHY FW updated                                                                                                                                                   |
| 2022-06-06 | DHC-4889    | Pawel Zurawski      |         | clarification for minimum required MTU values used by VCS                                                                                                                        |
| 2022-06-15 | AMC-4134    | Pawel Zurawski      |         | DR A/P ports on PHY FW updated, and minor mistakes corrected                                                                                                                     |
| 2022-06-20 | AMC-4136    | Pawel Zurawski      |         | Replication Network MTU changed to 1500                                                                                                                                          |
| 2022-07-22 | CESVXR-504  | Arun Sompura        |         | DR A/P ports on PHY FW updated for VSR and SRM                                                                                                                                   |
| 2022-07-28 | CESDHC-585  | Marcin Gala         |         | Added additional ports in physical FW section - TCP 4433 and 4434 - to cover newly deployed Deep Security AV console in ASN                                                      |
| 2022-12-07 | CESDHC-5125 | Pawel Zurawski      |         | Added segment profile informations for AVN                                                                                                                                       |
| 2022-12-13 | CESDHC-5164 | Pawel Zurawski      |         | Added NSX-T local account information                                                                                                                                            |
| 2022-12-15 | CESDHC-5183 | Lukasz Bienkowski   |         | Added information about IP discovery profile for segments                                                                                                                        |
| 2023-02-27 | CESDHC-5897 | Bhalchandra Gavhane | VCS 1.7 | Update SDN Components Placement diagram for additional Workload Domain                                                                                                           |
| 2023-05-04 | VCS-9445    | Pawel Zurawski      |         | Add IPv6 related information                                                                                                                                                     |
| 2024-05-16 | VCS-12802   | Pawel Zurawski      | VCS 2.0 | Update Multi Tenant section with new features from NSX-T 4.1.x                                                                                                                   |
| 2024-05-31 | VCS-623     | Pawel Zurawski      | VCS 2.0 | Updating physical routing options and recommendations.                                                                                                                           |
| 2025-04-22 |             | Alec Dunn           |         | Fixed missing reference ot VCS in purpose section                                                                                                                                |
| 2024-05-31 | VCS-623     | Pawel Zurawski      | VCS 2.0 | Updating physical routing options and recommendations.                                                                                                                           |
| 2024-31-10 | VCS-17311   | Adam Szymczak       | VCS 2.0 | Add missing Witness host connections.                                                                                                                                            |
| 2026-02-24 | VCS-15538   | Przemyslaw Pakula   | VCS 2.2 | Added Security Requirements Coverage                                                                                                                                             |

# Introduction

## Purpose

The purpose of this document is to provide detailed design and architectural guidance required to implement validated software defined network for a VMware Cloud Services (VCS) deployment in accordance with Atos standards and portfolio services. The principal aim of this document is to translate the high-level design (HLD) into a technical low-level design (LLD).

The design is providing a component architectural overview in the "Architecture Overview" chapter, that provides basic building blocks and main principles, followed by "Detailed Logical Design".

"Architecture Overview" provides basic building blocks and main design principles of the presented design. It covers known requirements cascaded from the HLD and other LLDs.
"Detailed Logical Design" presents business logic, relations and fundamental design decisions.
"Detailed Physical Design" provides detailed configuration of components.

## Audience

This document is intended for Atos Cloud Services Engineers and Architects responsible for VMware Cloud Services (VCS) solution implementation and maintenance.

## Scope

That LLD is intended to cover below components and domains:

1. VCS Management Domain for Software Defined Network (SDN) using VMware NSX
2. VCS Workload Domain for Software Defined Network (SDN) using VMware NSX
3. Connectivity towards the External Network

The following areas are explicitly out of scope:

1. External Network (DC/Campus LAN)
2. Underlay Network – including TOR switches
3. Data Center Management Network
4. IP Address Management

## Related Documents

This document is a subset of Atos Technology Lifecycle Management (ATLM) artefacts. All documents are stored in the VCS documentation repository.

| Document Name                         | Description                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| lldSoftwareDefinedNetworksFirewall.md | Part of the SDN LLD which is containing information about Firewall ruleset. |

##### Vendor Related Documents

| Vendor | Document Name                                                    | Description                                                 |
|--------|------------------------------------------------------------------|-------------------------------------------------------------|
| VMware | <https://configmax.vmware.com>                                   | Documentation which allows to define SDN Maximums for NSX-T |
| VMware | <https://docs.vmware.com/en/VMware-NSX-T-Data-Center/index.html> | Official documentation from VMware related with NSX-T       |

##### Security Requirements Coverage

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

## Requirement Levels

That document is following below principles in terms of requirements and design decisions.

| Term       | Meaning                                                                                                                                                                                                                                                          |
|------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| MUST       | The definition is an absolute requirement of the specification.                                                                                                                                                                                                  |
| MUST NOT   | The definition is an absolute prohibition of the specification                                                                                                                                                                                                   |
| SHOULD     | There may exist valid reasons in particular circumstances to ignore a particular item, but the full implications must be understood and carefully weighed before choosing a different course                                                                     |
| SHOULD NOT | There may exist valid reasons in particular circumstances when the particular behaviour is acceptable or even useful, but the full implications should be understood, and the case carefully weighed before implementing any behaviour described with this label |
| MAY        | Any design decisions that are not classified as MUST and SHOULD or covering optional feature that is not general available for VCS product                                                                                                                       |

# Architecture Overview

Below diagram highlights VCS areas covered in that LLD.

##### VCS Architecture Overview

![Architecture Overview](images/lldSoftwareDefinedNetworks/architectureOverview.png)

VCS is a VMware Cloud Services product built upon a Software Defined Data Center (SDDC) stack using mainly VMware software. Software Defined Networking (SDN) is designed based on VMware NSX-T.

The SDN is split into two main logical parts. Management Domain and Workload Domain. Those constructs provide separation between Management objects and Workload objects (where customer workload would be hosted). This separation is dictated by the VMware VVD (VMware Validated Design) and VCF (VMware Cloud Foundation).

SDN Management Domain as well as SDN Workload Domains are built based on VMware NSX-T. Important thing here is that those are two separate NSX-T Managers and Controllers.

##### Design Decisions for Global SDN

| Decision ID | Design Decision                                                         | Design Justification                                                                                                                                                                                          | Design Implication                                                                                          |
|-------------|-------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|
| ARCDD001    | VMware NSX-T will be used for the SDN Management Domain.                | NSX-T is a requirement of the management domain in the VMware VCF 4.0 onwards architecture.                                                                                                                   | The SDN design must include specific architecture for NSX-T.                                                |
| ARCDD002    | VMware NSX-T will be used for the SDN Workload Domain.                  | NSX-T was chosen due to decisions made in VMware to focus all development resources on developing NSX-T. NSX-T in addition is giving a lot of flexibility regarding other components like Pivotal Containers. | The SDN design must include specific architecture NSX-T.                                                    |
| ARCDD003    | Versioning of the NSX-T is dictated by the VCF Bill of Materials (BOM). | VMware VCF has a defined BOM per VCF version to ensure alignment and compatibility with a specific VCF version.                                                                                               | The specific NSX software version will dictate specific features and functions available within the design. |

##### SDN Components Placement

![SDN components placement](images/lldSoftwareDefinedNetworks/productPlacement-2.svg)

Above figure is showing placement of the SDN components, where NSX Manager for NSX-T as well as three NSX-T Controllers and NSX-T Edge Nodes would be placed on Management domain. Those objects would as well consume Management Domain resources.

NSX Manager for NSX-T would be located on Management Domain as well as it’s Controllers. Edge Nodes though will be placed on Workload Domains.

##### Component Placement Design Decisions

| Decision ID | Design Decision                                                                                              | Design Justification                                                                   | Design Implication                                                                                                                                       |
|-------------|--------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| CP001       | The NSX-T Edge VMs are located on Management Domain and will consume its resources.                          | According to VVD, management components should be separated from workload components.  | North-South network traffic from Management components would traverse through Edge VM. NOTE: This is applicable for all components connected to (VXLAN). |
| CP002       | The NSX-T Edge Node VMs are deployed on each Workload Domain.                                                | There should only be one ingress and egress point in the network for customer traffic. | North and South Network traffic must traverse through Edge Node in Workload Domains.                                                                     |
| CP003       | The NSX-T Manager is hosted in Management Domain buts it is used to manage SDN for all all Workload Domains. | Such scenario is chosen by VCF and VVD.                                                | Single plane of glass for all Workload Domains may cause misunderstanding for pure multi tenant environment.                                             |

## Business and Solution Requirements

Below table provides known requirements mandatory to be incorporated into design decisions of VCS SDN described in that LLD.

##### Initial Requirements

| ID   | Requirement description                                                                                                                                | Requirement Source | Requirement Level |
|------|--------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|-------------------|
| R001 | To follow the VMware Cloud Foundation architecture as closely as possible.                                                                             | VCS HLD            | MUST              |
| R002 | To follow the VMware Validated Design architecture as closely as possible.                                                                             | VCS HLD            | MUST              |
| R003 | All configuration and lifecycle of the SDN for both management and workload must be automated.                                                         | Product Portfolio  | SHOULD            |
| R004 | All SDN components are to be resilient to hardware and software failure.                                                                               | VCS HLD            | MUST              |
| R005 | The SDN is to be able to provide network functionality for all typical customer workloads and include Switching, Routing, Firewall and Load Balancing. | VCS HLD            | MUST              |
| R006 | All components within the SDN are to be monitored for both performance and availability.                                                               | VCS HLD            | MUST              |
| R007 | Security is to be designed into the SDN where possible.                                                                                                | VCS HLD            | MUST              |
| R008 | The SDN design is to be considered PCI DSS ‘Ready’.                                                                                                    | Product Portfolio  | SHOULD            |
| R009 | Reduce costs. Keep the SDN design as efficient as possible, with cost reduction/expense in mind.                                                       | Product Portfolio  | SHOULD            |
| R010 | Vendor default SSL certificates are to be replaced with VCS specific certificates. This is to ensure an enhanced level of security is ensured.         | VCS HLD            | MUST              |

# Detailed Logical Design

## SDN Design

##### Scope of VCS

![Scope](images/lldSoftwareDefinedNetworks/scope.png)

Above figure presents the scope of VCS. Components located inside the Management Domain as well as in Workload Domains are supported by VCS. Underlay networks are independent from VCS and should be managed by NDCS with initial requirements covered by this document, mentioned in section "Underlay requirements for VCS SDN". Communication between components in Management Domain, between components inside Workload Domains as well as cross connection between those domains would be solved by Underlay network.

## Underlay requirements for VCS SDN

### Physical requirements

There are no physical requirements except existence of the Physical Firewall defined later in this section, as well as physical infrastructure which will provide minimum quality of the traffic and necessary protection. Internal routing between VCS management and other Workload Domains in the same site must be less than 5ms. VCS requires network latency between the management stack and all other instances of VCS regions to be lower than 150ms PEAK latency. If DR is selected the max latency between DR locations is 100ms. Physical topology should be build based on Top Of Rack and End Of Row design including redundancy.

### DC Routing for VCS considerations

As DC devices such as Physical Firewall are out of management scope for VCS operations team this includes both DSO and CNT.  
Routing between AVN networks is done via NSX-T as such this traffic is not routed by DC.  
However traffic between AVN and MGT network that is connected vSphere Distributed Virtual Switch is routed using DC equipment, route exchanged between AVN networks (NSX-T hosted) and MGT network (connected to Physical FW that is gateway for that network) is done using BGP described later in this document. This traffic needs to be included throughput requirements for Physical Firewall.  
Another traffic that will traverse Physical Firewall is all traffic sources from MGT Cluster and destined outside VCS POD, this traffic also needs to be taken into account for Physical Firewall throughput considerations.  
Last type of traffic that requires DC devices involvement is ETN (Edge Transport Node vTEP Network) to HTN (ESXi Host Transport vTEP Network), it is important to understand that each North-South connections and also each Bridge connection that involves Edge Node will generate this traffic, as such that traffic can be substancial, in first VCS version it was not considered to route that traffic other way that using Physical Firewall but in DHC-2.0 this principle was changed to address problems with throughput demands for Physical Firewall, even it is still according to VCS design to route this traffic using Physical Firewall it is advisable to use dedicated non-Firewall device such a router for routing this particular traffic. All traffic between ETN and HTN is allowed by VCS design, no policing can be applied on that traffic, and additional if second device like RTR is used, this traffic need to be isolated from other traffic routed by this device by using dedidcated VRF or similar option.

### VLANs

The following list of VLANs represents those that are required and planned for the VCS implementation. Such VLANs should be provided before VCS build will start.

##### VLAN’s requirements

| VLAN           | Minimum L2 Ethernet MTU | Routed Interface | Workload Domain | Management Domain | Purpose                                                                                                           |
|----------------|-------------------------|------------------|-----------------|-------------------|-------------------------------------------------------------------------------------------------------------------|
| Ext VLAN 0     | 1518                    | Yes              | No              | Yes               | VLAN between Customer Router and physical firewall                                                                |
| Ext VLAN 1     | 1518                    | Yes              | Yes             | No                | VLAN between Customer Router and T0 VRF in Workload Domain 1                                                      |
| Ext VLAN 2     | 1518                    | Yes              | Yes             | No                | VLAN between Customer Router and T0 VRF in Workload Domain 2                                                      |
| Ext VLAN 3     | 1518                    | Yes              | No              | Yes               | VLAN between physical firewall and T0 Logical Router in Management Domain.                                        |
| Ext VLAN 4     | 1518                    | Yes              | No              | Yes               | VLAN between physical firewall and T0 Logical Router in Management Domain.                                        |
| MGT            | 1518                    | Yes              | Yes             | Yes               | Management Network                                                                                                |
| vSAN           | 9018                    | Yes              | Yes             | Yes               | vSAN Network                                                                                                      |
| vMotion        | 9018                    | Yes              | Yes             | Yes               | vMotion Network                                                                                                   |
| Replication    | 1518                    | Yes              | Yes             | Yes               | Replication Network                                                                                               |
| vTEP           | 9018                    | Yes              | Yes             | Yes               | Host Transport Node vTEP Network                                                                                  |
| Edge           | 9018                    | Yes              | Yes             | Yes               | Edge Transport Node vTEP Network                                                                                  |
| _VSAN Witness_ | 1518                    | Yes              | N/A             | N/A               | VSAN Witness Appliance Network, hosted on 3rd location or Public Cloud, valid only for Stretched Cluster scenario |

Minimum L2 Ethernet MTU value is minimal value acceptable by VCS Design, that needs to be configured on ports that are connected to VCS, as VCS is using Trunk ports, highest value needs to be used. Ethernet MTU configured on port level can be higher than value specified in this document.

Please be noted that _VSAN Witness VLAN_ is valid only for Stretched Cluster scenario, and beside is part of VCS it will not be hosted in VCS POD, VSAN Witness will be hosted on 3rd location or on Public Cloud.

### Subnets

Below table representing networks which are necessary to create VCS and its components:

##### Subnet list for VCS

| Name                | VLAN             | Subnet            | Subnet Prefix Length          | L3 IP MTU | Description                                                                                                                                           |
|---------------------|------------------|-------------------|-------------------------------|-----------|-------------------------------------------------------------------------------------------------------------------------------------------------------|
| Management Network  | < MGT >          | Customer Routable | 24                            | 1500      | Management network                                                                                                                                    |
| vSAN Network        | < vSAN >         | Customer Routable | 24                            | 9000      | vSAN Network                                                                                                                                          |
| vMotion Network     | < vMotion >      | Customer Routable | 24                            | 9000      | vMotion Network                                                                                                                                       |
| Replication Network | < Replication >  | Customer Routable | 24                            | 1500      | Replication Network                                                                                                                                   |
| vTEP Network        | < vTEP >         | Customer Routable | 24                            | 9000      | vTEP Network (On PHY FW or on dedicated RTR)                                                                                                          |
| Edge Network        | < Edge >         | Customer Routable | 24                            | 9000      | Edge Transport Node vTEP Network (On PHY FW or on dedicated RTR)                                                                                      |
| xRegion Network     | < xRegion >      | Customer Routable | 24                            | 1500      | Cross Region Network                                                                                                                                  |
| Region A Network    | < Region A >     | Customer Routable | 24                            | 1500      | Region A Network                                                                                                                                      |
| External Network 0  | < Ext VLAN 0 >   | Customer Routable | Customer Specific             | 1500      | Subnet between Customer Router and physical firewall                                                                                                  |
| External Network 1  | < Ext VLAN 1 >   | Customer Routable | Customer Specific             | 1500      | Subnet between Customer Router and Workload Domain 1 T0 VRF                                                                                           |
| External Network 2  | < Ext VLAN 2 >   | Customer Routable | Customer Specific             | 1500      | Subnet between Customer Router and Workload Domain 2 T0 VRF                                                                                           |
| External Network 3  | < Ext VLAN 3 >   | Customer Routable | Customer Specific             | 1500      | Subnet between Physical Firewall and Management Domain T0 Logical Router                                                                              |
| External Network 4  | < Ext VLAN 4 >   | Customer Routable | Customer Specific             | 1500      | Subnet between Physical Firewall and Management Domain T0 Logical Router                                                                              |
| _VSAN Witness_      | < VSAN Witness > | Customer Routable | Customer Specific, default 28 | 1500      | VSAN Witness Appliance Network, one Witness Appliance per Cluster, hosted on 3rd location of Public Cloud, valid only for Stretched Clusters scenario |

IP MTU value specified in this document need to be configured on Gateway device for specified VLAN/subnet. Configured value need to be exactl as is specified in this document.

Please be noted that _VSAN Witness Subnet_ is valid only for Stretched Cluster scenario, and beside is part of VCS it will not be hosted in VCS POD, VSAN Witness will be hosted on 3rd location or on Public Cloud.

### Physical Security

VCS is a product which relies on existing physical infrastructure. Because of this, the physical network should provide security functionalities. Required security can be accomplished by providing a device capable of inspecting traffic between networks. VCS relies on networks which should be terminated (gateway should be) on the firewall device. Figures in this document will not name any specific vendor of this firewall, all firewalls which are capable of defining zones, and setup BGP adjacency are good (Juniper SRX, Cisco ASA context or other capable firewalls). For simplicity in below sections this device will be named "Physical Firewall".

In VCS we present two types of zones - Internal and External:

- Internal - internal networks inside VCS - there is no need to duplicate security between internal networks if internal security is solved by NSX
- External - external networks to VCS

Above split is allowing to prepare simple ruleset which is required on Physical Firewall. Below table is displaying ruleset based on subnets defined in previous section. Table will contain as well groups of objects which will be defined later.

##### Ruleset for Physical Firewall

| Firewall Rule Name                  | Source                                                                                                                                               | Destination                                                                                                                                         | Service                                                                                 | Action | Annotation                                                                                                     |
|-------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------|--------|----------------------------------------------------------------------------------------------------------------|
| DomainControllersToTimeSource       | AD Controllers and All ESXis                                                                                                                         | NTP Time Source                                                                                                                                     | UDP123, ICMP                                                                            | ACCEPT | Valid only if PHY FW is not Time Source for VCS                                                                |
| ProxyToInternet                     | Internal Proxy                                                                                                                                       | Internet                                                                                                                                            | HTTPS, HTTP, TCP/4430, TCP/4431, TCP/4432, TCP/4433, TCP/4434, TCP/4445                 | ACCEPT |                                                                                                                |
| AsnToTerminalServers                | ASN                                                                                                                                                  | TSS                                                                                                                                                 | RDP                                                                                     | ACCEPT |                                                                                                                |
| CrossInternalNetworksConnection     | Replication Network <br>  Management Network <br>  xRegion Network <br> Region A Network <br> Overlay Network<br>  vMotion Network<br>  vSAN Network | Replication Network <br> Management Network <br> xRegion Network <br> Region A Network <br>  Overlay Network <br> vMotion Network <br> vSAN Network | any                                                                                     | ACCEPT |                                                                                                                |
| NsxtInterconnections                | vTEP Network <br> Edge Network                                                                                                                       | vTEP Network <br>  Edge Network                                                                                                                     | any                                                                                     | ACCEPT | Implemented only if no dedicated router is used for that traffic, so these interfaces are available on PHY FW. |
| SmtpToAtf                           | SMTP Server                                                                                                                                          | ATF                                                                                                                                                 | SMTP                                                                                    | ACCEPT |                                                                                                                |
| AnsibleToAtf                        | Ansible Server                                                                                                                                       | ATF                                                                                                                                                 | HTTPS                                                                                   | ACCEPT |                                                                                                                |
| vSanWitnessesToVcenters             | vSAN Witnesses                                                                                                                                       | vCenters + PSC                                                                                                                                      | TCP/443, UDP/902, TCP/902, TCP/9080                                                     | ACCEPT | Valid only for DR A/A                                                                                          |
| vCentersToVsanWitnesses             | vCenters + PSC                                                                                                                                       | vSAN Witnesses                                                                                                                                      | TCP/443, UDP/902, TCP/902, TCP/9080                                                     | ACCEPT | Valid only for DR A/A                                                                                          |
| SddcManagerToVsanWitnesses          | SDDC Manager                                                                                                                                         | vSAN Witnesses                                                                                                                                      | TCP/22 , TCP/443                                                                        | ACCEPT | Valid only for DR A/A                                                                                          |
| vSanWitnessesToEsxis                | vSAN Witnesses                                                                                                                                       | All ESXis                                                                                                                                           | TCP/2233, UDP/12321, ICMP                                                               | ACCEPT | Valid only for DR A/A                                                                                          |
| esxisToVsanWitnesses                | All ESXis                                                                                                                                            | vSAN Witnesses                                                                                                                                      | TCP/2233, UDP/12321, ICMP                                                               | ACCEPT | Valid only for DR A/A                                                                                          |
| vSanWintessesToAD                   | vSAN Witnesses                                                                                                                                       | AD Controllers                                                                                                                                      | UDP/53 , UDP/123                                                                        | ACCEPT | Valid only for DR A/A                                                                                          |
| Ansible002ToWitnessVsan             | ANS002                                                                                                                                               | vSAN Witnesses                                                                                                                                      | TCP/22 , TCP/443                                                                        | ACCEPT | Valid only for DR A/A                                                                                          |
| AnsibleToWitnessVsan                | Ansible Server                                                                                                                                       | vSAN Witnesses                                                                                                                                      | TCP/22 , TCP/443                                                                        | ACCEPT | Valid only for DR A/A                                                                                          |
| TerminalServersToWitnessVsan        | TSS                                                                                                                                                  | vSAN Witnesses                                                                                                                                      | TCP/22 , TCP/443                                                                        | ACCEPT | Valid only for DR A/A                                                                                          |
| vSanWitnessesToVcenterUpdateManager | vSAN Witnesses                                                                                                                                       | vCenters + PSC                                                                                                                                      | TCP/80 , TCP/9000, TCP/9084                                                             | ACCEPT | Valid only for DR A/A                                                                                          |
| PrimaryAndDrSiteTraffic             | SRM                                                                                                                                                  | Remote vCenter Server                                                                                                                               | HTTPS                                                                                   | ACCEPT | Valid only for DR A/P                                                                                          |
| PrimaryAndDrSiteTrafficHosts        | SRM                                                                                                                                                  | Remote ESXi hosts                                                                                                                                   | HTTPS, TCP/902, UDP/902                                                                 | ACCEPT | Valid only for DR A/P                                                                                          |
| PrimaryAndDrSiteSrm                 | SRM                                                                                                                                                  | Remote SRM                                                                                                                                          | HTTPS                                                                                   | ACCEPT | Valid only for DR A/P                                                                                          |
| PrimaryAndDrSiteSrm                 | Network                                                                                                                                              | Remote SRM                                                                                                                                          | HTTPS                                                                                   | ACCEPT | Valid only for DR A/P                                                                                          |
| PrimaryAndDrSiteVcs                 | Replication Network                                                                                                                                  | Remote vCenter and PSC                                                                                                                              | HTTPS                                                                                   | ACCEPT | Valid only for DR A/P                                                                                          |
| PrimaryAndDrSiteVsr                 | Replication Network                                                                                                                                  | Remote VSR                                                                                                                                          | TCP31031, TCP/31032                                                                     | ACCEPT | Valid only for DR A/P                                                                                          |
| PrimaryAndDrReplication             | Replication Network                                                                                                                                  | Remote Replication Network                                                                                                                          | TCP/8043                                                                                | ACCEPT | Valid only for DR A/P                                                                                          |
| PrimaryAndDrSiteAdc                 | ADC                                                                                                                                                  | Remote ADC                                                                                                                                          | UDP/53 , TCP/53                                                                         | ACCEPT | Valid only for DR A/P                                                                                          |
| PrimatyAndDrSiteAnsibleToNsx        | ANS                                                                                                                                                  | Remote NSX-T Manager                                                                                                                                | HTTPS                                                                                   | ACCEPT | Valid only for DR A/P                                                                                          |
| DrAndPrimarySiteAnsibleToAnsible    | ANS                                                                                                                                                  | Remote ANS                                                                                                                                          | SSH , ICMP                                                                              | ACCEPT | Valid only for DR A/P                                                                                          |
| PrimatyAndDrSiteAnsibleToVault      | ANS                                                                                                                                                  | Remote HSV                                                                                                                                          | TCP/8200 , ICMP                                                                         | ACCEPT | Valid only for DR A/P                                                                                          |
| PrimaryAndDrSiteInfoblox            | INF                                                                                                                                                  | Remote INF                                                                                                                                          | UDP/1194 , UDP/2114                                                                     | ACCEPT | Valid only for DR A/P                                                                                          |
| PrimaryAndDrSiteAbxToVcenter        | ABX                                                                                                                                                  | Remote vCenter + PSC                                                                                                                                | HTTPS                                                                                   | ACCEPT | Valid only for DR A/P                                                                                          |
| PrimaryAndDrSiteAbxToInfoblox       | ABX                                                                                                                                                  | Remote Infoblox Grid                                                                                                                                | HTTPS , ICMP                                                                            | ACCEPT | Valid only for DR A/P                                                                                          |
| SrmToRemoteVsr                      | SRM                                                                                                                                                  | Remote VSR                                                                                                                                          | TCP/8043                                                                                | ACCEPT | Valid only for DR A/P                                                                                          |
| PrimaryAndDrSiteVcs                 | vCenters + PSC                                                                                                                                       | Remote vCenters + PSC                                                                                                                               | TCP/10433                                                                               | ACCEPT | Valid only for DR A/P                                                                                          |
| DrAndPrimarySiteVcs                 | Remote vCenters + PSC                                                                                                                                | vCenters + PSC                                                                                                                                      | TCP/10433                                                                               | ACCEPT | Valid only for DR A/P                                                                                          |
| PrimaryVsrToRemoteSrm               | Local VSR                                                                                                                                            | Remote SRM                                                                                                                                          | HTTPS                                                                                   | ACCEPT | Valid only for DR A/P                                                                                          |
| RemoteVsrToPrimarySrm               | Remote VSR                                                                                                                                           | Local SRM                                                                                                                                           | HTTPS                                                                                   | ACCEPT | Valid only for DR A/P                                                                                          |
| AvAgentToAsn                        | All Linux VMs except ans002 <br> All Windows VMs except rca001                                                                                       | ASN                                                                                                                                                 | TCP/4430, TCP/4431, TCP/4432, TCP/4433, TCP/4434, TCP/4445                              | ACCEPT | Antivirus Agent connection to Antivirus server in ASN                                                          |
| AvamarToVcenter                     | AvamarUtilityNode                                                                                                                                    | vCenters + PSC                                                                                                                                      | ICMP echo , TCP/443                                                                     |        | Valid only for CEB                                                                                             |
| AvamarToAvp                         | AvamarUtilityNode                                                                                                                                    | AvamarVMwareProxy                                                                                                                                   | ICMP echo , TCP/4 , TCP/5489 , TCP/30009                                                |        | Valid only for CEB                                                                                             |
| AvamarToEsxi                        | AvamarUtilityNode                                                                                                                                    | All ESXis                                                                                                                                           | TCP/443                                                                                 |        | Valid only for CEB                                                                                             |
| AvpToAvamar                         | AvamarVMwareProxy                                                                                                                                    | AvamarUtilityNode                                                                                                                                   | UDP/137 , UDP/138 , UDP/139 , TCP/28001 , TCP/29000 , TCP/30001 , TCP/30003 , ICMP echo |        | Valid only for CEB                                                                                             |
| VcenterToAvamar                     | vCenters + PSC                                                                                                                                       | AvamarUtilityNode                                                                                                                                   | TCP/443                                                                                 |        | Valid only for CEB                                                                                             |
| AnsibleToAvamar                     | Ansible                                                                                                                                              | AvamarUtilityNode                                                                                                                                   | TCP/443                                                                                 |        | Valid only for CEB                                                                                             |
| Default Rule                        | any                                                                                                                                                  | any                                                                                                                                                 | any                                                                                     | DENY   |                                                                                                                |

Please note that "Valid only for DR A/A" rules are valid only for Stretched Cluster scenario, and can be omitted if there is no plan for Stretched Cluster.
Please note that "Valid only for DR A/P" rules are valid only for DR Active / Passive scenarios, and can be omitted if DR is not used.
Please note that "Valid only for CEB" rules are valid only if Canopy Enterprise Backup service will be choose as backup solution.
Below figure shows flows which have been defined in VCS where traffic is exiting Zones.

In case a customer has implemented a dedicated Key Management System (BYOK - Bring-your-own-key - implementation), the physical firewall ruleset must be extended with the rules below:

| Firewall Rule Name | Source | Destination | Service | Action | Annotation |
| --- | --- | --- | --- | --- | --- |
| vCenterToCustomerKMS | vCenter02 management interface | KMS‑EXTERNAL (all KMS nodes) | TCP 5696 | ALLOW | VM Encryption key exchange |
| esxiToCustomerKMS | ESXi CMP management interfaces | KMS‑EXTERNAL | TCP 5696 | ALLOW | VM Encryption key exchange |
| trustAuthToCustomerKMS | TrustAuth Cluster | KMS‑EXTERNAL | TCP 5696 | ALLOW | Trust‑Authority KMIP calls |

##### Management traffic passing Physical Firewall

![traffic flow](images/lldSoftwareDefinedNetworks/flows.png)

Red Area represents the External Zone, and green represents the Internal Zone. Inspection of the traffic should be done only when traffic is going from External Zone to Internal or from Internal to External. In addition, traffic should be opened for all established connections. Important here to understand is that the figure presents outgoing traffic, as incoming may be different (i.e. outgoing traffic from vROps Virtual Machine is done directly from VM, while incoming packets are destined to vROps Edge Load Balancer VIP address to reach one of the vROps VMs). Other requirement is to provide Internet connectivity for Management Domain. This might be done by providing direct Internet access or by using other proxy.

##### Requirements for Underlay Network - Stretched Clusters

| Requirements                           | Value              |
|----------------------------------------|--------------------|
| Minimum Bandwidth between DCs          | 10 Gbps            |
| Minimum supported MTU between DCs      | 9016 B             |
| Maximum Latency between DCs (RTT)      | 5 ms               |
| Maximum FW cluster switch-over time    | 3 s                |
| Active cluster member placement        | AZ1                |
| Physical FW cluster pre-emption        | enabled            |
| Cluster switchover (recover) delay     | 5 s                |
| Minimum Bandwidth to VSAN Witness Host | 20 Mbps / 1000 VMs |
| Maximum Latency to VSAN Witness Host   | 100 ms             |

Please note that VSAN Witness Host is used only in Stretched Cluster scenario.

### ATF Ruleset

This section covers required rules for ATF firewall to allow VCS components communicate with corresponding tools. Following table represents form which might be copy and pasted to the official ATF form.

| Firewall Rules                          | Add | Remove | Permanent | Source                        | Destination                | UDP/TCP Port number | Transport \(IP\) protocol | Application Protocol |
|-----------------------------------------|-----|--------|-----------|-------------------------------|----------------------------|---------------------|---------------------------|----------------------|
| Allow SMTP traffic to SMTP ASN Server 1 | x   |        |           | \<Management Network\>\.67/32 | 155\.45\.163\.100          | 25                  | tcp                       | SMTP                 |
| Allow SMTP traffic to SMTP ASN Server 2 | x   |        |           | \<Management Network\>\.67/32 | 155\.45\.163\.120          | 25                  | tcp                       | SMTP                 |
| TOSCA / ALCATRAZ \- compliance scan     | x   |        |           | \<Management Network\>\.37/32 | 161\.89\.90\.30            | 443                 | tcp                       | HTTPS                |
| TOSCA / ALCATRAZ \- compliance scan     | x   |        |           | \<Management Network\>\.39/32 | 161\.89\.90\.30            | 443                 | tcp                       | HTTPS                |
| CSI \- reporting                        | x   |        |           | \<Management Network\>\.48/32 | 161\.89\.178\.210          | 22                  | tcp                       | SFTP                 |
| ATF1 to TSS server 1                    | x   |        |           | 161\.89\.0\.0/16              | \<Management Network\>\.22 | 3389                | tcp                       | RDP                  |
| ATF2 to TSS server 1                    | x   |        |           | 155\.45\.128\.0/17            | \<Management Network\>\.22 | 3389                | tcp                       | RDP                  |
| ATF1 to TSS server 2                    | x   |        |           | 161\.89\.0\.0/16              | \<Management Network\>\.23 | 3389                | tcp                       | RDP                  |
| ATF2 to TSS server 2                    | x   |        |           | 155\.45\.128\.0/17            | \<Management Network\>\.23 | 3389                | tcp                       | RDP                  |
| TOSCA / ALCATRAZ \- compliance scan     | x   |        |           | \<Management Network\>\.22/32 | 161\.89\.90\.30            | 443                 | tcp                       | HTTPS                |
| TOSCA / ALCATRAZ \- compliance scan     | x   |        |           | \<Management Network\>\.23/32 | 161\.89\.90\.30            | 443                 | tcp                       | HTTPS                |
| Allow SMTP traffic to SMTP ASN Server 1 | x   |        |           | < Management Network >.67/32  | 155\.45\.163\.100          | 25                  | tcp                       | SMTP                 |
| Allow SMTP traffic to SMTP ASN Server 2 | x   |        |           | < Management Network >.67/32  | 155\.45\.163\.120          | 25                  | tcp                       | SMTP                 |
| TOSCA / ALCATRAZ \- compliance scan     | x   |        |           | < Management Network >.37/32  | 161\.89\.90\.30            | 443                 | tcp                       | HTTPS                |
| TOSCA / ALCATRAZ \- compliance scan     | x   |        |           | < Management Network >.39/32  | 161\.89\.90\.30            | 443                 | tcp                       | HTTPS                |
| CSI \- reporting                        | x   |        |           | < Management Network >.48/32  | 161\.89\.178\.210          | 22                  | tcp                       | SFTP                 |
| ATF1 to TSS server 1                    | x   |        |           | 161\.89\.0\.0/16              | < Management Network >.22  | 3389                | tcp                       | RDP                  |
| ATF2 to TSS server 1                    | x   |        |           | 155\.45\.128\.0/17            | < Management Network >.22  | 3389                | tcp                       | RDP                  |
| ATF1 to TSS server 2                    | x   |        |           | 161\.89\.0\.0/16              | < Management Network >.23  | 3389                | tcp                       | RDP                  |
| ATF2 to TSS server 2                    | x   |        |           | 155\.45\.128\.0/17            | < Management Network >.23  | 3389                | tcp                       | RDP                  |

### Network Services

##### NTP (Time Source)

Two Time Sources supporting NTPv4 are required. Time Source can be a Physical Firewall (Gateway) itself or other device. Time Sources need to be Stratum 3 or lower. Time Sources can not be local. Sources need to be synced with Stratum 0 Time Source. Traffic between VCS AD Controllers and Time Sources needs to be allowed.

### Other requirements

Other requirements are covered in lldSddcVmwareCloudFoundation.md.

## Management Domain

##### SDN Management Domain

![management domain](images/lldSoftwareDefinedNetworks/managementDomain.png)

The SDN Management Domain is build based on VMware NSX-T. During deployment of the VCF, it will create necessary components for Management Domain including PortGroups, Logical Routers T0 and T1, Logical Switches. Most important networks (those where management VMs and VMKernel interfaces are residing) are deployed as a PortGroups in vCenter VDS. Logical Routers T0 and T1 as well as corresponding Logical Switches are build for vRealize components. For his purpose two networks are created - "Region A Network" and "xRegion Network".

To explain more what is happening between Management Domain VMs and external infrastructure, follow below diagram:

![management domain sdn](images/lldSoftwareDefinedNetworks/managementDomainSdn.png)

T0 Logical Router is deployed in Active/Active mode with ECMP enabled. This provides redundancy and good bandwidth utilization (both uplinks can be used). Design requires two uplink VLANs for North/South connectivity (for Edge VMs in Edge Cluster).  Uplink Profile dedicated for this connectivity defines teaming policies to be used in Edge uplink transport zone and the segments. To provide routing between SDN in VCS and physical environment, eBGP protocol would be used (connection between T0 Logical Router and Physical Firewall). In addition to that iBGP session is established between T0 Edge VMs Service Routers components to provide internal sync.

If DC design is according to assumptions, and Physical Firewall is acting as a VCS GW for Management Networks, BGP MED and Local Preference attributes need to be modified on the Physical Firewall to avoid asymmetric routing. Route related to Preferred VLAN should have MED BGP attribute configured to 90 and Local Preference BGP attribute configured to 120. Route related to Backup VLAN should have the default attribute values configured - MED equal to 100 and Local Preference equal to 100 as depicted below on the image below:

![single device SDN](images/lldSoftwareDefinedNetworks/managementDomainSdnSingleDevice.png)

##### Design Decisions for Management Domain

| Decision ID | Design Decision                                                                                                                                                                                            | Design Justification                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | Design Implication                                                                                                                                                             |                                                                                                                                                                                                                                                                                                                                                                                                       |
|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| MGTDD001    | Physical Firewall will terminate Management Network and will provide Firewalling capabilities. This might be any firewall which is capable of fulfilling VCS requirements (ASA, Checkpoint, SRX or other). | Management Network where crucial components like vCenter Server or NSX Managers are located requires a Firewall which will filter traffic to those components                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          | This requires ordering a pair of Physical firewalls which would be clustered and adding additional configuration to those. Or configuring existing one to fulfil requirements. |                                                                                                                                                                                                                                                                                                                                                                                                       |
| MGTDD002    | Assign static IP addresses to all management components in the VCS infrastructure except NSX-T TEPs. NSX-T TEPs are assigned by using DHCP server.                                                         | Ensures that interfaces such as management and storage always have the same IP address. In this way, you provide support for continuous management of ESXi hosts using vCenter Server and for provisioning IP storage by storage administrators. NSX-T TEPs do not have an administrative endpoint. As a result, they can use DHCP for automatic IP address assignment. You are also unable to assign directly a static IP address to the VMkernel port of an NSX-T TEP. IP pools are an option but the NSX-T administrator must create them. If you must change or expand the subnet, changing the DHCP scope is simpler than creating an IP pool and assigning it to the ESXi hosts. | Requires accurate IP address management.                                                                                                                                       | Management of the Management Domain might be done from ASN Admingates by accessing dedicated MS Windows based terminal server (also called Jump server or Stepping-Stone). This Bastion Server will be placed on Management Network where other management components are located. Because Management Network is using customer pentacle network, there is a need to make NAT on ASN Router/Firewall. |
| MGTDD003    | Logical Routers T0 and T1 as well as corresponding Logical Switches are created in Management Domain for vRealize components                                                                               | Components are built by VCF by default and there is no way to overwrite this.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          | Additional VLANs are required, Physical Firewalls should handle Dynamic Routing Protocol.                                                                                      |                                                                                                                                                                                                                                                                                                                                                                                                       |

##### ASN Connection to VCS Terminal Servers

![asn connection](images/lldSoftwareDefinedNetworks/asnConnection.png)

Logical Routers T0 and T1 are deployed for Region A and Cross Region networks. vROps components will be deployed under those networks. In addition to that, T1 Logical Router will provide inline Load balancing feature for all vROps Collectors.

## Workload Domain

Workload Domain is a construct within the VCS deployment for the purpose of running customer Workloads. The SDN Workload Domain consists number of the Logical Routers (LR) and Logical Switches (LS) dependent upon the VCS Customer requirements. Each deployment will have Tier 0 LR which is acting as Border Router between External environment or Management Domain and VCS Workload Domain.

Workload Domain consists of Tier 1 LRs, which are created on demand and joined to Tier 0 Logical Router. Each Tier 1 LR will connect Logical Switches to its interfaces to provide default gateway capabilities, as well as Routing between those Logical Switches. It will contain two instances – Distributed Router and SR (Service Router). Distributed Router will provide East-West communication Logical Switches, where SR will provide North-South communication and centralized services such as Firewall, NAT, Load Balancing.

##### SDN Workload Domain

![workload domain](images/lldSoftwareDefinedNetworks/workloadDomain.png)

Customer Routers will communicate with Customer workload using dedicated VLAN which will be terminated by T0 VRF Service Router. To provide dynamic routing capability to learn routes, there is a possibility that T0 VRF would be configured with BGP routing protocol. To avoid issues with hardware failure, there is a requirement to provide at least two Customer Routers.

T0 and T1 Logical Routers would be deployed inside Edge Nodes which will construct Edge Cluster.

## Management Plane

The management plane is built by the NSX Manager, the centralized network management component of NSX-T. It provides the single point of configuration and REST API entry-points. The NSX Manager is installed as a Virtual IP on a controllers which are installed as a virtual appliances on ESXi hosts in vCenter Server environment. Both Management Domain as well as Workload Domains are using NSX-T as a SDN management platform.

#### NSX-T Manager

NSX-T Manager is deployed as a Controller Cluster of three VMs inside Management Network. Management Domain will be managed by dedicated instance of the NSX-T Manager. All Workload Domains will be managed by another dedicated instance of the NSX-T Manager. In case of increasing amount of Workload Domains, number of the NSX-T Managers are not changing.

NSX-T Manager VMs (controllers) are sized to meet the load requirements for all VCS deployments and is also predefined size in VMware VCF design and VCF SDDC Manager automatic deployment. NSX-T Manager is placed within Management Network which is reducing a need to open any traffic between Manager and ESXi’s.

The default vendor SSL certificate used within the NSX-T Manager will be replaced with a VCS specific certificate. This will be managed by the certificate management functionality included in the VCS VCF SDDC Manager.

##### NSX-T Manager Settings

| Parameter            | Value                                                                                  | Description/Justification                                                                                                                                                                    |
|----------------------|----------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Single Sign On (SSO) | Configured using the vCenter Server.                                                   | Single Sign On for NSX-T Manager                                                                                                                                                             |
| NTP                  | Configuration would be done via VCF and will be synchronized with Active Directory VM. | Configuration of the NTP Server and Time Zone, for accurate time synchronization.                                                                                                            |
| Syslog Server        | IP Address or FQDN of the VCS VMware Log Insight, Port 514, UDP protocol               | Syslog Server for remote logging                                                                                                                                                             |
| Backup               | Defined below                                                                          | NSX-T backup contains a copy of the NSX database which includes all details of NSX components including Controllers, Transport Zones, Logical Switches, Edges/DLRs and all Security objects. |

In VCS an external SFTP server (ans001) with SDDC Manager is registered for backing up NSX Managers.
>NOTE: When an external SFTP server is configured, SDDC Manager configures all existing NSX Managers to use the SFTP server. When subsequent NSX Managers are deployed, SDDC Manager configures them to use this SFTP server as well but backup interval must be modified in the NSX Manager UI.

The NSX backup covers the configuration of the NSX Manager itself including IP address, NTP and DNS settings along with its external connections to vCenter.

Backups are encrypted using a passphrase which is stored in HashiVault ( path: `secret/< customerCode >/< locationCode >/servers/< locationCode >sdm001/backupVcfPassphrase`). The SFTP access will be performed using a domain service account  called ‘svc-{locationCode}-bck01’.

The NSX-T backup directory structure is as follow:

- main folder configured as a backup path: `/backup/vcf`
- subfolder for NSX-T Manager Cluster: `/backup/vcf/cluster-node-backups`. Inside is a folder for each NSX-T Manager (mgmt and cmp), Manager node and each backup with the files backups (controller, manager, policy
and node backup files)
- subfolder for NSX-T Cluster inventory backups: `/backup/vcf/inventory-summary`. Inventory backups will be set to occur every 60 minutes.

The backup files are approximately 10MB each and we would retain these for minimum 14 days consuming 140MB per NSX Manager, to be comfortable 200 GB of disk space is allocated on ans001 vm for VCF components backups.

##### Backup Configuration on NSX-T (mgmt and cmp)

| Parameter                    | Value                                                                                  |
|------------------------------|----------------------------------------------------------------------------------------|
| Automatic Backup             | Enabled                                                                                |
| IP/Host name                 | < ans001 IP Address >                                                                  |
| Port                         | 22                                                                                     |
| Transfer Protocol            | SFTP                                                                                   |
| User Name                    | svc-{locationCode}-bck01                                                               |
| Password                     | < password for the svc-< locationCode >-bck01 domain account - stored in HashiVault >  |
| Destination Directory        | `/backup/vcf`                                                                          |
| Backup encryption passphrase | automatically created during external SFTP server configuration - stored in HashiVault |
| SSH fingerprint              | SHA-256 fingerprint of ECDSA key                                                       |

Backups will be taken on a daily basis in the evening. They should be retained on the backup server for a minimum of 14 days after which they can be cleaned.

##### Backup frequency for NSX-T (mgmt and cmp)

| Parameter                              | Value                                                          |
|----------------------------------------|----------------------------------------------------------------|
| Node/Cluster Backup (Backup Frequency) | Weekly                                                         |
| Node/Cluster Backup (Days)             | Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday |
| Node/Cluster Backup (Time)             | 00:00                                                          |

##### NSX-T Manager Design Decisions

| Decision ID | Design Decision                                                                | Design Justification                                                                                                                                                                                                                                                                               | Design Implication                                                                                                      |
|-------------|--------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------|
| NSXTM001    | Use the default NSX-T Manager VMs sizing.                                      | Limited physical resource use and maximum efficiency should be ensured where possible. The default sizing is sufficient to meet the current and any future load requirements.                                                                                                                      | Reduces the management resource overhead of the VCS deployment.                                                         |
| NSXTM002    | Use SFTP as the transfer protocol for all NSX-T backups.                       | To ensure the secure transfer of the backup data between the NSX-T manager and the FTP server.                                                                                                                                                                                                     | The server used as a destination for the backup files must be configured with SFTP.                                     |
| NSXTM003    | A pass phrase will be configured for the NSX-T manager backups.                | To ensure the backups can only be restored when the passphrase is known by the administrator. This pass phrase is to be stored in the VCS password manager. If there is any change in the passphrase to the existing, the old passphrase while restoring previously taken backups need to be used. | A secure method for storing passwords in VCS must exist.                                                                |
| NSXTM004    | Sufficient resources should be reserved in vCenter for the NSX-T Manager VM.   | In the event of resource contention sufficient resources should always be reserved and available for the NSX-T Manager VM. This is to ensure NSX-T is always performing as required.                                                                                                               | The management domain, management cluster, should have enough physical resources to ensure all reservations can be met. |
| NSXTM005    | Deploy a three node NSX-T Manager cluster using the large-size appliance.      | The large-size appliance supports more than 64 ESXi hosts. NSX-T Managers are included into Controllers mentioned below.                                                                                                                                                                           | The large size requires more resources in the management cluster.                                                       |
| NSXTM006    | Create a virtual IP (VIP) for the NSX-T Manager cluster.                       | Provides HA for the NSX-T Manager UI and API.                                                                                                                                                                                                                                                      | The VIP provides HA only, it does not load balance requests across the manager cluster.                                 |
| NSXTM007    | Management Domain and Workload Domains Backup frequency would be same frequent | SDDC Manager v. 4.0 does not configure NSX-T Managers Backup frequency and it must be configured separately. Management Domain and Workload Domain's NSX-T Manager would backup its configuration once a day.                                                                                      | ans001 is prepared to be SFTP server and it contains enough space for backups                                           |

## Control Plane

The control plane runs in the NSX Controller cluster. The NSX Controller is an advanced distributed state management system that provides control plane functions for logical switching and routing functions. It is the central control point for all logical switches within a network and maintains information about all hosts, logical switches (VXLANs), and distributed logical routers.

The NSX Controller Cluster is responsible for managing hypervisors and their distributed switching and routing modules. Controllers are not taking part in the Data Plane traffic.

The NSX Controller Cluster is always deployed with three members to enable high-availability and scale. In case of failure of controllers’ nodes, Data Plane traffic is not impacted. Three virtual appliances provide, maintain and update the state of all network functioning within NSX domain.

### NSX Controllers for NSX-T

Similar to the NSX-T Managers, Controllers are built separately for Management Domain and separately for Workload Domains, but configuration and design decisions are same for both. The Controllers for NSX-T providing learning function of the Overlay Network. They are responsible for providing the MAC learning and ARP learning function and directing ESXi hosts on how best to communicate with other ESXi hosts that are members of the same Logical Switch inside Workload Domain.

Controllers are deployed inside the Management Network, three of them maintain the Management Domain, remaining three the Workload domain. Controllers for the Workload Domain are deployed once as they are maintaining all Workload Domains, so additional workload domains share these components.

Inside the Controller Cluster, there is a requirement to have a quorum (majority) in order to avoid “split-brain scenario” which can break functionality of the SDN.

##### NSX-T Controllers failure domain

|                                | Min | Max |
|--------------------------------|-----|-----|
| Number of NSX Nodes in Cluster | 2   | 3   |
| Majority Number                | 2   | 2   |
| Number of Nodes that can fail  | 0   | 1   |

There is a need to create Anti-Affinity rules to keep Controllers separated.

Management Controllers would be built with following size:

##### NSX-T Controllers sizes Management Domain

| Appliance             | Memory | vCPU | Disk Space |
|-----------------------|--------|------|------------|
| NSX-T Controller VM 1 | 24GB   | 6    | 320GB      |
| NSX-T Controller VM 2 | 24GB   | 6    | 320GB      |
| NSX-T Controller VM 3 | 24GB   | 6    | 320GB      |

Workload Controllers would be built with following size:

##### NSX-T Controllers sizes Workload Domains

| Appliance             | Memory | vCPU | Disk Space |
|-----------------------|--------|------|------------|
| NSX-T Controller VM 1 | 48GB   | 12   | 360GB      |
| NSX-T Controller VM 2 | 48GB   | 12   | 360GB      |
| NSX-T Controller VM 3 | 48GB   | 12   | 360GB      |

##### Design Decisions for NSX-T Controllers

| Decision ID | Design Decision                                                                                 | Design Justification                                                                         | Design Implication                                                      |
|-------------|-------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------|-------------------------------------------------------------------------|
| NSXTC001    | NSX-T Controllers should be placed inside Management Network                                    | VCF SDDC Manager is deploying Controllers in Management Network.                             |                                                                         |
| NSXTC002    | NSX-T Controller Cluster will contain 3 Controllers VM.                                         | VCF SDDC Manager is deploying 3 Controllers and form a Cluster.                              |                                                                         |
| NSXTC003    | Controllers would be separated to different ESXi hosts inside Cluster using AntiAffinity rules. | To avoid any problems with “split brain” and provide high availability of the Control Plane. | vSphere Anti-Affinity rules should be created inside Management Domain. |

## Data Plane

The data plane consists of the NSX Virtual Switch, which is based on the vSphere Distributed Switch (VDS) with additional components to enable services. Kernel modules, user space agents, configuration files, and install scripts are packaged in VIBs and run within the hypervisor kernel to provide services such as distributed routing and logical firewall and to enable VXLAN bridging capabilities.

The NSX Virtual Switch (vDS-based) abstracts the physical network and provides access-level switching in the hypervisor. It is central to network virtualization because it enables logical networks that are independent of physical constructs, such as VLANs.

### Management Domain VDS

VMware Virtual Distributed Switch (VDS) provides a centralized interface from which you can configure, monitor and administer virtual machine access switching for the entire data center. It provides simplified virtual machine network configuration, enhanced monitoring and troubleshooting capabilities and support for advanced VMware vSphere networking features.

The VDS of the Management Domain is automatically created by the VCF Cloud Builder at initial setup of the VCS deployment. This VDS contains a number of the Distributed Port Groups (dPG) and VMKernel (VMK) interfaces as described in the "Management Domain ESXi" section. The VCS SDN design requires the following:

- The MTU of the VDS setting at 9000,
- Two VXLAN VMkernel adapters teamed using Route Based on originating virtual port.

VCS Management Domain VDS’s will be resilient from perspective of uplink failure and will utilize all bandwidth possible.

##### Management Cluster Teaming and Policy Decisions

|                                                                     | Management                                                                                          | vMotion                          | vSAN                             | VXLAN                                   | Replication                             |
|---------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|----------------------------------|----------------------------------|-----------------------------------------|-----------------------------------------|
| Security (Promiscuous mode, MAC address changes, Forged transmits.) | **Promiscuous mode:** Reject <br> **MAC address changes:** Accept <br> **Forged transmits:** Accept | All Reject                       | All Reject                       | All Reject                              | All Reject                              |
| Traffic Shaping                                                     | Disabled                                                                                            | Disabled                         | Disabled                         | Disabled                                | Disabled                                |
| VLAN Type                                                           | VLAN                                                                                                | VLAN                             | VLAN                             | VLAN                                    | VLAN                                    |
| Teaming                                                             | Route Based on physical NIC load                                                                    | Route Based on physical NIC load | Route Based on physical NIC load | Route Based on originating virtual port | Route Based on originating virtual port |
| Network failure detection                                           | Link status only                                                                                    | Link status only                 | Link status only                 | Link status only                        | Link status only                        |
| Failback                                                            | Yes                                                                                                 | Yes                              | Yes                              | Yes                                     | Yes                                     |
| Active Uplinks                                                      | uplink1, uplink2                                                                                    | uplink1, uplink2                 | uplink1, uplink2                 | uplink1, uplink2                        | uplink1, uplink2                        |
| Standby Uplinks                                                     | None                                                                                                | None                             | None                             | None                                    | None                                    |
| Unused Uplinks                                                      | None                                                                                                | None                             | None                             | None                                    | None                                    |
| Filtering & Marking                                                 | Disabled                                                                                            | Disabled                         | Disabled                         | Disabled                                | Disabled                                |
| MTU                                                                 | 1500                                                                                                | 9000                             | 9000                             | 9000                                    | 1500                                    |

##### Management Domain Virtual Switch Design Decisions

| Decision ID | Design Decision                                                                           | Design Justification                                                                     | Design Implication                                                                                                           |
|-------------|-------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
| MDVS001     | MTU of 9000 set at minimum on the VXLAN VMKernel Adapter.                                 | VXLAN transport requires an MTU of 1600 or higher. 9000 is considered the optimum value. | The physical switch ports must also be configured with the correct MTU value.                                                |
| MDVS002     | Two VXLAN VMKernel adapters will be teamed using Route Based on originating virtual port. | This value is set by VCF Cloud Builder as a default.                                     | This will cause that VMs connected to the VXLAN would be assigned to first vmnic where another VM to second vmnic and so on. |

#### Transport Zones - Management Domain

In NSX-T, virtual layer 2 domains are called Logical Switches (LS). Logical switches are defined in Transport Zone (TZ). A Transport Zone is a container that defines the potential reach of transport nodes. There are two types of the TZ:

- Overlay Transport Zone – it’s used to handle traffic between ESXi hosts within virtual environment.
- VLAN Transport Zone – it’ used to handle traffic which is exiting virtual environment.

The type of the logical switch is derived from the transport zone it belongs to.

During initial deployment of the Transport Zones is done in NSX-T. Three Transport Zones are created:

- Overlay Transport Zone - for all overlay networks (VXLANs)
- VLAN Transport Zone - for all VLAN based networks in Management Domain (except of uplink interfaces)
- Edge Uplink Transport Zone - for VLANs based networks used to connect T0 Logical Router with Physical Firewall

NSX-T is using Unicast Transport Zones only so there is no need to support IGMP or IGMP Snooping in physical infrastructure.

##### Design Decisions Transport Zones for Management Domain

| Decision ID | Design Decision                                                        | Design Justification                        | Design Implication |
|-------------|------------------------------------------------------------------------|---------------------------------------------|--------------------|
| NSXTTZ001   | Three Transport Zones would be created in NSX-T for Management Domain. | SDDC Manager is deploying those by default. |                    |

#### Transport Zones - Workload Domain

During initial deployment of the Transport Zones is done in NSX-T. Three Transport Zones are created:

- Overlay Transport Zone - for all overlay networks (VXLANs)
- VLAN Management Transport Zone - for Management VLAN based networks in Workload Domain (except of uplink interfaces)
- Edge Uplink Transport Zone - for VLANs based networks used to connect T0 Logical Router with Physical Customer Infrastructure

NSX-T is using Unicast Transport Zones only so there is no need to support IGMP or IGMP Snooping in physical infrastructure.

##### Design Decisions for NSX-T Transport Zones - Workload Domain

| Decision ID | Design Decision                                                                   | Design Justification                                                                                                                 | Design Implication                                                                                                                     |
|-------------|-----------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| TZNT001     | The NSX-T will use one Overlay Transport Zone.                                    | There is no need to deploy more than one Overlay Transport Zone.                                                                     | This TZ is used only to traverse traffic between ESXi’s/Edge Nodes within virtual environment.                                         |
| TZNT002     | The NSX-T will use one Management VLAN Transport Zone.                            | This Transport zone is used only for VMK traffic to the ESXi’s.                                                                      | Traffic between transport zones are available only via Underlay network. Which allows to protect those networks via SRX or another FW. |
| TZNT003     | The NSX-T will use Workload VLAN Transport Zone (one per each tenant – Edge Node) | There is no need to use more than one VLAN Transport Zone due to fact that there is no more than just one Uplink VLAN per Edge Node. | This simplify the design and customer requirements.                                                                                    |

#### ESXi

Management and Workload Domains are using VCF Ready Nodes as a compute resource. They are prepared by Cloud Builder. Each ESXi will be configured with VMKernel NICs for the following services only:

##### VMKernel NICs services

| Service         | Description                                                 | Primary NIC | Secondary NIC |
|-----------------|-------------------------------------------------------------|-------------|---------------|
| ESXi Management | Used to manage the ESXi host itself                         | vmNIC0      | vmNIC1        |
| vMotion         | Used for managing workload between ESXi hosts within a Rack | vmNIC0      | vmNIC1        |
| vSAN            | Used to provide a Virtual Storage System within a Rack      | vmNIC0      | vmNIC1        |
| SDN             | Two VMkernel ports are used to handle SDN overlay traffic   | vmNIC0      | vmNIC1        |

Detailed information about ESXi physical NICs assignment can be found in lldInfrastructure.md LLD.

#### Transport Nodes for NSX-T

Transport Nodes are hypervisor hosts and NSX Edges that will take a part in NSX-T overlay.

Transport Node can belong to:

- Multiple VLAN transport zones
- One overlay transport zone with a standard N-VDS
- Multiple overlay transport zones with advanced Datapath N-VDS if the  transport node is running on an ESXi host

VCS Deployment uses Transport Nodes which are connected to one overlay TZ, and multiple VLAN Transport Zones. Each of ESXi hosts are by default prepared used SDDC Manager and no other configuration is required. ESXi Transport Nodes are connected to both Transport Zones Overlay and Management VLAN. Edge Nodes are connected to both Overlay and Uplink VLAN Transport Zones.

Below figure presents how Transport Nodes are divided between Transport Zones.

##### Transport Nodes and Transport Zones

![transport zones](images/lldSoftwareDefinedNetworks/transportZones.png)

Such design provides necessary separation between External Customer VLANs and internal VMkernel Interfaces used by ESXi Hosts.

#### Edge Transport Node

Host Transport Node is a node that is capable of participating in an NSX-T overlay or NSX-T VLAN networking. All Edge Nodes for workload domain become Edge Transport Nodes. Both Edge Nodes should be configured as Edge Transport Nodes. Edge Transport Nodes are as well place where all stateful services are organized, like:

- Connectivity to physical infrastructure
- NAT
- DHCP server
- MetaData Proxy
- Edge Firewall
- Load Balancer

Edge Nodes are the VMs taking responsibility for all stateful services in NSX-T Infrastructure. Each Edge Node should be setup with the following configuration:

##### Configuration of the NSX-T Edge Node - Management Domain

| Parameter            | Value                                                 | Justification/Description                                                                                                                        |
|----------------------|-------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| Name                 | < name >                                              | Name of the Edge Node                                                                                                                            |
| Host name/FQDN       | < FQDN >                                              | FQDN name of the Edge Node                                                                                                                       |
| Form Factor          | Medium <br> (4vCPU <br> 8 GB RAM <br> 200 GB Storage) | Medium Form Factor is enough for most of the deployments, and it’s having option to define multiple Load Balancers (up to 10 small or 1 medium). |
| Compute Manager      | < VCS for Management domain >                         | vCenter Server responsible for Management Domain.                                                                                                |
| Cluster              | < MGMT Cluster >                                      | Cluster connected to the vCenter Server (just one should be available).                                                                          |
| Resource Pool        | Optional                                              |                                                                                                                                                  |
| Host                 | Optional                                              |                                                                                                                                                  |
| Datastore            | < Datastore >                                         | Datastore dedicated for this workload domain.                                                                                                    |
| IP Assignment        | Static                                                | Static IP assignment is giving more control and limit number of the components which are prerequisites for the build.                            |
| Management IP        | < IP Address >                                        | IP address of the management interface of the Edge Node. Input form of the SDDC Manager contain this information.                                |
| Default Gateway      | < Default GW >                                        | Default Gateway for the management network (Physical Firewall interface)                                                                         |
| Management Interface | < Management Network Port Group >                     | Management Interface from VCS to communicate with Management Network.                                                                            |
| Datapath Interfaces  | < 1st network >                                       | Uplink VLAN 1 network                                                                                                                            |
|                      | < 2nd network >                                       | Uplink VLAN 2 network                                                                                                                            |
|                      | < 3rd network >                                       | Not Used                                                                                                                                         |

##### Edge Node NIC assignment Management Domain

![edge node interfaces](images/lldSoftwareDefinedNetworks/edgeNodeInterfacesManagementDomain.png)

##### Configuration of the NSX-T Edge Node - Workload Domain

| Parameter            | Value                                                 | Justification/Description                                                                                                                       |
|----------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|
| Name                 | < name >                                              | Name of the Edge Node                                                                                                                           |
| Host name/FQDN       | < FQDN >                                              | FQDN name of the Edge Node                                                                                                                      |
| Form Factor          | Large <br> (8vCPU <br> 32 GB RAM <br> 200 GB Storage) | Large Form Factor is enough for most of the deployments, and it’s having option to define multiple Load Balancers (up to 40 small or 4 medium). |
| Compute Manager      | < VCS for Workload domain >                           | vCenter Server responsible for Workload Domain.                                                                                                 |
| Cluster              | < CMP Cluster >                                       | Cluster connected to the vCenter Server (just one should be available).                                                                         |
| Resource Pool        | Optional                                              |                                                                                                                                                 |
| Host                 | Optional                                              |                                                                                                                                                 |
| Datastore            | < Datastore >                                         | Datastore dedicated for this workload domain.                                                                                                   |
| IP Assignment        | Static                                                | Static IP assignment is giving more control and limit number of the components which are prerequisites for the build.                           |
| Management IP        | < IP Address >                                        | IP address of the management interface of the Edge Node.                                                                                        |
| Default Gateway      | < Default GW >                                        | Default Gateway for the management network (Physical Firewall interface)                                                                        |
| Management Interface | < Management Network Port Group >                     | Management Interface from VCS to communicate with Management Network.                                                                           |
| Datapath Interfaces  | < 1st network >                                       | Customer EXT VLAN network                                                                                                                       |
|                      | < 2nd network >                                       | Underlay VLAN network                                                                                                                           |
|                      | < 3rd network >                                       | Not Used                                                                                                                                        |

##### Edge Node NIC assignment Workload Domain

![edge node interfaces](images/lldSoftwareDefinedNetworks/edgeNodeInterfacesWorkloadDomain.png)

##### Backup of the Edge Transport Nodes

NSX-T Edge Transport Node are not backed up, as NSX-T Automated Backup does not store Edge information, it is not possible to use NSX-T native functions to backup Edge configuration, also each NSX-T Edge configuration has the exact same configuration. In a case of Edge failure, manual intervention including Edge recreation will be needed.

##### Design Decisions for NSX-T Edge Nodes

| Decision ID | Design Decision                                                                                                                                    | Design Justification                                                                                                                                                                                                                                              | Design Implication                                                                       |
|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
| EDGN001     | Size of the Edge Nodes should be Medium in Management Domain and Large in Workload Domain.                                                         | Medium Form Factor is used in Management domain only for LoadBalancer used for vRealize suite. In Workload Domain Large Form Factor is enough for most of the deployments, and it’s having option to define multiple Load Balancers (up to 40 small or 4 medium). | Deployment in Workload Domain will take much more resources, but it’s a calculated risk. |
| EDGN002     | Two Edge Nodes should be deployed per each tenant for the customer (workload domain). Two Edge Nodes should be deployed for all Management Domain. | Complete separation of the workload domain is bringing all necessary security. Due to fact that Management Domain is having separate NSX-T Manager, Edge Nodes should be present there as well.                                                                   | N/A                                                                                      |
| EDGN003     | Edge Nodes in Workload Domain would use one uplink to the Customer VLAN                                                                            | In case of using BGP there is no need to provide alternate VLANs like for OSPF. BGP is capable to recover neighbourship fast enough                                                                                                                               | Only one VLAN would connect with Customer infrastructure.                                |
| EDGN004     | Edge Nodes in Management Domain would use two uplinks to Physical Firewall                                                                         | Due to fact that VCF is responsible for building whole Management Domain, there is a need to use two uplinks within two different VLANs.                                                                                                                          |                                                                                          |
| EDGN005     | Edge VM will not be backup                                                                                                                         | Edge configuration is stored on NSX-T not Edge VM                                                                                                                                                                                                                 | Day-two playbook creating Edge VM will be needed                                         |

#### Edge Cluster

The NSX-T Edge cluster is a logical grouping of NSX-T Edge virtual machines that helps to ensure at least one NSX Edge Node is always available. These NSX-T Edge virtual machines run in the vSphere shared edge and compute cluster and provide North-South routing for the workloads in the compute clusters.

Backup for Edge Clusters configuration will be performed using Ansible playbook.

##### Configuration of the NSX-T Edge Cluster - Management Domain

| Parameter            | Value                                      | Justification/Description                                                                     |
|----------------------|--------------------------------------------|-----------------------------------------------------------------------------------------------|
| Name                 | < name >                                   | Name of the Edge Node Cluster                                                                 |
| Edge Cluster Profile | nsx-default-edge-high-availability-profile | Using default Edge Cluster profile guarantees high availability mechanism appropriate timing. |
| Transport Nodes      | < Edge Transport Nodes >                   | Both Edge Transport Nodes should be part of the cluster to bring HA mechanism.                |

##### Configuration of the NSX-T Edge Cluster - Workload Domain

| Parameter            | Value                                      | Justification/Description                                                                     |
|----------------------|--------------------------------------------|-----------------------------------------------------------------------------------------------|
| Name                 | < name >                                   | Name of the Edge Node Cluster                                                                 |
| Edge Cluster Profile | nsx-default-edge-high-availability-profile | Using default Edge Cluster profile guarantees high availability mechanism appropriate timing. |
| Transport Nodes      | < Edge Transport Nodes >                   | Both Edge Transport Nodes should be part of the cluster to bring HA mechanism.                |

##### Design Decisions for NSX-T Edge Node Cluster

| Decision ID | Design Decision                                       | Design Justification                                                                                                                                                    | Design Implication                         |
|-------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------|
| EDNC001     | Default Edge Cluster Profile would be used.           | Edge Cluster Profile is defining probing time between Edge Nodes inside cluster. Default values are providing necessary parameters to keep Edge Cluster High Available. | N/A                                        |
| EDNC002     | Each Edge Node Cluster should contain two Edge Nodes. | There is no need to deploy more than two Edge Nodes based on our initial requirements.                                                                                  | N/A                                        |
| EDNC003     | Edge Cluster configuration should be backed up        | Backup in a case of lost configuration                                                                                                                                  | Ansible playbook for backup will be needed |

### Uplink profiles in NSX-T - Management Domain

​An uplink profile defines policies for the links from hypervisor hosts to NSX-T logical switches or from NSX-T Edge nodes to top-of-rack switches. ​Uplink profiles allow you to consistently configure identical capabilities for network adapters across multiple hosts or nodes. VCF and standard build is using multiple Uplink profiles, there is no need to describe configuration of the default ones used by ESXi hosts, due to fact that they are present by default and they are configured correctly.

##### Edge Uplink Profile in NSX-T - Management Domain

| Parameter      | Value                                                                                                                | Justification/Description                                          |
|----------------|----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|
| Name           | < Edge Uplink Profile Name >                                                                                         | Name for Edge Uplink Profile                                       |
| Description    | < Description of the Edge Uplink >                                                                                   |                                                                    |
| LAGs           | N/A                                                                                                                  |                                                                    |
| Teamings       | uplink1-named-teaming-policy: Failover Order: uplink-1  <br>  uplink2-named-teaming-policy: Failover Order: uplink-2 |                                                                    |
| Transport VLAN | < Edge VTEP VLAN >                                                                                                   | VLAN dedicated for Edge VTEPs, different than ESXi's VTEP network. |
| MTU            | 9000                                                                                                                 |                                                                    |

##### Edge Uplink Profile in NSX-T - Workload Domain

| Parameter      | Value                              | Justification/Description                                           |
|----------------|------------------------------------|---------------------------------------------------------------------|
| Name           | < Edge Uplink Profile Name >       | Name for Edge Uplink Profile                                        |
| Description    | < Description of the Edge Uplink > |                                                                     |
| LAGs           | N/A                                |                                                                     |
| Teamings       | Failover Order: uplink-1           | Just one interface is needed (it's VM anyway)                       |
| Transport VLAN | < Edge VTEP VLAN >                 | VLAN dedicated for Edge VTEPs, different than ESXi's VTEP network\. |
| MTU            | 9000                               |                                                                     |

##### General configuration of the NSX-T Edge Transport Node - Management Domain

| Parameter       | Value                             | Justification/Description                                                                                                                                                              |
|-----------------|-----------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Name            | < name >                          | Name of the Edge Transport Node                                                                                                                                                        |
| Node            | < Edge Node Name >                | Name of the Edge Nodes.                                                                                                                                                                |
| Transport Zones | < Overlay TZ > < Edge Uplink TZ > | Each Edge Transport Node should have access to the Edge Uplink TZ to reach Physical Firewall and to the Overlay TZ Transport zone to provide communication within Virtual Environment. |

##### General configuration of the NSX-T Edge Transport Node - Workload Domain

| Parameter       | Value                               | Justification/Description                                                                                                                                                               |
|-----------------|-------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Name            | < name >                            | Name of the Edge Transport Node                                                                                                                                                         |
| Node            | < Edge Node Name >                  | Name of the Edge Nodes.                                                                                                                                                                 |
| Transport Zones | < Overlay TZ > < Workload VLAN TZ > | Each Edge Transport Node should have access to the Workload VLAN TZ to reach customer subnets and to the Overlay TZ Transport zone to provide communication within Virtual Environment. |

##### VDS configuration of the NSX-T Edge Transport Node - Management Domain

| Parameter             | Value                                                                                       | Justification/Description                                                                               |
|-----------------------|---------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| N-VDS Type            | Standard                                                                                    |                                                                                                         |
| Edge Switch Name      | SDDC-DPortGroup-Edge                                                                        | Name defined by SDDC Manager                                                                            |
| NIOC Profile          | N/A                                                                                         | There is no need to change default behavior of the NIOC. Default values are enough for VCS deployments. |
| Uplink Profile (Name) | < Uplink Profile Name >                                                                     | Name for Uplink profile defined by SDDC Manager                                                         |
| IP Assignment         | Use Static IP List                                                                          |                                                                                                         |
| IP Pool               | < IP addresses defined in SDDC Manager intake form as "Edge Overlay IP Address XX Node X" > | Value configured by a SDDC Manager                                                                      |
| Gateway               | < GW IP address for Edge VTEP Subnet >                                                      | Value taken from SDDC Manager intake form                                                               |
| Subnet Mask           | < Subnet Mask for Edge VTEP Subnet >                                                        | Value taken from SDDC Manager intake form                                                               |
| Virtual NICs          | fp-eth0 uplink-1 <br> fp-eth1 uplink-2                                                      | Fp-eth0 and 1 should be uplink toward Physical Firewall                                                 |

##### Workload VLAN N-VDS configuration of the NSX-T Edge Transport Node - Workload Domain

| Parameter                            | Value                         | Justification/Description                                                                               |
|--------------------------------------|-------------------------------|---------------------------------------------------------------------------------------------------------|
| N-VDS Type                           | Standard                      |                                                                                                         |
| Edge Switch Name                     | < Name for Workload VLAN TZ > |                                                                                                         |
| NIOC Profile                         | N/A                           | There is no need to change default behavior of the NIOC. Default values are enough for VCS deployments. |
| Uplink Profile (Name)                | < Uplink Profile Name >       | Name for Uplink profile for customer VLAN                                                               |
| Uplink Profile (LAGs)                | N/A                           | Edge Node is containing just one uplink and it’s not possible to LAG another interface.                 |
| Uplink Profile (Teaming)             | N/A                           | Edge Node is containing just one uplink and it’s not possible to LAG another interface.                 |
| Uplink Profile (Transportation VLAN) | < Customer EXT1 VLAN >        | Customer Provided VLAN to reach Customer Routers (refer to the "SDN Workload Domain" figure)            |
| Uplink Profile (MTU)                 | 1600                          | MTU 1600 is necessary to provide proper communication between physical and virtual infrastructure.      |
| IP Assignment                        | N/A                           |                                                                                                         |
| IP Pool                              | N/A                           |                                                                                                         |
| Virtual NICs                         | fp-eth0 uplink-1              | Fp-eth0 should be uplink toward Customer Routers                                                        |

##### Overlay N-VDS configuration of the NSX-T Edge Transport Node - Workload Domain

| Parameter                            | Value                               | Justification/Description                                                                               |
|--------------------------------------|-------------------------------------|---------------------------------------------------------------------------------------------------------|
| N-VDS Type                           | Standard                            |                                                                                                         |
| Edge Switch Name                     | < Name for Overlay TZ N-VDS >       |                                                                                                         |
| NIOC Profile                         | nsx-default-nioc-hostswitch-profile | There is no need to change default behavior of the NIOC. Default values are enough for VCS deployments. |
| Uplink Profile (Name)                | < Host Uplink Profile name >        | Name for Host uplink Profile.                                                                           |
| Uplink Profile (LAGs)                | N/A                                 |                                                                                                         |
| Uplink Profile (Teaming)             | N/A                                 |                                                                                                         |
| Uplink Profile (Transportation VLAN) | < Edge VLAN >                       | Edge VLAN number                                                                                        |
| Uplink Profile (MTU)                 | 9000                                | Overlay Transportation require much more headers to carry, Jumbo Frames are required.                   |
| IP Assignment                        | < Static IP Pool >                  | Static Pool will be used, assigning IP addresses from Edge Vlan range                                   |
| Virtual NICs                         | fp-eth1 uplink-1                    | Fp-eth1 should be used for Overlay Network                                                              |

##### Design Decisions for NSX-T Edge Transport Nodes

| Decision ID | Design Decision                                                                                                                                                          | Design Justification                                                                                                                                                                                           | Design Implication                                                                                                                                                                                                                                  |
|-------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| EDTN001     | Workload Edge Transport Node would be configured with two nVDSes                                                                                                         | Edge Transport Node should have an access to Overlay N-VDS to be able to communicate with ESXes, and in Workload VLAN N-VDS to reach Customer WAN infrastructure and provide this access to T0 Logical Router. | N/A                                                                                                                                                                                                                                                 |
| EDTN002     | Workload Edge Transport Node for Workload VLAN N-VDS will use Uplink Profile generated in section Uplink Profile, to address communication with physical infrastructure. | Due to fact that VCF limitation to just 2 uplinks, there is a need to use Logical Switch in Trunk Mode as an platform for traffic coming from Edge Transport Node and Customer WAN physical network.           | Due to fact that Logical Switch in Trunk Mode is not located in Workload VLAN Transport Zone there is a bridge between Customer Traffic and Management Traffic, but proper isolation would protect environment from potential issues with security. |

### Host Transport Node

Host Transport Node is a node that is capable of participating in a NSX-T overlay and NSX-T VLAN networking. All ESXi hosts inside workload domain become Host Transport Nodes. Host Transport Nodes need to have communication with Edge Transport Nodes using VXLAN networkstack. IP addresses for VMkernel inside VXLAN networkstack will be assigned by DHCP server from vTEP VLAN range. DHCP server inside VCS will be configured as cluster on both Active Directory Virtual Machine Servers. A traffic between vTEP VLAN and Edge VLAN will travel through Physical Firewall it needs to be allowed on it.

##### General configuration of the NSX-T Host Transport Node

| Parameter       | Value                                 | Justification/Description                                                                                                                                                                   |
|-----------------|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Name            | < name >                              | Name of the Host Transport Node                                                                                                                                                             |
| Node            | < ESXi Name >                         | Name of the ESXi host.                                                                                                                                                                      |
| Transport Zones | < Overlay TZ > < Management VLAN TZ > | Each Host Transport Node should have access to the Management VLAN TZ to reach management subnets and to the Overlay TZ Transport zone to provide communication within Virtual Environment. |

##### VDS configuration of the NSX-T Host Transport Node

| Parameter                            | Value                                                      | Justification/Description                                                                                                                                |
|--------------------------------------|------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Type                                 | VDS                                                        | VCF 4.0 introduces possibility to support VDSes, so thats what is configured by SDDC Manager                                                             |
| Mode                                 | Standard                                                   |                                                                                                                                                          |
| Edge Switch Name                     | < Name for Overlay TZ N-VDS >                              |                                                                                                                                                          |
| NIOC Profile                         | nsx-default-nioc-hostswitch-profile                        | There is no need to change default behavior of the NIOC. Default values are enough for VCS deployments.                                                  |
| Uplink Profile (Name)                | < Name for Host Uplink profile >                           | Predefined by VCF                                                                                                                                        |
| Uplink Profile (LAGs)                | N/A                                                        |                                                                                                                                                          |
| Uplink Profile (Teaming)             | Name: Default Teaming Teaming Policy: Load Balance Source, | Predefined by VCF                                                                                                                                        |
|                                      | Active uplinks: uplink-1, uplink-2                         |                                                                                                                                                          |
| Uplink Profile (Transportation VLAN) | < Overlay VLAN >                                           | Overlay VLAN number                                                                                                                                      |
| Uplink Profile (MTU)                 | 9000                                                       | Overlay Transportation require much more headers to carry, Jumbo Frames are required.                                                                    |
| IP Assignment                        | Use DHCP                                                   | DHCP server should be configured on both Active Directory Servers in cluster and connected directly to this subnet. DHCP relay option shouldn't be used. |
| Virtual NICs                         | Vmnic0 uplink-1 Vmnic1 uplink-2                            |                                                                                                                                                          |

##### Design Decisions for NSX-T Host Transport Node

| Decision ID | Design Decision                                                                   | Design Justification                                                                                                                                              | Design Implication |
|-------------|-----------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| HOTN001     | Host Transport Node would be connected to both Overlay TZ and Management VLAN TZ. | This is how VCF is preparing hosts, VVD is defining builds in this way. In addition, ESXi’s are not taking part in any other traffic than Overlay and Management. | N/A                |
| HOTN002     | Host is participating in Overlay TZ by using both uplinks.                        | Both uplinks should be part of the Overlay Transport Zone due to fact that this is main type of traffic generated by ESXi hosts.                                  | N/A                |
| HOTN003     | IP Assignment is done by using DHCP                                               | External DHCP is required to address vTEPs, due to fact that this is how VCF is preparing Host Transport Nodes.                                                   | N/A                |

### Logical Switches

The logical switching capability in the NSX-T platform provides the ability to spin up isolated logical L2 networks with the same flexibility and agility that exists for virtual machines. A logical switch provides a representation of Layer 2 switched connectivity across many hosts with Layer 3 IP reachability between them. When logical switches are attached to transport zones, it connects to the VDS/N-VDS for networking.

When Logical Switches are created, they are assigned a VNI from the pool defined by VCF.

Each Logical Switch might use various Switching Profiles which will provide additional configuration for those Logical Switches.

In NSX-T Logical Switches are called Segments.

Logical Switches configured in VCS under building phase are as follows (All switches will have Replication mode set to Hierarchical Two-Tier replication, and Segment Profiles set to default )

##### Logical Switches created during build Phase

| Segment Name                                               | Connectivity                    | Transport Zone                     | Subnets                    | Admin State | VLAN              | Domain     | Description                                                              |
|------------------------------------------------------------|---------------------------------|------------------------------------|----------------------------|-------------|-------------------|------------|--------------------------------------------------------------------------|
| < location code >lsw101                                    | N/A                             | nsx-vlan-transportzone             | Not Set                    | UP          | 0-4094            | Workload   | Trunk used by Edge Transport node to reach physical network              |
| < location code >lsw001                                    | < location code >lrt001         | < location code >vtz001            | Not Set                    | UP          | 200               | Workload   | Customer Uplink from T0 LR to Customer Router - parameter can be changed |
| < Region A Segment >                                       | < location code >ecn101-t1-gw01 | < location code >-m01-tz-overlay01 | < Region A GW IP address > | UP          | Not Set           | Management | Region-specific virtual network not designed to fail over                |
| < xRegion Segment >                                        | < location code >ecn101-t1-gw01 | < location code >-m01-tz-overlay01 | < xRegion GW IP address >  | UP          | Not Set           | Management | Cross-Region virtual network designed to fail over                       |
| < location code >lsw002                                    | < location code >lrt101         | overlay-tz-< NSX-T FQDN >          | Not Set                    | UP          | Not Set           | Workload   | Demo build of Web Tier Network                                           |
| < location code >lsw003                                    | < location code >lrt101         | overlay-tz-< NSX-T FQDN >          | Not Set                    | UP          | Not Set           | Workload   | Demo build of App Tier Network                                           |
| < location code >lsw004                                    | < location code >lrt101         | overlay-tz-< NSX-T FQDN >          | Not Set                    | UP          | Not Set           | Workload   | Demo build of Db Tier Network                                            |
| VCF-edge_< location code >ecn101_segment_< Uplink VLAN 1 > | N/A                             | sfo01-m01-edge-uplink-tz           | Not Set                    | UP          | < Uplink VLAN 1 > | Management | Edge Uplink 1 network for Edge VM                                        |
| VCF-edge_< location code >ecn101_segment_< Uplink VLAN 2 > | N/A                             | sfo01-m01-edge-uplink-tz           | Not Set                    | UP          | < Uplink VLAN 2 > | Management | Edge Uplink 2 network for Edge VM                                        |

All NSX-T OTZ segments (logical switches) are created with default segment profiles, for < Region A Segment > and < xRegion Segment >  default behavior needs to be changed. Please see table below.

| Segment Name         | IP Discovery             | MAC Discovery             | Spoof Guard                | Segment Security                 |
|----------------------|--------------------------|---------------------------|----------------------------|----------------------------------|
| < Region A Segment > | dhc-ip-discovery-profile | dhc-mac-discovery-profile | default-spoofguard-profile | default-segment-security-profile |
| < xRegion Segment >  | dhc-ip-discovery-profile | dhc-mac-discovery-profile | default-spoofguard-profile | default-segment-security-profile |

**dhc-ip-discovery-profile settings:**

Duplicate IP Detection: **Disabled**  
ARP Snooping: **Enabled**  
ARP Binding Limit: **1**  
ND Snooping: **Disabled**  
ND Snooping Limit: **1**  
ARP ND Binding Limit Timeout: **10**  
Trust on First Use: **Disabled**  

DHCP Snooping: **Enabled**  
DHCP Snooping - IPv6: **Enabled**  
VMware Tools: **Enabled**  
VMware Tools - IPv6: **Enabled**  

**dhc-mac-discovery-profile settings:**

MAC Change: **Enabled**  
MAC Learning Aging Time: **600**  
MAC Learning: **Enabled**  
MAC Limit: **4096**  
MAC Limit Policy: **Allow**  
Unknown Unicast Flooding: **Disabled**

##### Design Decisions for NSX-T Logical Switches

| Decision ID | Design Decision                                                  | Design Justification                                                                                                                                                 | Design Implication |
|-------------|------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| LOGS001     | All Logical Switches are using Hierarchical Two-Tier replication | Hierarchical two-tier replication is more efficient by reducing the number of ESXi hosts the source ESXi host must replicate traffic to.                             | N/A                |
| LOGS002     | All Logical Switches are using default Switching Profiles        | There is no initial requirement to use different Switching Profiles than default. In case of the Business requirement, required Switching Profiles might be created. | N/A                |

### Logical Routers

Logical Routers provides North-South connectivity between virtual customer workload and outside of VCS networks, as well as East-West connectivity between different networks within the same tenant.

VCS supports two-tier router topology which provides Tier0 Logical Router as a top-tier layer and Tier1 Logical Router as a bottom-tier layer. This construct provides control over both layers services and policies and necessary separation of the tenants.

Single T1/T0 NSX-T router is both IPv4 and IPv6 capable, by VCS design.

##### Service Router and Distributed Router concept

![logical routers](images/lldSoftwareDefinedNetworks/logicalRouterLogic.png)

Above figure presents a concept of the Logical Router with Service Router and Distributed Router and how those are connected to each other. Service Router is used to handle all services (like Load Balancing, Firewalling, Uplink, etc.) where Distributed Router is represented on each ESXi inside Kernel as a process where its responsible for the routing decisions.

#### T0 Logical Router and T0 VRF

Tier 0 Logical Routers and T0 VRF provide communication with the physical networks, here as well dynamic routing protocol (BGP) would be configured to exchange necessary routes with customer physical network. Other side of the T0 Logical Router or T0 VRF would be used to connect T1 Logical Routers and receive routing information from them.

T0 Logical Router and T0 VRF provides default information about routing for the bottom-tier layer instead of each route learned from physical network.

In normal VCS builds there would be just single T0 Logical Router deployed in Management Domain from Cloud Builder as part of vCF stack. Role of this T0 will be connecting T1 Logical Router and Physical Firewall using BGP protocol, providing connectivity to and from ACN where all vRSLCM components are deployed. For Workload Domain there will be deployed at least one T0 Logical Router, hovewer role of T0 Logical Router in Workload Domain will be different than in Management Domain, T0 Logical Router will not provide connectivity it will be configuration template to configure BGP protocol and interfaces on Edge Cluster. Traffic will be handled by T0 VRFs, one T0 VRF will be configured per tenant. T0 VRF will perform the same functions as |T0 Logical Router in Management Domain, hovewer BGP protocol configuration will be shared across all T0 VRFs hosted on single Edge Cluster.

##### General Configuration of the NSX-T T0 Logical Router - Management Domain

| Parameter                  | Value                         | Justification/Description                                                                                                                                                                    |
|----------------------------|-------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tier-0 Router Name         | < Edge Cluster Name >-t0-gw01 | T0 Generated by SDDC Manager                                                                                                                                                                 |
| Description                | < Description >               |                                                                                                                                                                                              |
| Edge Cluster               | < Edge Cluster Name >         |                                                                                                                                                                                              |
| High Availability Mode     | Active-Active                 | High Availability mode is set to default Active-Active, which allows to coexist two Edge Nodes with ECMP enabled.                                                                            |
| Failover Mode              | N/A                           |                                                                                                                                                                                              |
| Intra Tier0 transit subnet | optional                      | There is no need to change this value from default (169.254.0.0/28), due to fact that this is generated automatically network which is isolated with no risk that it would be redistributed. |
| Tier0-Tier1 transit subnet | optional                      | There is no need to change this value from default (100.64.0.0/16), due to fact that this is generated automatically network which is isolated with no risk that it would be redistributed.  |

##### Interfaces configuration of the NSX-T Tier 0 Logical Router - Management Domain

| Int | Name                                                                                           | Type        | MTU  | Transport Node              | URPF mode | Logical Switch                                    | Logical Switch Port       | IP Address/mask                      |
|-----|------------------------------------------------------------------------------------------------|-------------|------|-----------------------------|-----------|---------------------------------------------------|---------------------------|--------------------------------------|
| 1   | < edge 1 name >_VCF-edge_< LR T0 name >_segment_< Uplink VLAN 1 >_< IP address of the uplink > | Uplink      | 9000 | < Uplink 1 Transport Node > | Strict    | VCF-edge_< LR T0 Name >_segment_< Uplink VLAN 1 > | Attach to new switch port | < Uplink 1 IP address 1 >/< Prefix > |
| 2   | < edge 1 name >_VCF-edge_< LR T0 name >_segment_< Uplink VLAN 2 >_< IP address of the uplink > | Uplink      | 9000 | < Uplink 2 Transport Node > | Strict    | VCF-edge_< LR T0 Name >_segment_< Uplink VLAN 2 > | Attach to new switch port | < Uplink 2 IP address 1 >/< Prefix > |
| 3   | < edge 2 name >_VCF-edge_< LR T0 name >_segment_< Uplink VLAN 1 >_< IP address of the uplink > | Uplink      | 9000 | < Uplink 1 Transport Node > | Strict    | VCF-edge_< LR T0 Name >_segment_< Uplink VLAN 1 > | Attach to new switch port | < Uplink 1 IP address 2 >/< Prefix > |
| 4   | < edge 2 name >_VCF-edge_< LR T0 name >_segment_< Uplink VLAN 2 >_< IP address of the uplink > | Uplink      | 9000 | < Uplink 2 Transport Node > | Strict    | VCF-edge_< LR T0 Name >_segment_< Uplink VLAN 2 > | Attach to new switch port | < Uplink 2 IP address 2 >/< Prefix > |
| 5   | LinkedPort_< T1_name >                                                                         | Linked Port | \-   | \-                          | \-        | \-                                                | \-                        | \-                                   |

Linked Ports would be created automatically for each T1 Logical Router connected to this T0 Logical Router within domain. Uplink interface is an entry to the Physical infrastructure. T0 Logical Router is capable to do firewalling decisions on uplink interface. This feature is described inside section "Logical Router Firewall". T0 Logical Router have option to share routes with physical routers using dynamic routing protocol – BGP. BGP is only supported by VCS dynamic routing protocol.

##### BGP Configuration on NSX-T T0 Logical Router - Management Domain

| Parameter                       | Value                                  | Justification/Description                                                                                                                                                                                     |
|---------------------------------|----------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Status                          | Enabled                                |                                                                                                                                                                                                               |
| ECMP                            | Enabled                                |                                                                                                                                                                                                               |
| Graceful Restart                | Graceful Restart And Helper            |                                                                                                                                                                                                               |
| Local AS                        | < Local AS Number >                    | Autonomous System number should be received from Customer. By default, would be used from private pool 65000-65399. In case of need can be changed.                                                           |
| Route Aggregation               | None                                   | There is no need to aggregate any routes by default but might be changed in case of Business request.                                                                                                         |
| Neighbors 1 (Neighbour Address) | < IP address of the neighbour router > | IP address of the Customer Router from Customer Ext VLAN network.                                                                                                                                             |
| Neighbors 1 (Description)       | < Description >                        |                                                                                                                                                                                                               |
| Neighbors 1 (Maximum Hop Limit) | 1                                      | By default, there is no need to increase this value, because VCS is assuming that Customer Router is directly connected to the dedicated VLAN network where T0 Router is existing.                            |
| Neighbors 1 (Remote AS)         | < Remote AS Number >                   | Autonomous System Number of the Customer Router. Should be different than Local AS Number to configure eBGP.                                                                                                  |
| Neighbors 1 (Keep Alive Time)   | 4                                      |                                                                                                                                                                                                               |
| Neighbors 1 (Hold Down Time)    | 12                                     |                                                                                                                                                                                                               |
| Neighbors 1 (Source Address 1)  | < Uplink 1 IP address 1 of T0 >        |                                                                                                                                                                                                               |
| Neighbors 1 (Source Address 2)  | < Uplink 1 IP address 2 of T0 >        |                                                                                                                                                                                                               |
| Neighbors 1 (Password)          | Optional                               | Optional Password to protect BGP from creation neighbourship with incorrect router.                                                                                                                           |
| Neighbors 2 (Neighbour Address) | < IP address of the neighbour router > | IP address of the Customer Router from Customer Ext VLAN network.                                                                                                                                             |
| Neighbors 2 (Description)       | < Description >                        |                                                                                                                                                                                                               |
| Neighbors 2 (Maximum Hop Limit) | 1                                      | By default, there is no need to increase this value, because VCS is assuming that Customer Router is directly connected to the dedicated VLAN network where T0 Router is existing.                            |
| Neighbors 2 (Remote AS)         | < Remote AS Number >                   | Autonomous System Number of the Customer Router. Should be different than Local AS Number to configure eBGP.                                                                                                  |
| Neighbors 2 (Keep Alive Time)   | 4                                      |                                                                                                                                                                                                               |
| Neighbors 2 (Hold Down Time)    | 12                                     |                                                                                                                                                                                                               |
| Neighbors 2 (Source Address 1)  | < Uplink 2 IP address 1 of T0 >        |                                                                                                                                                                                                               |
| Neighbors 2 (Source Address 2)  | < Uplink 2 IP address 2 of T0 >        |                                                                                                                                                                                                               |
| Neighbors 2 (Password)          | Optional                               | Optional Password to protect BGP from creation neighbourship with incorrect router.                                                                                                                           |
| Address Families                | None                                   | By default, Address Families don’t have to be created due to fact that there are no Business requirements to filter routes advertised or learned to/from Customer Router. Can be adjusted if there is a need. |
| BFD Configuration               | Disabled                               | In case that BFD is supported by Psychical infrastructure, it’s recommended to used BFD to enhance performance of the BGP protocol. By default - disabled.                                                    |

IP Prefix Lists, Community Lists and Route Maps are not part of default build of the VCS but can be easily used to filter prefixes distributed or learned to/from neighbours. Route Redistribution is used to add routes from particular source to the routing table of the Logical Router.

##### Redistribution on NSX-T T0 Logical Router - Management Domain

| Parameter                                           | Value                                                                  | Justification/Description |
|-----------------------------------------------------|------------------------------------------------------------------------|---------------------------|
| Route Redistribution                                | Enabled                                                                |                           |
| Redistribution Criteria (Name)                      | default                                                                | Name defined by VCF       |
| Redistribution Criteria (Description)               | < Description >                                                        |                           |
| Redistribution Criteria (Tier-0 Subnets)            | Static Routes, IPSec Local IP, Connected, NAT IP, DNS Forwarder IP     | List defined by VCF       |
| Redistribution Criteria (Advertised Tier-1 Subnets) | DNS Forwarder IP, LB VIP, LB SNAT IP, Connected, Static Routes, NAT IP | List defined by VCF       |
| Route Map                                           | None                                                                   |                           |

##### General Configuration of the NSX-T T0 Logical Router - Workload Domain

| Parameter                  | Value                                 | Justification/Description                                                                                                                                                                    |
|----------------------------|---------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tier-0 Router Name         | < location code >lrt001               |                                                                                                                                                                                              |
| Description                | < Description >                       |                                                                                                                                                                                              |
| Edge Cluster               | < location code >ecn001               |                                                                                                                                                                                              |
| High Availability Mode     | Active-Active                         | High Availability mode is set to default Active-Active, which allows to coexist two Edge Nodes with ECMP enabled.                                                                            |
| Failover Mode              | N/A                                   |                                                                                                                                                                                              |
| Intra Tier0 transit subnet | optional                              | There is no need to change this value from default (169.254.0.0/28), due to fact that this is generated automatically network which is isolated with no risk that it would be redistributed. |
| Tier0-Tier1 transit subnet | optional                              | There is no need to change this value from default (100.64.0.0/16), due to fact that this is generated automatically network which is isolated with no risk that it would be redistributed.  |
| Uplink Interfaces          | < uplink interface network segments > | segment need to be untaged (vlan 0)                                                                                                                                                          |

##### General Configuration of the NSX-T T0 VRF - Workload Domain

| Parameter                   | Value                                 | Justification/Description                                                                                                                                                                    |
|-----------------------------|---------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tier-0 VRF                  | < location code >vrf001               |                                                                                                                                                                                              |
| Description                 | < Description >                       |                                                                                                                                                                                              |
| Edge Cluster                | < location code >ecn001               | need to be the same Edge Cluster as for linked T0 logical router                                                                                                                             |
| T0 Logical Router linked to | < T0 Logical Router name >            | T0 Logical Router used as template for BGP configuration                                                                                                                                     |
| Failover Mode               | N/A                                   |                                                                                                                                                                                              |
| Intra Tier0 transit subnet  | optional                              | There is no need to change this value from default (169.254.0.0/28), due to fact that this is generated automatically network which is isolated with no risk that it would be redistributed. |
| Tier0-Tier1 transit subnet  | optional                              | There is no need to change this value from default (100.64.0.0/16), due to fact that this is generated automatically network which is isolated with no risk that it would be redistributed.  |
| Uplink Interfaces           | < uplink interface network segments > | segment need to be taged with exact WAN VLAN                                                                                                                                                 |

##### Interfaces configuration of the NSX-T Tier 0 Logical Router - Workload Domain

| Int | Name   | Type   | MTU  | Transport Node              | URPF mode | Logical Switch | Logical Switch Port       | IP Address/mask |
|-----|--------|--------|------|-----------------------------|-----------|----------------|---------------------------|-----------------|
|     | Uplink | Uplink | 1500 | < Workload Transport Node > | Strict    | < VLAN 0 >     | Attach to new switch port | none            |

Uplink interface is an entry to the Physical infrastructure, and it is only way how multiple VRFs can talk through single Edge Cluster.
T0 Logical Router firewall capability will be disabled.
NAT, LB and VPN features will not be configured on T0 logical router. Functions are not supported by T0 VRFs and as such to will not be used also on T0 Logical Router for design comapatibility between domains.
BGP protocol will be configured on T0 logical router, but no neighbors and redistribution will be configured, this options will be configured on T0 VRF only, it is important to understand that all general BGP options like ECMP support or Local AS will be inherit on T0 VRFs from T0 logical router.

##### Interfaces configuration of the NSX-T Tier 0 VRF - Workload Domain

| Int | Name                   | Type        | MTU  | Transport Node              | URPF mode | Logical Switch  | Logical Switch Port       | IP Address/mask                                      |
|-----|------------------------|-------------|------|-----------------------------|-----------|-----------------|---------------------------|------------------------------------------------------|
|     | Uplink                 | Uplink      | 1500 | < Workload Transport Node > | Strict    | < Ext VLAN LS > | Attach to new switch port | < IP Address of Customer EXTVLAN Subnet >/< Prefix > |
| 1   | LinkedPort_< T1_name > | Linked Port | \-   | \-                          | \-        | \-              | \-                        | \-                                                   |
| 2   | …                      |             |      |                             |           |                 |                           |                                                      |
| 3   | LinkedPort_< T1_name > | Linked Port | \-   | \-                          | \-        | \-              | \-                        | \-                                                   |

Linked Ports would be created automatically for each T1 Logical Router connected to this T0 VRF within the workload domain.  
Uplink interface is an entry to the Physical infrastructure, and it is only way how multiple Workload Domain can talk with each other.  
Tier 0 VRF is not also capable to handle NAT, LB, VPN or FW functions. As such these need to configured on respective T1 logical routers.  
T0 VRF have option to share routes with physical routers using dynamic routing protocol – BGP. There is no support for other dynamic routing protocols.

##### BGP Configuration on NSX-T T0 VRF - Workload Domain

| Parameter         | Value               | Justification/Description                                                                                                                                                                             |
|-------------------|---------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Status            | Enabled             |                                                                                                                                                                                                       |
| ECMP              | Enabled             | will be inherited by all VRFs linked to T0 Logical Router                                                                                                                                             |
| Graceful Restart  | Disabled            | Graceful Restart would be disabled due to fact that VCS is providing two Edge Nodes in Cluster, so there is no need to inform other devices that something is happening if there is better mechanism. |
| Local AS          | < Local AS Number > | Autonomous System will be inherited by all VRFs connected to                                                                                                                                          |
| Route Aggregation | None                | There is no need to aggregate any routes by default but might be changed in case of Business request.                                                                                                 |

Follwing table will summarize with BGP Parameter will be configured on T0 Logical Router (Linked) and onT0 VRF

| Option                                 | configured on                                            | Justification/Description                                                                                                                                                      |
|----------------------------------------|----------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Local AS number                        | T0 Logical Router, inherited by T0 VRF BGP configuration |                                                                                                                                                                                |
| ECMP configuration                     | T0 Logical Router, inherited by T0 VRF BGP configuration |                                                                                                                                                                                |
| GRES configuration                     | T0 Logical Router, inherited by T0 VRF BGP configuration |                                                                                                                                                                                |
| BGP neighbor IP address (IPv4 or IPv6) | T0 VRF directly                                          |                                                                                                                                                                                |
| BGP neighbor remote AS number          | T0 VRF directly                                          |                                                                                                                                                                                |
| BGP neighbor password                  | T0 VRF directly                                          |                                                                                                                                                                                |
| BGP neighbor timers                    | T0 VRF directly                                          |                                                                                                                                                                                |
| BGP allow-as-in                        | T0 VRF directly                                          |                                                                                                                                                                                |
| BFD Configuration                      | T0 VRF directly                                          | Disabled by default. In case that BFD is supported by Psychical infrastructure, it’s recommended to use BFD to enhance performance of the BGP protocol. By default - disabled. |

IP Prefix Lists, Community Lists and Route Maps are not part of default build of the VCS but can be easily used to filter prefixes distributed or learned to/from neighbors.

Route Redistribution is used to add routes from particular source to the routing table of the Logical Router. It is important to understant that on T0 VRF redistribution there is separate option for T1 and T0 routes to be redistributed, by default only T1 Connected, Static, NAT, IPsec Local Endpoint, LB SNAT and LB VIP routes will be injected to BGP and advertised to neighbors.

If both IPv4 and IPv6 address families are needed there is a need to have dedicated BGP peers for IPv4 and for IPv6 address family as for NSX-T only one address family is possible per BGP neighbor.

##### Redistribution on NSX-T T0 VRF - Workload Domain

| Parameter                             | Value                     | Justification/Description                                                                                                                                                                                                                                                                                                                                                                                                         |
|---------------------------------------|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Route Redistribution                  | Enabled                   |                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| Redistribution Criteria (Name)        | < Redistribution Name >   |                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| Redistribution Criteria (Description) | < Description >           |                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| Redistribution Criteria (Sources)     | NSX Connected, NSX Static | In order to redistribute routes connected to the Tier 1 Logical Routers to the Physical environment there is a need to redistribute Static routes In case of having any network directly connected to T0 and to be able to redistribute those as well, there is a need to add Connected. There might be a use case that Tier1-LB VIP or Tier1-LB SNAT would be required if Load Balancers would be created on T1 Logical Routers. |
| Route Map                             | None                      | There are no business requirements to filter routes which would be redistributed from and to VCS. In case of need – there is a possibility to use this function.                                                                                                                                                                                                                                                                  |

| Decision ID | Design Decision                                                                                                                                                                            | Design Justification                                                                                                                                                                                                                                                                  | Design Implication                                                                                                              |
|-------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|
| T0LR001     | Dynamic routing (BGP) configuration would be used by default by VCS for communication with Customer infrastructure on both Management and Workload Domains. Protocol of the choice is BGP. | To allow easily exchange routes from T0 LR to physical infrastructure, dynamic routing protocol is chosen.                                                                                                                                                                            | Its required that customer infrastructure should support BGP routing protocol on his routers as well as Physical Firewall.      |
| T0LR002     | Static routing configuration is not preferable.                                                                                                                                            | Once using Static routing there is a need to create a lot of static routes in each workload domain. There is as well problem if Customer is willing to use automatization of the deployment. Management domain is not having an option to use static routing anyway (VCF limitation). | Static routing might be easier solution for the customers, but because of the limitations might become obstacle for automation. |
| T0LR003     | NAT feature is not used by default                                                                                                                                                         | There is no business case to use NAT based on initial requirements                                                                                                                                                                                                                    | This is not stopping to allow customer to modify this for his own needs                                                         |
| T0LR004     | ECMP under T0 Logical Router is enabled                                                                                                                                                    | Allowing ECMP gives benefits while use Active-Active T0 on Edge Nodes.                                                                                                                                                                                                                | Traffic can be forwarded by both T0 Logical Router instances (each located on different Edge Nodes).                            |
| T0LR005     | Graceful Restart is disabled                                                                                                                                                               | Graceful restart is useful if the is no HA mechanism to not drop BGP sessions immediately in case of restart i.e. VCS is providing two Edge Nodes which are clustered and there is no need for GR.                                                                                    | N/A                                                                                                                             |
| T0LR006     | eBGP is a dynamic routing protocol of choice                                                                                                                                               | eBGP is giving much more flexibility in configuration and its more preferable in routing table                                                                                                                                                                                        | N/A                                                                                                                             |
| T0LR007     | BGP Address Families are not used                                                                                                                                                          | There is no need to filter what routes are redistributed due to fact that all networks should be by default reachable from Customer Infrastructure.                                                                                                                                   | Necessary configuration can be done on Physical Router.                                                                         |
| T0LR008     | BGP Timers are default for Workload Domain.                                                                                                                                                | Timers are left as default due to fact that its individual case for customer.                                                                                                                                                                                                         | Necessary adjustments can be done after build.                                                                                  |
| T0LR009     | BFD is not used by default.                                                                                                                                                                | Not all devices are supporting BFD, there is no business requirements for BFD.                                                                                                                                                                                                        | BFD can be enabled if it’s needed from customer perspective.                                                                    |
| T0LR010     | Route Redistribution on T0 is done using NSX Connected and NSX Static on Workload Domain                                                                                                   | Static routes are those which are learned from T1 – this is how NSX-T is advertising routes between Logical Routers T1 and T0. Connected routes are necessary in case of having any directly connected networks into T0. Route Redistribution filters might be used in case of need.  | N/A                                                                                                                             |

#### T1 Logical Router

To provide T0 logical router or T0 VRF information about networks connected to T1 Logical Router, T1 should redistribute static routes.

##### General Configuration of the NSX-T T1 Logical Router - Management Domain

| Parameter                  | Value                         | Justification/Description                                                                                                                                                                    |
|----------------------------|-------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tier-1 Router Name         | < Edge Cluster Name >-t1-gw01 |                                                                                                                                                                                              |
| Description                | < Description >               |                                                                                                                                                                                              |
| Tier-0 Router              | < Edge Cluster Name >-t0-gw01 | Tier 0 Logical Router which will be top-tier layer for this Tier 1 Logical Router.                                                                                                           |
| Edge Cluster               | < Edge Cluster Name >         |                                                                                                                                                                                              |
| Failover Mode              | Non-Preemptive                | There is no need to preempt T1 Logical router active member to initial Edge Transport Node in case of restore from failure.                                                                  |
| Intra Tier1 transit subnet | optional                      | There is no need to change this value from default (169.254.0.0/28), due to fact that this is generated automatically network which is isolated with no risk that it would be redistributed. |

Following Table is providing details how T1 Logical Router interfaces should be configured:

##### Interfaces configuration of the NSX-T Tier 1 Logical Router - Management Domain

| Int | Name                            | Type        | URPF mode | Logical Switch              | Logical Switch Port       | IP Address/mask                              |
|-----|---------------------------------|-------------|-----------|-----------------------------|---------------------------|----------------------------------------------|
|     | LinkedPort_< T1_name >          | Linked Port | \-        | \-                          | \-                        | \-                                           |
| 1   | < customer location >-m01-seg01 | Downlink    | Strict    | < Web Tier Logical Switch > | Attach to new switch port | < IP Address of Region A Subnet >/< Prefix > |
| 2   | xreg-m01-seg01                  | Downlink    | Strict    | < App Tier Logical Switch > | Attach to new switch port | < IP Address of xRegion Subnet >/< Prefix >  |

T1 Logical Router does not contain any Uplink interfaces, due to the fact that this is a technology limitation. T1 Logical Router can be connected with the Physical environment only via a T0 Logical Router which acts as a gateway for the T1 Logical Router.

Downlink interfaces are used for vRealize specific networks.

Route Advertisement configuration is as follows:

##### Route Advertisement on NSX-T T1 Logical Router - Management Domain

| Parameter                           | Value   | Justification/Description                                                                |
|-------------------------------------|---------|------------------------------------------------------------------------------------------|
| Status                              | Enabled | Route Advertisement needs to be enabled to advertise routes from T1 to T0 Logical Router |
| Advertise All Static Routes         | No      | In case of using Static Routing, it might be changed to Yes.                             |
| Advertise All NAT IP Routes         | Yes     | VCF Default configuration                                                                |
| Advertise all DNS Forwarder Routes  | Yes     | VCF Default configuration                                                                |
| Advertise All LB VIP Routes         | Yes     | VCF Default configuration                                                                |
| Advertise All Connected Segments    | Yes     | VCF Default configuration                                                                |
| Advertise All LB SNAT IP Routes     | Yes     | VCF Default configuration                                                                |
| Advertise All IPSec Local Endpoints | Yes     | VCF Default configuration                                                                |
| Advertise routes                    | None    | VCF Default configuration                                                                |

##### General Configuration of the NSX-T T1 Logical Router - Workload Domain

| Parameter                  | Value                            | Justification/Description                                                                                                                                                                    |
|----------------------------|----------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tier-1 Router Name         | < T1 name >                      |                                                                                                                                                                                              |
| Description                | < Description >                  |                                                                                                                                                                                              |
| Tier-0 VRF                 | < Tier0 Logical Router name >    | Tier 0 VRF which will be top-tier layer for this Tier 1 Logical Router.                                                                                                                      |
| Edge Cluster               | < Edge Cluster Name >            |                                                                                                                                                                                              |
| Failover Mode              | Non-Preemptive                   | There is no need to preempt T1 Logical router active member to initial Edge Transport Node in case of restore from failure.                                                                  |
| Edge Cluster Members       | < Edge Node 1 >, < Edge Node 2 > | This value must be setup for all centralized services. Both Edge Nodes inside cluster will be used to provide redundancy                                                                     |
| Intra Tier1 transit subnet | optional                         | There is no need to change this value from default (169.254.0.0/28), due to fact that this is generated automatically network which is isolated with no risk that it would be redistributed. |

##### Interfaces configuration of the NSX-T Tier 1 Logical Router - Workload Domain

Following Table is providing details how T1 Logical Router interfaces should be configured:

| Int | Name                      | Type        | URPF mode | Logical Switch               | Logical Switch Port       | IP Address/mask                                      | Relay Service |
|-----|---------------------------|-------------|-----------|------------------------------|---------------------------|------------------------------------------------------|---------------|
|     | LinkedPort_< T1_name >    | Linked Port | \-        | \-                           | \-                        | \-                                                   | \-            |
| 1   | <AttachedExampleSegment1> | Downlink    | Strict    | <AttachedExampleSegment1 >   | Attach to new switch port | < IP Address of AttachedExampleSegment1 >/< Prefix > | \-            |
| 2   | <AttachedExampleSegment2> | Downlink    | Strict    | < AttachedExampleSegment2 >  | Attach to new switch port | < IP Address of AttachedExampleSegment2 >/< Prefix > | \-            |
| 3   | <AttachedExampleSegment3> | Downlink    | Strict    | < DAttachedExampleSegment3 > | Attach to new switch port | < IP Address of AttachedExampleSegment3 >/< Prefix > | \-            |

T1 Logical Router does not contain any Uplink interfaces, due to the fact that this is a technology limitation. T1 Logical Router can be connected with the Physical environment only via a T0 Logical Router which acts as a gateway for the T1 Logical Router.

Downlink interfaces are used for customer specific networks, and they depend on customer requirements. They can be modified to customer needs.  
Static routes are not the preferred way of configuration in VCS, but this option is necessary in case of using static routes on T0 Logical Router.  
No default configuration is predefined due to fact that VCS don’t have business need.

Route Advertisement is used to advertise routes to the T0 Logical Router. This feature needs to be enabled and there is a need to advertise Connected Routes as well as LB VIP and LB SNAT IP routes in case of using Load balancer feature. Configuration is as follows:

On single NSX-T Segment T1 logical router can act as IPv4 gateway and IPv6 gateway simultaneously.

##### Route Advertisement on NSX-T T1 Logical Router - Workload Domain

| Parameter                          | Value   | Justification/Description                                                                                                                                                          |
|------------------------------------|---------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Status                             | Enabled | Route Advertisement needs to be enabled to advertise routes from T1 to T0 Logical Router                                                                                           |
| Advertise All NSX Connected Routes | Yes     | To advertise all connected downlinks.                                                                                                                                              |
| Advertise All NAT Routes           | No      | In case of using NAT, it might be changed to Yes.                                                                                                                                  |
| Advertise All Static Routes        | No      | In case of using Static Routing, it might be changed to Yes.                                                                                                                       |
| Advertise All LB VIP Routes        | No      | In case of using LB it might be changed to Yes.                                                                                                                                    |
| Advertise All LB SNAT IP Routes    | No      | In case of using LB it might be changed to Yes.                                                                                                                                    |
| Advertise routes                   | None    | Manually add routes to advertise. There is no need to use those based on current requirements. But This option might be used in case of need to filter some routes advertisements. |

NAT Feature is not used by default, but it is available for deployment. Any required configuration is supported.

##### Design Decisions for NSX-T T1 Logical Router

| Decision ID | Design Decision                                                                                   | Design Justification                                                                                                               | Design Implication                                |
|-------------|---------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------|
| T1LR001     | Static routing is not used                                                                        | There is no need to use static routing, in case of need, static might be used.                                                     | This is simplifying configuration and management. |
| T1LR002     | By default, Route Advertisement is enabled, and advertisement of the Connected routes is enabled. | There is no requirement to advertise any other type of routes by default. In case of business case, remaining options can be used. | N/A                                               |
| T1LR003     | By default, NAT feature is not enabled.                                                           | There is no business requirement for initial deployments to use NAT. NAT would be available in case of need.                       | N/A                                               |

### Logical Router Services

### NAT

NAT translations need to be configured on T1 logical routers only, due fact that T0 VRF not supports NAT translations, this imply that NAT translation is valid only for networks connected to T1 router.

Only IPv4 NATs are supported.

### VPN

VPN tunnels need to be configured on T1 logical routers only, due fact that T0 VRF not supports VPN services, this imply that VPN tunnel need to be connected networks connected to T1 router, also in way of using static routing, such static routes need to be advertised on T1 level and on T0 VRF level.

### Load Balancers

The NSX-T logical load balancer offers high-availability service for applications and distributes the network traffic load among multiple servers. The load balancer accepts TCP, UDP, HTTP, or HTTPS requests on the virtual IP address and determines which pool server to use. Logical load balancer is supported only on the T1 Logical Routers.

Both IPv4 and IPv6 VIP are supported.

##### NSX-T Load Balancer Parameters

| Parameter             | Options                                                                                                                                                                                                                                                                                                       |
|-----------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Protocols             | TCP, UDP, HTTP, HTTPS                                                                                                                                                                                                                                                                                         |
| Load Balancing Method | Round-Robin, Weighted Roun-Robin, Least-Connection and IP-Hash                                                                                                                                                                                                                                                |
| Pools                 | Static or Dynamic (via Security Groups)                                                                                                                                                                                                                                                                       |
| Persistence           | Source-IP, Cookie (Insert, Prefix, Rewrite)                                                                                                                                                                                                                                                                   |
| Monitors              | Active (LB generates HTTP, HTTPs, TCP, UDP, ICMP probes), Passive (LB monitors client connections)                                                                                                                                                                                                            |
| SNAT                  | Transparent (No LB-SNAT), Automap (LB-SNAT using LB IP), IP List (LB-SNAT using IP list)                                                                                                                                                                                                                      |
| L7 LB Rules           | Rules with Regex support (i.e. Host Load Balancing, URL block, URL rewrite, response header rewrite)                                                                                                                                                                                                          |
| L4 Acceleration       | TCP multiplexing                                                                                                                                                                                                                                                                                              |
| SSL                   | SSL Offload (LB terminates HTTPS and talk using HTTP to server)  SSL End-to-End (LB terminates HTTPS and talk using HTTPS to server)  SNI support (LB presents different certificates to client based on host name presented by client)  Client Certificate authentication (LB asks and validate client cert) |

##### Supported Load Balancer Features

![loadbalancer](images/lldSoftwareDefinedNetworks/loadbalancer.png)

#### Inline Load Balancer

Load Balancer in inline mode is placed between LB Client and Pool Member (Server). Clients and servers cannot be connected to the same Tier-1 Logical Router, but this solution is not require using SNAT. This solution is less preferred due to fact that it's limiting flexibility.

##### NSX-T Inline Load Balancer

![loadbalancer](images/lldSoftwareDefinedNetworks/inlineLoadbalancer.png)

#### One-Arm Load Balancer

Load Balancer in One-Arm mode doesn't have to be placed between LB Client and Pool Member. Both client and server can be placed anywhere.

Normally Tier-1 Logical Router is linked directly to Tier-0 to allow routing to and from outside world, as well as other segments and Tier-1 Logical Routers in environment. One-arm Load Balancer needs to be deployed on dedicated Tier-1 Logical Router which is NOT connected to any Tier-0 Logical Router.

The load balancer is performing SNAT (Source NAT) to force return traffic from the Pool Members destined to the client to go through the load balancer. Because of that Source NAT should be enabled in the topology. When the load balancer receives the client traffic to the virtual IP address, the load balancer selects a server pool member and forward the client traffic. One-arm load balancer is replacing the client IP address with it's own (load balancer IP). Pool member response is always sent to the load balancer, then load balancer forward response to the client.

##### NSX-T One-Arm Load Balancer

![loadbalancer](images/lldSoftwareDefinedNetworks/oneArmLoadbalancer.png)

### Network I/O Control for NSX-T

Network I/O Control profile is ensuring that traffic will be properly prioritized/limited in case of network contention. Network profile is attached to all N-VDSes.

Network I/O Control enforces the share value specified for the different traffic types only when there is network contention. When contention occurs, Network I/O Control applies the share values set to each traffic type. As a result, less important traffic, as defined by the share percentage, is throttled, granting access to more network resources to more important traffic types. Network I/O Control also supports the reservation of bandwidth for system traffic according to the overall percentage of available bandwidth.

##### Network I/O Control Profile configuration

| Parameter                              | Value           | Justification/Description            |
|----------------------------------------|-----------------|--------------------------------------|
| Name                                   | < name >        | Name of the NIOC Profile             |
| Description                            | < description > | Description of the NIOC Profile      |
| Status                                 | Enabled         | n/a                                  |
| Fault Tolerance (FT) Traffic           | 25              | Shares value for FT                  |
| vSphere Replication (VR) Traffic       | 25              | Shares value for vSphere Replication |
| iSCSI Traffic                          | 25              | Shares value for iSCSI               |
| Management Traffic                     | 50              | Shares value for Management          |
| NFS Traffic                            | 25              | Shares value for NFS                 |
| vSphere Data Protection Backup Traffic | 25              | Shares value for Backup              |
| Virtual Machine Traffic                | 100             | Shares value for Virtual Machine     |
| vMotion Traffic                        | 25              | Shares value for vMotion             |
| vSAN Traffic                           | 100             | Shares value for vSAN                |

Above configuration is automatically done by VCF and should not be modified.

##### Network I/O Control for NSX-T Design Decisions

| Decision ID | Design Decision                                                       | Design Justification                                                                                                                                                                                  | Design Implication                                                                                          |
|-------------|-----------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|
| NIOCT001    | Create and attach a Network I/O Control Policy on all N-DVS switches. | Increases resiliency and performance of the network.                                                                                                                                                  | If configured incorrectly, Network I/O Control might impact network performance for critical traffic types. |
| NIOCT002    | Set the share value for vSphere vMotion traffic to Low (25).          | During network contention, vSphere vMotion traffic is not that important. Virtual Machine or storage traffic is more important.                                                                       | It may cause longer times than usual for vMotion.                                                           |
| NIOCT003    | Set the share value for vSphere Replication traffic to Low (25).      | vSphere Replication traffic is not as important as Virtual Machine Traffic or Storage traffic.                                                                                                        | Once network contention will occur, vSphere Replication will take more than usual.                          |
| NIOCT004    | Set the share value for vSAN traffic to High (100).                   | During Network contention, vSAN traffic needs a guaranteed bandwidth to support virtual machine performance.                                                                                          | None                                                                                                        |
| NIOCT005    | Set the share value for management traffic to Normal (50).            | Management traffic during network contention is less important than vSAN traffic but more important than vSphere replication or vMotion to keep environment accessible in case of  network contention | None                                                                                                        |
| NIOCT006    | Set the share value for NFS traffic to Low (25).                      | VCS design does not use NFS traffic (vSAN is storage mechanism of choice)                                                                                                                             | None                                                                                                        |
| NIOCT007    | Set the share value for backup traffic to Low (25).                   | During Network Contention, basic functionalities should remain accessible. Backup is not critical service in case of contention.                                                                      | During network contention backups will take more than usual.                                                |
| NIOCT008    | Set the share value for virtual machines to High (100).               | In VCS Virtual Machines are critical asset. By setting up value to High, VM will always get access to network resources.                                                                              | None                                                                                                        |
| NIOCT009    | Set the share value for vSphere Fault Tolerance to Low (25).          | VCS design does not use vSphere Fault Tolerance. Fault tolerance traffic can be set the lowest priority.                                                                                              | None                                                                                                        |
| NIOCT010    | Set the share value for iSCSI traffic to Low (25).                    | VCS design does not use iSCSI. iSCSI traffic can be set the lowest priority.                                                                                                                          | None                                                                                                        |

## Overlay Network Security

### Role Based Access Control

VCS offers RBAC on the NSX-T Manager level.

RBAC for NSX-T is achieved by using VMware Identity Manager which is injecting users into NSX. Configuration of NSX-T integration with IDM is shown in below table.

##### NSX-T VMware Identity Manager Configuration

| Parameter                           | Value                  | Justification/Description                                  |
|-------------------------------------|------------------------|------------------------------------------------------------|
| External Load Balancer Integration  | unchecked              | There is no need to use external Load Balancer Integration |
| Vmware Identity Manager Integration | checked                | There is a need to configure IDM on NSX-T                  |
| VMware Identity Manager Appliance   | < IDM name FQDN >      |                                                            |
| OAuth Client ID                     | < NSX-T manager name > |                                                            |
| OAuth Client Secret                 | < password >           | Secret password generated by IDM                           |
| SSH Thumbprint                      | < thumbprint >         | Information gathered from IDM                              |
| NSX Appliance                       | < NSX-T manager FQDN > |                                                            |

In addition to Identity Manager configuration, there is a need to create User Groups inside NSX-T. Table NSX RBAC - Role Assignments shows two roles required in VCS for
NSX-T. Those users should be a part of the Active Directory domain with proper assignment of the users.

##### NSX RBAC - Role Assignments

| User                                          | Role             | Description/Justification                                                                                                                        |
|-----------------------------------------------|------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| rsce-< customer code >-nsx-l-auditors         | Auditor          | Users in this role can only view system settings and auditing, events and reporting information, but cannot make any configuration changes.      |
| rsce-< customer code >-nsx-l-enterpriseadmins | Enterprise Admin | Users in this role can perform all tasks related to deployment and configuration of NSX products and administration of the NSX Manager instance. |

##### NSX Local Accounts

NSX-T is build with 5 default accounts. guestuser1 and guestuser2 are disabled by default and should stay disabled by VCS design, additionaly audit user should be disbabled as well, on both NSX-T Controllers and NSX-T Edges

| username   | user ID | available on GUI | disabled by default | disabled by VCS design | configured only on controllers |
|------------|---------|------------------|---------------------|------------------------|--------------------------------|
| admin      | 10000   | yes              | no                  | no                     | no                             |
| root       | 10001   | no               | no                  | no                     | no                             |
| audit      | 10002   | yes              | no                  | yes                    | no                             |
| guestuser1 | 10003   | yes              | yes                 | yes                    | yes                            |
| guestuser2 | 10004   | yes              | yes                 | yes                    | yes                            |

##### Design Decisions - RBAC

| Decision ID | Design Decision                                                 | Design Justification                                                                    | Design Implication                                |
|-------------|-----------------------------------------------------------------|-----------------------------------------------------------------------------------------|---------------------------------------------------|
| RBAC001     | NSX-T will require IDM to apply RBAC.                           | NSX-T technology limitation in this area is forcing to use IDM.                         | IDM should be in place before RBAC configuration. |
| RBAC002     | Two roles would be used in NSX T, Auditor and Enterprise Admin. | Those two roles are required for managing environments as well as performing auditions. | N/A                                               |

### Firewall

#### General information

VCS provides two way of secure traffic inside environment. First is the Logical Router Firewall and second is the Distributed Firewall (DFW).

Logical Router only protects traffic on its uplink interfaces, so if there is east-west traffic on the same Logical Router, there is no way for the Logical Router to inspect such traffic.

NSX Distributed Firewall is a hypervisor kernel-embedded firewall that provides visibility and control for virtualized workloads and networks. The distributed nature of the firewall provides a scale-out architecture that automatically extends firewall capacity when additional hosts are added to the VCS.

Distributed Firewall applies firewall rules at the virtual machine kernel and network interface (vNIC) right above the guest OS, meaning that each packet that leaves or enters a VM is processed systematically by the DFW before the packets are processed by the ESXi forwarding engine.

DFW is providing following benefits:

- speed of the processing traffic is higher
- network utilization is lower due to filtering unwanted flows at the start of flow
- security rules can be agnostic to the network layer
- security implementation is not depending on network architecture
- can be combined with traditional physical or virtual firewalls in an overall solution
- stateful security that migrates with workload
- its a microsegmentation mechanism

DFW and Edge FW fully supports both IPv4 and IPv6 based rules. IP address object, VM objects and TAGs are supported for both IPv4 and IPv6. Only difference is that in default IP Discovery profile, VMware Tools - IPv6 options is disabled while VMware Tools option is enabled, this mean that for segment with default IP Discovery Profile it is not possible to use VM objects or TAGs in FW rule, however as both VMware Tools options are enabled in dhc-ip-discovery-profile it is not a problem in VCS.

It is important to mention, that in case of IPv6 over Ethernet, MAC address resolution is not done by ARP but by ICMPv6. This have significant impact on working with DFW, as from historical reason DFW by default is not blocking L2 protocols and is operating only on L3 protocols, this means that for IPv4 where ARP is used for IP to MAC addresses mapping by default there is no need for any changes, since IPv6 is using ICMPv6 that is L3 protocol for all VMs at least IMCPv6 type 135 (Neighbor Solicitation) and ICMPv6 type 136 (Neighbor Advertisement) need to be allowed.

##### General guidelines about NSX Logical Router firewall rules

| No | Guideline                                                                                                                                                                                                                                                                                                                                                 |
|----|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1  | Where interface rules are defined, these should be placed before any non-interface rules with an associated deny from that interface immediately beneath the accept rules, thereby effectively creating a section of rules, if specific addresses from the interface need specific permissions then these would need to be added before the deny is added |
| 2  | For dedicated Internet facing interfaces do not accept based upon 0.0.0.0/0 but instead accept based upon the interface (VNIC Group) this ensures that blocks from other interfaces by IP addresses can be deployed                                                                                                                                       |
| 3  | Group similar rules together, this makes it clearer for the reader of the ruleset what is and is not allowed – i.e. all Internet Rules, all WAN Rules                                                                                                                                                                                                     |
| 4  | Ensure that rules always have a sensible name, for complex rules add a comment to enable better understanding                                                                                                                                                                                                                                             |
| 5  | Use NSX Objects – Security Groups and Services. IP Addresses and obscure ports can easily be forgotten their purpose. NSX Objects can be given meaningful names and normally descriptions to add clarity.                                                                                                                                                 |
| 6  | Ensure that the Logical Routers as well as Edge Transport Nodes are protected from external connection except where required                                                                                                                                                                                                                              |
| 7  | Enable ICMP between internal network and the Edges to aid in diagnostics                                                                                                                                                                                                                                                                                  |
| 8  | Log all denies, additional logging should be applied based upon the customer security requirements                                                                                                                                                                                                                                                        |
| 9  | Ensure that the default rule is set to deny                                                                                                                                                                                                                                                                                                               |

##### General guidelines about NSX DFW firewall rules

| No | Guideline                                                                                                                                                                                        |
|----|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1  | Ensure Firewall rules are easy to understand (KISS)                                                                                                                                              |
| 2  | By default, all Firewall rules will be applied across the whole Distributed Firewall                                                                                                             |
| 3  | A Firewall rule must contain ONLY IP Addresses, ANY and NSX Objects (Network related and Infrastructure or Application based) - those IP addresses should be part of the Security Groups anyway. |
| 4  | IP Addresses and/or ANY must not be present in Source AND Destination for any single Firewall rule either directly or indirectly via Security Group or IPSets (prevent Network related rules)    |
| 5  | Principle #4 may be broken provided that the Applied TO is specific to a Security Group/Logical Switch                                                                                           |
| 6  | Do not use nested Security Groups – this would break Principle #1                                                                                                                                |
| 7  | Prevent Network based Firewall Rules                                                                                                                                                             |
| 8  | Ensure that the default rule is set to deny                                                                                                                                                      |

##### Standard Firewall Settings

| Standard Security Object        | Value | Comment                                                           |
|---------------------------------|-------|-------------------------------------------------------------------|
| Default Security Policy         | Deny  | Best Practice is to have a Firewall automatically block traffic   |
| Default Security Policy Logging | Log   | Ensure that dropped traffic is logged                             |
| Drop Invalid Traffic            | True  | Ensure that badly formatted or crafted packets are dropped        |
| Log Invalid Traffic             | True  | Ensure that badly formatted or crafted packets are logged on drop |

VCS build supports using Source/Destination objects composed of one or more of the following:

##### VCS Supported Logical Router Firewall Object Types

| Source/Destination Object Type | Source          | Destination     | Comment                                                                                                                                                               |
|--------------------------------|-----------------|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Any                            | Not recommended | Not recommended | Caution should be exercised with Any as it signifies any IP address on any interface. It is recommended to specify a vNIC Group instead of using Any for this reason. |
| NSX Security Group             | Yes             | Yes             | Used to create a placeholder for objects such as Virtual Machines to be assigned to. IP addresses might be an Security Group object as well.                          |
| NSX Edge vNIC Group            | Yes             | Yes             | For source indicates a packet received on this interface. For destination indicates a packet is leaving on this interface                                             |
| IP Address                     | Not recommended | Not recommended | Normally it is preferred to create an IP set where a description can be applied. IP Addresses should normally not be used.                                            |

Multiples of the above can be selected as a source and/or destination in a single Firewall rule to provide necessary granularity of potential source/destination objects that can be match against, where this is done each of the source objects can communicate with each of the destination using the selected protocols and services.

Once source and destination have been selected, the Service must be selected, which can be one of the following:

##### Supported Service Object Types

| Destination Service Object Type | Example   | Comment                                                                                                                                                        |
|---------------------------------|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Any                             |           | Permits any Protocol, TCP, UDP, ICMP etc. Do not use this on an external interface.                                                                            |
| Protocol                        | UDP       | Permit all UDP traffic                                                                                                                                         |
|                                 | TCP       | Permit all TCP traffic                                                                                                                                         |
|                                 | TCP:500   | Permit TCP traffic on a specific port eg 500                                                                                                                   |
| Service                         | SSH       | Permit predefined Service Object SSH (matches to TCP port 22)                                                                                                  |
| Service Group                   | Heartbeat | Permit predefined Service Group which is a collection of Service Objects (matches to Service Objects Vmware-Heartbeat-PrimarySecondary and Vmware-VCHeartbeat) |

Whilst a Source Service Object may be selected of the same type as Destination Service Object types it is common practice to let the Source Service Object default to Any.

Finally, an Action is defined of Accept, Deny or Reject. When blocking traffic to the Internet use Deny rather than Reject to keep infrastructure invisible from outside.

Additional options for whether to Log the match on the Firewall Rule or not and whether to match on original or translated addresses plus the direction of traffic through the interface to perform the match on can be applied.

##### Firewall Rule Approach

| Approach                                                                                                                                                                           | Reason                                                                                                                                                                                                                                 |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Logging must be applied to all flows coming from a source outside of the locally attached Edge interface (i.e. routed from Internet, Customer WAN or Edge)                         | External traffic is inherently untrusted, and access must therefore be logged                                                                                                                                                          |
| Where traffic from external sources is not permitted it will be blocked with a DENY action                                                                                         | Two methods of blocking are available, DENY and REJECT. Using REJECT will indicate a valid host to an external device which is undesirable especially if that host is Internet facing as it allows a hacker to map an internal network |
| Flows generated by the Edge Services Gateway, eg Load Balancer VIPs, should have open Firewall rules to the backend Servers                                                        | Internally generated flows are trusted                                                                                                                                                                                                 |
| Rules must be created securely but simply – there is no need for multiple layers of the same rule                                                                                  |                                                                                                                                                                                                                                        |
| Traffic from the Internet will be blocked from egressing to all other services by other than front end Server Farms or where no Load Balancer is present the front-end Web Servers |                                                                                                                                                                                                                                        |

##### Design Decisions - Firewall

| Decision ID | Design Decision                                                          | Design Justification                                                                                                                                                                                                                                                                         | Design Implication                                                                                 |
|-------------|--------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| FW001       | DFW would be used in both Management Domain as well as Workload Domains. | Due to PCI compliance and internal security, there is a need to use Microsegmentation provided by DFW.                                                                                                                                                                                       | DFW in Management and Workload Domains will increase complexity in maintaining all firewall Rules. |
| FW002       | Preferred objects to use in Overlay Firewalls are Security Groups        | Security Groups are most flexible containers of objects in SDN. They are providing options to include even other Security Groups. By using Security Groups there is a possibility to predefine firewall ruleset as well on DR environment without necessity of having corresponding objects. | Security Groups will limit necessity of changing ruleset over and over again.                      |

#### Workload Domain Logical Router Firewall

Under Workload Domains, logical routers can be configured with firewall rules. Those rules are allowing to filter traffic between zones or from/to external world. NSX-T is providing tools to apply those rules only on uplink of the Logical Router. So if traffic is coming from Customer Router to T0 Logical router, traffic can be inspected by Logical Router Firewall. In case that two or more Logical Switches would be connected to T0 Logical router or to T1 Logical router as a downlinks, this Logical Router would not be able to inspect this traffic. In that case preferable solution is to use both - Logical Firewall and Distributed Firewall.

Logical Router Firewalling features can be enabled on both T0 and T1 Logical Routers. There are not predefined list of the firewall rules for this kind of protection, due to fact that there are too many possibilities how Customer can use security in his environment.

Following schema is presenting example placement of the Logical Router's firewalls.

##### NSX-T Logical Router Firewall

![firewall](images/lldSoftwareDefinedNetworks/edgeFirewall.png)

### Microsegmentation

Micro-segmentation is a network security technique that enables dividing data center into distinct security segments down to the individual workload level, in addition to that it's allowing to define security controls and deliver services for each unique segment. NSX Micro-segmentation is allowing to be very granular in this separation by applying flexible security policies deep into network virtualization technology instead of physical objects. Micro-segmentation is providing to VCS security into Management and Workload Domains, which means all components are fully protected.

To illustrate DFW Micro-segmentation, following drawing have been created. On this drawing terminal server is speaking with internet via proxy. Both are located on same Management Network. Because how DFW is working, inspection would be done three times on this path. Once Terminal Server is sending packet through his NIC, first inspection is done. Traffic is going through network to Proxy server where second DFW rule is present,so inspection have been done on egress of source and ingress of destination of the traffic. Next, Proxy is sending traffic to Internet, so egress traffic is inspected on NIC level of Proxy VM, then Physical FW is hit by traffic where inspection is done once again.

##### Microsegmentation practice example

![dfwexample](images/lldSoftwareDefinedNetworks/dfwExample.png)

#### Management Domain

Management domain is protected by Distributed Firewall to achieve microsegmentation even on Management VMs level. Microsegmentation is achieved by using following NSX-T components:

- Security Groups
- Services

Each mentioned in below table source or destination is achieved by using Security Groups with static members set IP addresses. Those which are having "APPLYTO" name are using Dynamic Membership where matching criteria is the name of the VM.  This table contain list of the Distributed Firewall Rules to achieve full communication between internal components. This table would be extended with external objects. Names inside Source or Destination columns are mentioned in here just for understanding purposes. Normally their name would be different (according naming convention). True names are mentioned in following section of this document named Management DFW Security Groups. Last column is showing on which objects this ruleset would be applied. This operation is necessary to limit number of FW rules inspected on each node.

Microsegmentation section is expanded by [lldSoftwareDefinedNetworksFirewall.md](#related-documents).

#### Workload Domain

Due fact that each VCS implementation is multitenant deployment and DFW rules are global for whole cluster, to provide separation and isolation of ruleset for particular Tenant, each Tenant will have at least one Policy. DFW Policy is method of grouping together FW rules. FW Rules providing option to decision making if traffic should be allowed or not. Any Policy configured on Workload Domain by default must have APPLY TO option configured to match Security Group consisting all Network Segments used by Tenant. APPLY TO option, allowing to decide on with Network Segment, Virtual Machines or Virtual Machines Interface FW rules will be enforced. In a case if more than one Policy is configured per Tenant possible is to configure Security Group used in APPLY TO to be unique per different Policies for same Tenant, but as this increase Management Overhead it is highly not recommended. Also default behavior of using APPLY TO per Policy instead of per Rule, can be changed if need but it is not recommended. Any Security Group used in APPLY TO matching conditions should use NSX-T Tags as prefered option.

## Availability and Scalability

### Availability Design

Most of the Availability for VCS SDN is accomplished by using vSphere High Availability. Components like NSX Controllers (including NSX Managers), Edges and Edge Transport Nodes, are taking advantage from vSphere High Availability and in case of Hypervisor failure those objects might be restarted on other host in the cluster. NSX is providing additional, availability mechanism to improve this done by vSphere HA. Objects like Controllers are creating cluster, and in case of failure of one of the member of this cluster, remaining two Controllers are keeping majority and stay operational. NSX-T Edge Transport Nodes are having build in availability mechanism by introducing Edge Transport Nodes Cluster. Members of this cluster may be in active-active or active-passive state.

#### Network Components

| Component                                   | Failure Protection |
|---------------------------------------------|--------------------|
| NSX-T Controllers (including NSX-T Manager) | Cluster            |
| NSX-T Edge Transport Node                   | NSX-T Edge Cluster |

##### Design Decisions - Availability

| Decision ID | Design Decision                                                                       | Design Justification                                                                                                                       | Design Implication                                                                                                                                              |
|-------------|---------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| AVAIL001    | Use vSphere HA to protect all clusters against failures.                              | vSphere HA supports a robust level of protection for both ESXi host and virtual machine availability.                                      | There is a need to provide necessary resources on remaining hosts to be able to handle migrated VMs in case of failure.                                         |
| AVAIL002    | NSX Controller Cluster should be build with 3 members.                                | Cluster should be operational in case of loosing one member from 3. Leaving one isolated, would cause split brain scenario.                | Each NSX would require Controllers to be split across different hosts, in case of failure of one hypervisor, majority of the cluster should stay unharmed.      |
| AVAIL003    | VCS would use at least two NSX-T Edge Transport Nodes in cluster for Workload Domain. | This decision is increasing availability of the Edge VMs in case of failure, and provide ready mechanism to almost immediately switchover. | Building two or more Edge Transport VM would require additional Workload resources. Anti-Affinity rules should be in place to keep those VMs on separate Hosts. |

### Scalability Design

Scalability in VCS Workload Domain in NSX-T can be divided into few pieces:

- Edge Transport Nodes and their Clusters Scalability
- Logical Routers Scalability
- Workload Domains Scalability

Each part would be explained in separate sections.

#### Edge Transport Nodes and Clusters Scalability

Edge Transport Nodes in NSX-T are deployed as a VMs form factor for all stateful services which cannot be distributed. Those are listed in section Edge Transport Nodes.

In general idea of creating multiple Edge Nodes and form a Cluster can have multiple reasons. First and most obvious is redundancy - in case that one of the members in Cluster will fail, remaining taking it's role.

Standard VCS build is containing two Edge Transport Nodes which are forming one Edge Cluster.

Edge Clusters can be scaled both Vertical and Horizontal, Vertical scale required power off Edge Transpot Node to modify vCPU and/or vRAM settings.

##### Edge Cluster Standard Design

![edge cluster design](images/lldSoftwareDefinedNetworks/edgeClusterStandard.png)

This can be extended in case of need of better redundancy or better uplink speed. Fact is that if one Edge Transport Node located on one ESXi host is sending data via uplink to Physical Router, throughput can be limited to the uplink speed of one ESXi host. To extend this throughput there is a possibility to deploy two or more Active-Active Edge Transport Nodes which can increase this throughput up to 8 uplinks (maximum ECMP paths in Cluster is 8).

For this scenario, scaled Standard design can be implemented.

##### Edge Cluster Scaled Standard Design

![edge cluster design](images/lldSoftwareDefinedNetworks/edgeClusterScaledStandard.png)

This will allow to increase redundancy and scale throughput.

In case of more complex designs, there is a possibility to implement Optimized design or Service Based design in case of need. Those designs are building segmentation of the services which might be useful in case of usage of the huge amount of services.

##### Edge Cluster Optimized Design

![edge cluster design](images/lldSoftwareDefinedNetworks/edgeClusterOptimized.png)

##### Edge Cluster Service Based Design

![edge cluster service design](images/lldSoftwareDefinedNetworks/edgeClusterServiceBased.png)

#### Logical Routers Scalability

Logical Routers are providing multiple capabilities - Routing, Firewalling, LoadBalancing hook, NAT etc. Design for Customer should be prepared with caution and be based on customer requirements.

VCS is offering three types of designs:

- Standard Design
- Scaled Standard Design
- Multi Tenant with Isolation Design

By default Standard Design is used. Standard Design contains one T0 Logical router and VRF which would be deployed using two Edge Transport Nodes combined into Cluster. This provides an option to keep redundancy and uplink on good level. T0 Logical Router and VRF are connected via an internal link to the T1 Logical Router where all customer Logical Switches are located (i.e. Web, App and Db Logical Switches). In case of need this can be replicated.

Standard Design containins two T1 Logical Routers.

Following diagram shows the Standard Design.

##### Logical Routers Standard Design

![logical routers design](images/lldSoftwareDefinedNetworks/logicalRouterStandard.png)

The Standard design can be scaled in case of need by adding Edge Transport Nodes to the Cluster and increasing in this way amount of T0 Logical Router's instances. In addition additional T1 Logical Routers can be added to handle separate Customer networks or services (i.e. Load Balancer - inline or ane arm, Firewall, NAT). This topology might be treated as a multi tenant solution. Such scalability design is represented by Scaled Standard Design on following diagram.

##### Logical Routers Scaled Standard Design

![logical routers design](images/lldSoftwareDefinedNetworks/logicalRouterScaledStandard.png)

Another design which might be implemented in case of needs is Multi Tenant with Isolation. This design is ideal for environments where there is no need to implement another workload domain but still provide necessary separation between environments. This solution is very resource consuming and might become very hard for troubleshooting, but it's still possible. Mentioned design is shown on following diagram.

##### Logical Routers Multi Tenant With Isolation Design

![logical routers design](images/lldSoftwareDefinedNetworks/logicalRouterMultiTenantWithIsolation.png)

This design has some limitations. Amount of Workload Domain resources consumed with Edge Transport Nodes is doubled if two Business Units are created. It's very important to understand that in such scenario the ONLY way to communicate between Business Units is to use Physical Infrastructure, it's prohibited to utilize same networks in both Business Units. This scenario is as well not a DR scenario, and cannot be treated in this way.

#### Workload Domains Scalability

Easiest form of the scalability is to add another Workload Domain to the VCS environment. This is most common way to scale environment as well. More details can be found in **lldSddcVmwareCloudFoundation.md** document, including limitations.

##### Design Decisions - Scalability

| Decision ID | Design Decision                              | Design Justification                                                                                                                                                                                                                                                                                                                                               | Design Implication                                                                                                                                                                                                  |
|-------------|----------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SCADD001    | Standard VCS build is using Standard Design. | Without knowing future infrastructure and customer requirements for services, there is no way to predict which design would be best for Customer to use. Standard Design would be most frequently used and is most universal build for all Customers at the beginning stage. This can be expanded to Scaled Standard Build without any charm to existing services. | Design should be picked based on customer requirements or amount of services used in his infrastructure. It may appear that Standard Design is not ideal for Customer Build - additional actions would be required. |

## Recoverability

Recoverability design in SDN environment is done by restoring NSX configuration. All components which are missing would be recreated. Section "NSX-T Manager Settings" covers necessary configuration for NSX Managers to be backed up. In case of failure any of the components, NSX can rebuild environment even from scratch.

Following section will cover only scenarios which will extend this capability of making configuration backup and restore.

### Component failure

In case of the components failure there is redundancy mechanism for each single SDN component. Redundancy options are listed above.

#### DataCenter failure - Stretched Clusters scenario

##### Design Decisions - DataCenter failure

| Decision ID | Design Decision                                                      | Design Justification                                                                                                                                                                                                            | Design Implication                                                                                                                                                                            |
|-------------|----------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| DRSC001     | "L2 scenario" will be used (all networks stretched between clusters) | Simpler design with less requirements                                                                                                                                                                                           | FW cluster switchover time less then 3 seconds                                                                                                                                                |
| DRSC002     | Preferred option with Twin-DC with distance less then 30km           | In order to achieve 5ms RTT for VSAN                                                                                                                                                                                            | distance between AZ1 and AZ2 DCs less than 30km                                                                                                                                               |
| DRSC003     | VSAN Witness host located in third-location                          | VSAN protection in case of any AZ failure                                                                                                                                                                                       | routing between AZs and VSAN Witness Host required                                                                                                                                            |
| DRSC004     | Active cluster member (physical FW) in AZ1                           | Most of VMs will be placed in AZ1 as VMware advised, in such to avoid unnecessary sending traffic to AZ2 to route traffic between VLANs Management, xRegion Network and Region A Network, active member should be placed in AZ1 | physical Firewall cluster members priority should be higher for members in AZ1, preemption should be enabled                                                                                  |
| DRSC005     | VSAN Witness host will use single interface                          | Simplicity in new cluster deployment and new VCS instance deployment                                                                                                                                                            | Two different MTU values will be used for VSAN traffic, as such traffic to VSAN Witness need to be tagged on each ESXi as different type (VSAN Witness) and sources from management interface |

##### Availability Zones and witness site

![availability zones design](images/lldSoftwareDefinedNetworks/availabilityZonesAndWitness.png)

As presented on diagram, all VLAN in Stretched Cluster scenario all stretched between both Availability Zones. Any connections inside the same VLANs in both Availability Zones will use ethernet switching for communication. Traffic between VLANs and to/from outside VCS will use active Physical Firewall as gateway.
As such traffic between VLANs inside Availability Zone 2 will have to use Physical Firewall in Availability Zone 1 to route traffic between those VLANs, this is happening due to fact how most of the Firewall clusters are working. This situation would be limited due to fact that most of the Management Components would be placed in Management Domain. Due to this fact Physical Firewall Cluster should be configured with preemption to switch back to AZ1 in case of AZ1 member failure and recovery, to avoid "flapping" switchover time should be less than 5 seconds.

For providing VSAN availability in a case of on AZ failure, VSAN traffic will have connecting with VSAN Witness Host. Witness would be built on separate location for availability purposes. As connection with VSAN Withes Host can not be lost for more than 5 seconds, it was decided that minimal switchover time for Physical Firewall cluster must be below 3 seconds.

Traffic between VSAN nodes requires a delay below average 5ms RTT. 5ms RTT between AZ1 and AZ2 need to be provided by underlay network with MTU 9000 on all devices in path and bandwidth no less than 10 Gbps must be supported.

Bandwidth requirements between VSAN nodes in VCS and VSAN Witness Host located outside VCS depends on number VMs stored in VSAN, number of VMs snapshots and storage policies. As such save assumption of 20 Mbps per 1000 VMs stored in VSAN was chosen. Assumption is based on following formula:

```text
BW = VSANconst * NumVsanComponentsPerVM * NumVMs * Margin / 5 sec = 9104 (b) * 9 * 1000 * 1,1 / 5 / 1000000 =~ 18 Mbps 
```

Connections detail for VSAN Witness are listed below.

![vsan witness](images/lldSoftwareDefinedNetworks/vsan-witness-net.png)

| Device       | Interface      | MTU            | Traffic Types                       |
|--------------|----------------|----------------|-------------------------------------|
| VSAN Witness | vmk0           | 1500           | Management , VSAN                   |
| ESXi         | vmk0 <br> vmk2 | 1500 <br> 9000 | Management , VSAN Witness <br> VSAN |

Using single interface and sourcing VSAN Witness traffic from Management interface on ESXi will allow using default routing and will eliminate need of providing on whole path MTU greater than 1500 B. VSAN traffic between ESXi hosts will use MTU size of 9000 B. Using two different MTU for VSAN traffic requires ESXi version 6.7U1 and above.

#### DataCenter failure - Disaster Recovery scenario

Disaster recovery scenario assumes that two sites would be built - Primary site and DR site. Both sites would be built separately and with full stack of the components. After build of the primary and DR site there is a need to configure them accordingly via SRM. In this scenario only Customer Workload is protected. But there are some caveats from SDN part which would be identified later in this section.

##### Management Domain

SRM server and vSphere Replication appliances will be deployed in management network.
The management network will communicate with the replication network for management purposes. No replication traffic will be sent via the management network.
To calculate recommended minimum megabits per second (Mbps) throughput, vSphere Replication Calculator must be utilized:
<https://storagehub.vmware.com/t/vsphere-replication-calculators/vsphere-replication-calculator/>

Dedicated network will be created for replication traffic purposes. vSphere Replication appliances will have one interface in management network and the second one in the Replication Network.
The replication network will be available as distributed Port Group in the management and workload domain and it would be terminated on Physical Firewall as i.e. management network.
In the management and workload domain replication network will be available for the ESXi hosts – each ESXi host will have its own VMK interface in Replication Network.
The replication network won’t be connected to any customer VM in any workload domain.

As was mentioned above, both Primary and DR sites would contain full stack of the components, they are requiring different networks as well. So networks mentioned in table [Subnet List for VCS](#subnet-list-for-vcs) are different for Primary site and for DR site. Overall design of the Management domains are the same.

##### Management Domain SDN for DR scenario

![management domain SDN](images/lldSoftwareDefinedNetworks/drManagementDomainSdn.png)

As Primary and DR sites are rarely located inside same Data Center, additional physical network infrastructure should be in place on the DR site (including Physical Firewall or TOR switches).

There are just few components which are required to synchronize between sites. Those components are SRM and Infoblox. Only those components are sending actively traffic between site as shown on below diagram.

##### Management Domain Flows for DR scenario

![management domain flows](images/lldSoftwareDefinedNetworks/drManagementDomainFlows.png)

Based on above drawing it's visible that traffic generated by SRM and IPAM in Management Domain (in Management Network) is going through L2 TOR Switches directly to the physical Firewall (because it's a gateway for such network) where traffic is inspected by FW rule and then routed between sites via Underlay Network which is not part of VCS infrastructure. Additional filter is applied on Physical Firewall on DR site, and then through L2 TOR Switches to corresponding SRM and IPAM components in DR site.

Overall traffic is not tunnelled or encrypted by any network gear between so this requirement is fulfilled by SRM and IPAM software itself.

##### Workload Domain

Customer Workload uses a completely different approach. To easier describe overall design a new term would be presented - "Area". The Area in general contains an Edge Node Cluster and the corresponding T0 Logical Router with linked T0 VRFs as well as numerous T1 Logical Routers and Logical Switches connected to it. The Area covers all additional services that are attached to Logical Routers and the Edge Cluster as well. The number of T1 Logical Routers and Logical Switches is limited by the technology and can be found in [Maximums](#vendor-related-documents) mentioned at the beginning of this document.

Workload Domain in VCS is built based on such Areas. By default, the standard build contains just a single Area, but it can be expanded in case of needs. DR scenario is assuming that there would be three types of Areas:

- Active Area - Area containing active workload and it's actively forming BGP adjacency or taking part of Load Balancing, etc. This Area is fully or partially protected by DR site.
- Passive Area - This Area does not contain any active workload and services due to fact that this Area is isolated from any external interaction (no communication with physical infrastructure because T0 VRF BGP protocol is disabled). It's not recommended to place any workload here.
- Independent Area - This Area construct is used to handle active workload and services but it won't be protected with DR site.

Each workload Domain in DR scenario will have mixture of those to fulfil customer need.

  **IMPORTANT NOTE: Following part of the documentation is saying that Active Area should be reflected 1:1 into Passive Area. There is one exception - T0 VRF Uplinks IPs which should be unique for all sites and areas. It's more common that T0 on DR site will use different VLAN/Network to form BGP adjacency anyway.**

Example shown in below diagram is showing two sites, Primary and DR. Primary site is containing Active Area, where DR is containing Passive Area.

##### Workload Domain SDN for Active-Passive DR scenario

![workload domain SDN](images/lldSoftwareDefinedNetworks/drWorkloadDomainSdnAP.png)

In this diagram, the Primary site is terminating networks Web1, App1 and Db1 as well as Web2, App2 and Db2 on corresponding T1 Logical Routers inside the Active Area, which are reflected one to one on DR site's Passive Area, which means that all protected VMs are placed in each network on primary site. Passive Area on DR site in addition to that, have disabled BGP process. This operation allows to avoid duplication of the IPs and problems in forming BGP neighbourship but at the same time allows to react in case of disaster without taking into consideration preparation of the infrastructure for Customer Workload.

It is possible to protect only i.e. VMs in Web1 and Db1 Logical Switches, but design in this scenario would be identical.

##### Workload Domain SDN for Active-Passive DR Scenario with Independent

Following image is identical to previous scenario but with small adjustment. Here Primary site contains an Independent area which hosts active workload but its not protected by DR site, so the DR site won't reflect this topology.

![workload domain SDN](images/lldSoftwareDefinedNetworks/drWorkloadDomainAP-I.png)

Other example shows a DR scenario with Active-Passive/Passive-Active infrastructure.

##### Workload Domain SDN for Active-Passive/Passive-Active DR scenario

![workload domain SDN](images/lldSoftwareDefinedNetworks/drWorkloadDomainSdnAP-PA.png)

Both Active and DR site would contain their Active Area and Passive Area. This allows to better balance workload. But in this example, it is required to build more Edge Clusters as well as maintaining multiple Logical Routers from both Areas. So Active Area will contain objects which would be actively used on this site. Passive Area will contain only infrastructure but no workload (no VMs attached). This would be mirrored to the DR site, where Active Area from Primary site would be treated as Passive Area and Passive Area from Primary site would be treated as Active Area. This allows Customer to use both sites at the same time when SRM is taking responsibility to protect VMs in case of disaster.

**NOTE: It is very important to understand here that this scenario is not providing option to stretch networks across sites, so network Active in Primary site is Passive on DR. I.e. Web1 cannot be active on Primary and DR site at the same time.**

As it was described previously, Passive area has the BGP protocol disabled to avoid duplication of the IPs and problems in forming BGP neighbourship but at the same time allows to react in case of disaster without taking into consideration preparation of the infrastructure for Customer Workload.

As a summary:
This example has a Primary Site with Active and Passive Areas. DR site has Active and Passive Areas as well. The Active Area in the Primary site is "backed up" by the Passive Area in the DR site, where Active Area from the DR site is "backed up" by the Passive Area in the Primary Site. Each Area has corresponding Edge Clusters, Logical Routers as well as Logical Switches which have been described with numbers to improve understanding.

It is very important to keep in mind that Passive Area should not contain any active workload and Active Area doesn't have to be protected as a whole. Customer may define which VMs from those networks should be protected and which not.

##### Design Decisions - Network Design for Disaster Recovery

| Decision ID | Design Decision                                                                                                             | Design Justification                                                                                                                                                                                                                                                                                                                                                      | Design Implication                                                                                                                                                            |
|-------------|-----------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SDNDR001    | The Management Domain VMs will not be protected using SRM                                                                   | Management Domain on both sites are independent from each other. They can be created separately using same bunch of scripts.                                                                                                                                                                                                                                              | This require additional configuration required for DR purposes as well as some synchronization mechanism or at least process.                                                 |
| SDNDR002    | vSphere Replication appliances will have one interface in management network and the second one in the Replication Network. | This operation is done to avoid using Physical Firewall to route packets related with replication within VCS POD.                                                                                                                                                                                                                                                         | N/A                                                                                                                                                                           |
| SDNDR003    | The replication network won’t be connected to any customer VM in any workload domain.                                       | It is not needed because SRM is gathering data about VMs based on Hosts and vCenter information.                                                                                                                                                                                                                                                                          | N/A                                                                                                                                                                           |
| SDNDR004    | Both Primary and DR sites would contain full stack of the components.                                                       | Build of the primary site and DR site are usually not done simultaneously. It may even happen that site won't be destined for DR in a first place.                                                                                                                                                                                                                        | More management components would be build in total. Sites can be build using same automation. No difference between builds make them simpler to implement.                    |
| SDNDR005    | Only SRM and Infoblox synchronization between sites are needed.                                                             | Due to fact that both sites are build independently, they are managed independently as well, so there is no need to DR protect any Management component. SRM will protect only customer workload. Infoblox will synchronize between sites just for networks and DNS synchronization as there is no other mechanism to allow "recreate" VRA Cloud - Infoblox associations. | Less traffic generated by SRM due to fact that no Management components are DR protected. Synchronization of the Infoblox require additional configuration on Infoblox level. |
| SDNDR006    | Active-Passive/Passive-Active scenario is not providing stretched networks.                                                 | Due to VCF 4.0 limitations from perspective of NSX-T Federations, there is no universal objects possible to use which would allow to stretch virtually single network across sites.                                                                                                                                                                                       | Stretching networks between sites is prohibited. Future versions of the VCF may overwrite this Design Decision.                                                               |
| SDNDR007    | After Disaster occur, switch over to DR site is a manual step.                                                              | Manual steps is required due to fact that decision to switch workload should be very carefully made. Workinstruction have been prepared for that.                                                                                                                                                                                                                         | Manual steps avoid unintentionally switchover to DR                                                                                                                           |
| SDNDR008    | Synchronization of the SDN configuration between Primary and DR sites should be done manually                               | Synchronization of the SDN configuration (DWF rules,Logical Routers configuration, Logical Switches configuration, NAT, VPN, LB, DHCP, Routing, etc.) should be done to avoid any issues after disaster occur.                                                                                                                                                            | Manual actions required.                                                                                                                                                      |

## Multi-tenancy

##### Summary

Multitenancy has been partially described in scalability section, where couple designs has been presented for multi tenant solutions. Separation of Customer Workload is done in Routing, where each Tenant/Customer have dedidcated VRF and dedicated uplink VLAN(s), and dedicated T1 created on Project level.  
All segments, FW rules and NAT rules need to be created on Project level. Any rule created in FW ruleset will introduce full separation, this means any rule created on "Project A" level will not have impact on any other Project.  
  
It is important to understand that Projects are function that is enabled in NSX-T by default, if no new Project is created all entities are part of Default Project, Default Project do not have limitations mentioned for Projects in this document.  
  
In case of using Projects and allowing Customer to have access to NSX-T, access needs to be limited in scope only to Project.  
It is imporatant to understand that following services can be configured from Project level: network segment , T1 router , DFW rule , NAT rule.  
  
![MT Architecture Overview](images/lldSoftwareDefinedNetworks/MT-arch.svg)  
  
One of the new features introduced in NSX-T 4.0 is Projects. Projects is an easy way to limit the scope of items that can be manipulated (read/modify/create) both using GUI or API. Any item created inside Project will be visible only for an authenticated user who is allowed to operate on Project level. On Project level the same well known NSX-T RBAC roles can be used but with scope of Project items only. Following items can be entitled to Project: T1 logical router , OTZ network segment, NAT rule , firewall rule.  
DFW rules created on Project level will be applied only to VMs attached to network segments that are part of particular Projects.  
At current NSX-T version that is 4.1.2.1 it is possible only to create OTZ network segment and only using Overlay Transport Zone assigned to Project.  
It is not possible to modify any segment profile on Project level, but it is possible to do it at Default Project level and share it with Project to be used.  
The same way DHCP service is working, it is not possible to create DHCP server and pools on Project level, but it is possible to do it at Default Project level and share it with Project.<br>
One of limitation using Project is lack of supporting standard Load-Balancer, only Advanced Load-Balancer can be used when working with Projects.  
Any subnet that will be used inside Projects that need to advertised by T0 routers needs to be part of pool specified to Project.  
On each Project it is possible to create Quota limiting number of T1, network segments, FW rules and VPCs that can be created in Project.  
  
VPC is another new feature introduced in NSX-T 4.1.2. VPC can be created only inside Projects. VPC can provide additional traffic isolation inside Project and limiting scope of items that can be manipulated only to items created inside VPC. Following items can be created as part of VPC, OTZ network segment, NAT rules, FW rules.  
It is important to understand that VPC at current NSX-T version that is 4.1.2.1 can not use T1 router, VPC is directly connected to T0 router, similar to Projects, if VPC segment need to be advertised by T0 router, it needs to belong to a VPC defined pool.  

##### Design Decisions - Multi-tenancy

| Decision ID | Design Decision                                                                              | Design Justification                                                                                             | Design Implication                                                                                              |
|-------------|----------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------|
| MULDD001    | Shared Cluster, all Customers will be hosted on single VCS cluster                           | Business decision, lowering cost, presenting as Entry Level scenario, with limited functions                     | DFW rules need to be applied to Group, dedicated T0 or VRF-Lite per Customer needed to allow routing separation |
| MULDD002    | DFW Policy will be applied per Tenant                                                        | DFW Policy is method of grouping DFW Rules together, grouping all DFW Rules together will easy maintainging them | APPLY TO options is mandatory to use                                                                            |
| MULDD003    | T0 VRF will be created for every Customer, as every POD will be deployed as Multi Tenant POD | standard POD design                                                                                              | T0 VRF will be used in every POD                                                                                |

##### Management Network

For Multi-tenancy solution Management Network will use standard /24 for all 3 managements networks.

##### Transport Zone

No dedicated Transport Zone per Customer will be created. Using Projects in NSX-T 4.1.x is not allowing to use different than default OTZ.

##### VRF-Lite Summary

VRF-Lite (VRF without MPLS) is new function introduced in NSX-T 3.0. It creates additional 2 VRF's (Service Router and Distributed Router) on Edge Transport Node. VRF is tied to T0 router and inherit from it all attributes including ETN attributes, Edge Cluster attributes, BGP attributes.

Using VRF-Lite feature supports up to 100 VRF per single Edge Cluster and T0 router.

VRF-Lite will be treated as normal T0 router peering with Customer WAN router, and allowing communication with Customer T1 router that connects Customer T1 networks, that allowing to keep original design with minimizing management overhead both in IP addresses and vSphere resources.

VRF-Lite list of inherited attributes from parent T0 that can not be changed:

- Edge Cluster
- HA mode
- BGP local AS
- BGP GRES settings

##### VRF-Lite Limitation

VRF-Lite new function introduced in NSX-T 3.0 has following limitation, that exclude use this option for some of the Customers.

- No Load-Balancing possible on T0 level
- No VPN possible T0 level
- No different BGP AS possible (all BGP attributes including AS are inherit from T0 router)
- Static routing not supported if NAT used
- Shared BW

As such all IP services need to be confiugured on dedicated customer T1 logical router.

##### WAN Connection

Any North-South connection from VCS required unique uplink VLAN. WAN connection will not be shared between Customers, and it is not possible that two or more Customers will share the same VLAN-id for uplink connection.

##### Distributed FW

Distributed FW is security mechanism that inspect traffic that is inbound and outbound for each Virtual Machine, using VM Manager process, allow this ESXi to work as native transparent (L2) firewall. Allowing this for separation on L2 segment level.

Rules in ruleset will be created manually as per VCS Software Defined Network LLD. Each rules match conditions for source and destination IP addresses will be using Security Groups only. No other object will be allowed. Security Group membership will be IP based only.

Because DFW works for entire ESXi Cluster, to alow separation and prohibit any interaction between rules dedicated for different Customers. Every Customer will have dedicated Policy (it is allowed to create more than one NSX_T DFW Policy per Customer Tenant) called also Section, under Policy apply to option will be used limiting specific Policy only to specific NSX-T Security Group. NSX-T DFW Policy will be created during Tenant bring up using ansible automation, this will be done automatically, during this process new NSX-T Security Group will be created , consisting of all Customers Network Segments. Membership of this Security Group will be dynamic based on NSX-T tags. When new Network Segment  will be crated using ansible automation correct NSX-T tag will be added making this segment memeber of this Security Group and as such will allow  all FW Rules from Customer Policy to be applied to all VMs from this Network Segment.

In NSX-T Each Customer will have one or more Policy with security rules. Policy naming convention < customerCode > - < policyName > **For e.g.** `nx2 - webapplications`. Multiple Policies will make rule base more manageable, all policies should be put in alphabet order to be human readable.

Using apply to function on Policy level prohibits using this function on Rule level, this mean that all DFW Rules from Policy will be aplied to all Virtul Machines that will be member of NSX-T Security Group sepcified in apply to field. If bigger granurality is needed additional NSXT DFW Policies per Customer can be created.

##### Network ports that must be open on Site Recovery Manager and vSphere Replication Protected and Recovery sites

| Firewall Rule Name                                                                                                                                                                                                   | Source                       | Destination                                                       | Service  | Action |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------|-------------------------------------------------------------------|----------|--------|
| From the ESXi host at the protected site to the vSphere Replication appliance at the recovery site                                                                                                                   | ESXi host                    | vSphere Replication appliance on the recovery site                | TCP31031 | Allow  |
| Initial and outgoing replication traffic from the ESXi host at the source site to the vSphere Replication appliance or vSphere Replication server at the target site for replication traffic with network encryption | ESXi host on the source site | vSphere Replication server at the target site                     | TCP32032 | Allow  |
| Management traffic between Site Recovery Manager instances and vSphere Replication appliances                                                                                                                        | Site Recovery Manager        | vSphere Replication appliance on the recovery and protected sites | TCP8043  | Allow  |

# Abbreviations and Definitions

| Abbreviation / term: | Translation                                 |
|----------------------|---------------------------------------------|
| ABX                  | VMware Action Based Extensibility           |
| AD                   | Active Directory                            |
| API                  | Application Programming Interface           |
| AS                   | Autonomous System                           |
| AZ                   | Availability Zone                           |
| BGP                  | Border Gateway Protocol                     |
| BU                   | Business Unit                               |
| CAS                  | VRA Cloud                                   |
| BIL                  | Billing                                     |
| DEB                  | Debian Repository                           |
| VCS                  | VMware Cloud Services                       |
| DNS                  | Domain Name System                          |
| dPG                  | Distributed Port Groups                     |
| DR                   | Disaster Recovery                           |
| ECMP                 | Equal Cost Multi Path routing               |
| GIT                  | Global Information Tracker                  |
| GR                   | Graceful Restart                            |
| GUI                  | graphical user interface                    |
| HA                   | High Availability                           |
| HLD                  | High Level Design                           |
| ICA                  | International Certification Authority       |
| IDM                  | Identity Manager                            |
| KMS                  | Key Management Service                      |
| LB                   | Load Balancer                               |
| LCM                  | Live Cycle Manager                          |
| LLD                  | Low Level Design                            |
| LR                   | Logical Router                              |
| LS                   | Logical Switch                              |
| MID                  | Management Instrumentation Discovery Server |
| NAT                  | Network Address Translation                 |
| NIOC                 | Network I/O Control                         |
| NSX\-T               | NSX Transformer                             |
| NTP                  | Network Time Protocol                       |
| n\-VDS               | NSX\-T Virtual Distributed Switch           |
| PSC                  | Platform Services Controller                |
| RTT                  | Round Time Travel                           |
| SDDC                 | Software Defined DataCenter                 |
| SDN                  | Software Defined Networking                 |
| SR                   | Service Router                              |
| T0                   | Tier 0 Logical Router                       |
| T1                   | Tier 1 Logical Router                       |
| TN                   | Transport Node                              |
| TSS                  | Terminal Server                             |
| TZ                   | Transport Zone                              |
| VCF                  | VMware Cloud Foundation                     |
| VCS                  | vCenter Server                              |
| VDS                  | vSphere Distributed Switch                  |
| VMK                  | VMKernel                                    |
| VROPS                | VMware vRealize Operations                  |
| vSAN                 | VMware solution for Storage                 |
| VTEP                 | Virtual Tunnel Endpoint                     |
| VVD                  | VMware Validated Design                     |
| WUS                  | Windows Update Server                       |
