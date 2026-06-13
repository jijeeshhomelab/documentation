# NSX TLS Inspection

## Table of Contents

- [NSX TLS Inspection](#nsx-tls-inspection)
  - [Table of Contents](#table-of-contents)
  - [List of Changes](#list-of-changes)
  - [Introduction](#introduction)
    - [Purpose](#purpose)
    - [Audience](#audience)
    - [Scope](#scope)
  - [Prerequisites](#prerequisites)
  - [Instructions](#instructions)
    - [External Decryption Implementation](#external-decryption-implementation)
    - [Internal Decryption Implementation](#internal-decryption-implementation)

## List of Changes

| Version | Date       | Author       | Issue | Changes           |
|---------|------------|--------------|-------|-------------------|
| 0.1     | 2024-04-29 | Adrian Baciu |       | Document creation |

## Introduction

### Purpose

Create documentation for NSX TLS Inspection step-by-step implementation.

### Audience

This document is intended for Atos Cloud Services Engineers and Architects responsible for implementing and managing NSX TLS Inspection.

### Scope

This document covers NSX TLS Inspection implementation instructions.

## Prerequisites

These prerequisites are valid for TLS Inspection in policies.
Activate the following settings. By default, they are deactivated.
- Activating TLS Inspection settings per gateway.
Navigate to Security > TLS Inspection and select the Settings tab. Select a gateway or gateways from the list of TLS-enabled gateways and click Turn On.
- Activating URL Database on the Edge cluster.
Navigate to Security > General Settings > URL Database. Edge nodes must have direct Internet connectivity (no proxy supported) so the NSX Threat Intelligence Cloud Service (NTICS) can complete URL database downloads.
- To view TLS Inspection statistics using the Security dashboard, deploy NSX Application 
Platform on your NSX 3.2 or later environment and ensure it is in a good state. A specific license is required for time-series monitoring.
- Additional license required.
- Tested on DHC test environment VX8 NSX version 3.2.2.1.0.21487560.

## Instructions

Majority of interent traffic is TLS encrypted.
TLS encryption can hide malware, concealed data theft or mask data leakage of sensitive information.
TLS Inspection is used to detect and prevent advanced threats over encrypted TLS channels. TLS Inspection transparently decrypts encrypted traffic and makes it available for advanced security 
features such as IDS/IPS, Malware Prevention, and URL Filtering.
TLS Inspection support includes:
- Support on tier-1 gateways only.
- Support for TLS version 1.0, 1.1, and 1.2 with TLS 1.2 with Perfect Forward Secrecy (PFS). 
If version 1.3 is used, the NSX proxy negotiates to an earlier version and establishes a connection.
- Leverages TLS Server Name Indication (SNI) in TLS client hello to classify the traffic.
- Visibility into encrypted traffic without offloading while retaining end-to-end encryption.
- TLS decryption on gateway firewalls to intercept the traffic and decrypt it to feed to the advanced firewall security features.
- TLS Inspection policies to create a set of rules that describe conditions to match and perform a predefined action.
- The TLS Inspection policy rules support the bypass, external, and internal decryption action profiles.

The following diagram depicts how the traffic is handled by the TLS internal and external decryption types.

![TLS Diagram](./images/wiTLSInspection/diagram_1.PNG)


TLS Decryption Types
TLS Inspection feature allows two types of decryption:
- Internal TLS Decryption - for traffic going to an Enterprise internal service where you own the service, certificate, and the private key. This is also called TLS reverse-proxy or inbound decryption.
- External TLS Decryption - for traffic going to an external service (Internet) where the Enterprise does not own the service, its certificate, and the private key. This is also called TLS forward proxy or outbound decryption.

### External Decryption Implementation

This topic provides the steps to configure an external decryption action profile.
Prerequisites
- Have the correct user role and permissions to set up TLS Inspection.
- Have a Trusted Proxy CA and Untrusted Proxy CA certificate imported or ready to be imported or have the related information to generate a certificate.

How External Decryption works - diagram:

![TLS Diagram Ext](./images/wiTLSInspection/diagram_2.PNG)


1. Log in to NSX Manager with administrator rights and navigate to Security - TLS Inspection - Quick Start and select the gateway where TLS Inspection policy will be applied:

   ![Step 1](./images/wiTLSInspection/step_1.PNG)
   
   ![Step 1](./images/wiTLSInspection/step_1_1.PNG)

2. Choose a name for the policy and type - external decryption.
   
   ![Step 2](./images/wiTLSInspection/step_2.PNG)

3. Add rules to the policy - select Default - click view/customize sites and leave defaults.
   
   ![Step 3](./images/wiTLSInspection/step_3.PNG)

4. Add Proxy CA certificate - here we can generate a new one or use an existing one.
   
   ![Step 4](./images/wiTLSInspection/step_4.PNG)

5. Review and publish.
   
   ![Step 5](./images/wiTLSInspection/step_5.PNG)
   ![Step 5](./images/wiTLSInspection/step_5_1.PNG)

6. Ensure that TLS Inspection is turned on on your gateway.
   
   ![Step 6](./images/wiTLSInspection/step_6.PNG)

7. On the VM from where we test TLS Inspection, add the certificate to trusted root certificate authorities.
   
   ![Step 7](./images/wiTLSInspection/step_7.PNG)

8. Open a browser and a website - on the certificate, you will see the custom certificate that we've created, not the website's original certificate (can be checked by turning off the TLS Inspection).
   
   ![Step 8](./images/wiTLSInspection/step_8.PNG)

9. Log in to the active edge node to see the TLS Inspection stats.
 
   Issue the commands:
   get tls-inspection
   get tls-inspection traffic-stats lr-uuid yourlruuidlistedbypreviouscommand
   
   ![Step 9](./images/wiTLSInspection/step_9.PNG)

10. You can review/change the decryption action profile.
 
   ![Step 10](./images/wiTLSInspection/step_10_1.PNG)
   ![Step 10](./images/wiTLSInspection/step_10_2.PNG)
   ![Step 10](./images/wiTLSInspection/step_10_3.PNG)

### Internal Decryption Implementation

This topic provides the steps to configure an internal decryption action profile manually.
Prerequisites
- Have the correct user role and permissions to set up TLS Inspection.
- Have an internal server certificate imported or ready to be imported or have the related 
information to generate the certificate.

How Internal Decryption works - diagram:

![TLS Diagram Int](./images/wiTLSInspection/diagram_3.PNG)

1. The first step is the same as for creating the external one, then on the second step choose a name for the policy and type - Internal decryption.

   ![Step 1](./images/wiTLSInspection/step_i1.PNG)

2. Specify the FQDN you want to inspect (press enter then you'll have the Match Certificate button active). Press Match certificates and select the proper certificates.

   ![Step 2](./images/wiTLSInspection/step_i2.PNG)

3. Review and publish. Make sure also that TLS Inspection is turned on on your gateway (TLS Inspection - Settings).

   ![Step 3](./images/wiTLSInspection/step_i3.PNG)
   ![Step 3](./images/wiTLSInspection/step_i3_1.PNG)
