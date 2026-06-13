# Work Instruction for VMware Skyline Advisor Implementation

# Changelog
  
| Version | Date       | Description              | Author       |
| ------- | ---------- | ------------------------ | --------------- |
| 0.1     | 20/06/2024 | Initial version creation | Radoslaw Dabrowski |

## Table of Contents

- [Work Instruction for VMware Skyline Advisor Implementation](#work-instruction-for-vmware-skyline-advisor-implementation)
- [Changelog](#changelog)
  - [Table of Contents](#table-of-contents)
  - [Introduction](#introduction)
    - [Purpose](#purpose)
    - [Audience](#audience)
    - [Scope](#scope)
    - [Prerequisites](#prerequisites)
    - [Appliance Requirements](#appliance-requirements)
      - [Appliance Resource Requirements](#appliance-resource-requirements)
      - [Software Requirements](#software-requirements)
      - [Network Connectivity Requirements](#network-connectivity-requirements)
  - [Installation Procedure](#installation-procedure)
    - [Download the Skyline Collector](#download-the-skyline-collector)
    - [Deploy the Skyline Collector OVA](#deploy-the-skyline-collector-ova)
    - [Prepare DNS for Skyline Collector](#prepare-dns-for-skyline-collector)
    - [Set Up Skyline Collector](#set-up-skyline-collector)
  - [Configuration](#configuration)
    - [Log In to Skyline Collector](#log-in-to-skyline-collector)
    - [Preparation of the Products to Handle Read-Only Accounts](#preparation-of-the-products-to-handle-read-only-accounts)
      - [NSX-T](#nsx-t)
      - [LogInsight](#loginsight)
      - [VRA](#vra)
      - [SDDC Manager](#sddc-manager)
    - [Add VMware Products to Skyline Collector](#add-vmware-products-to-skyline-collector)
    - [Alternative Method to Add VMware Products to Skyline Collector](#alternative-method-to-add-vmware-products-to-skyline-collector)

## Introduction

### Purpose

Install and configure VMware Skyline Collector to integrate it with VMware Skyline Advisor.

### Audience

This document is intended for system administrators, IT professionals, and users responsible for managing VMware environments.

### Scope

This document covers the entire process from downloading the Skyline Collector to troubleshooting common issues.

### Prerequisites

- VMware account permissions
- Access to the VMware vSphere environment
- Internet connectivity for downloading the Skyline Collector

### Appliance Requirements

#### Appliance Resource Requirements

To ensure optimal performance, your system should meet the following minimum hardware requirements:

- **Number of vCPUs**: 2
- **Memory**: 8 GB
- **Disk Space**: 87 GB (1.1 GB initial if thin provisioned)

#### Software Requirements

- **vCenter Server**: 5.5 or later
- **ESXi**: 5.5 or later
- **NSX for vSphere**: 6.1 or later (Configuration of NSX is optional)

#### Network Connectivity Requirements

| Reason                  | Source IP                          | Destination IP                                               | Port(s)               | Apply To                                                                            |
|-------------------------|------------------------------------|-------------------------------------------------------------|-----------------------|-------------------------------------------------------------------------------------|
| Skyline to All HTTPS    | \<custAbbr\>seg081 (\<localRegionNetwork\>.56) | \<custAbbr\>seg035, \<custAbbr\>seg002, \<custAbbr\>seg067, \<custAbbr\>seg034, \<custAbbr\>seg008, \<custAbbr\>seg005, \<custAbbr\>seg032, \<custAbbr\>seg020, \<custAbbr\>seg003, \<custAbbr\>seg014, \<custAbbr\>seg013 | HTTPS                 | \<custAbbr\>seg035_APPLYTO, \<custAbbr\>seg002_APPLYTO, \<custAbbr\>seg067_APPLYTO, \<custAbbr\>seg034_APPLYTO, \<custAbbr\>seg008_APPLYTO, \<custAbbr\>seg005_APPLYTO, \<custAbbr\>seg032_APPLYTO, \<custAbbr\>seg020_APPLYTO, \<custAbbr\>seg003_APPLYTO, \<custAbbr\>seg014_APPLYTO, \<custAbbr\>seg013_APPLYTO, \<custAbbr\>seg081_APPLYTO (match by name which begins with \<customerLocation\>sky) |
| Skyline to AD           | \<custAbbr\>seg081 (\<localRegionNetwork\>.56) | \<customerAbbr\>seg006                                      | DNS TCP, DNS UDP      | \<customerAbbr\>seg006_APPLYTO, \<custAbbr\>seg081_APPLYTO (match by name which begins with \<customerLocation\>sky)      |
| Skyline to Proxy        | \<custAbbr\>seg081 (\<localRegionNetwork\>.56) | \<customerAbbr\>seg001                                      | TCP3128               | \<customerAbbr\>seg001_APPLYTO, \<custAbbr\>seg081_APPLYTO (match by name which begins with \<customerLocation\>sky)      |
| Skyline to VRLI         | \<custAbbr\>seg081 (\<localRegionNetwork\>.56) | \<customerAbbr\>seg005                                      | TCP9543               | \<customerAbbr\>seg005_APPLYTO, \<custAbbr\>seg081_APPLYTO (match by name which begins with \<customerLocation\>sky)      |
| TSS to Skyline          | \<customerAbbr\>seg004             | \<custAbbr\>seg081 (\<localRegionNetwork\>.56)                | HTTPS                 | \<customerAbbr\>seg004_APPLYTO, \<custAbbr\>seg081_APPLYTO (match by name which begins with \<customerLocation\>sky)      |

## Installation Procedure

### Download the Skyline Collector

1. Visit the VMware Skyline Collector [download page](https://support.broadcom.com/group/ecx/productdownloads?subfamily=VMware%20Skyline%20Collector).
2. Log in with your VMware/Broadcom account credentials.
3. Agree to the Terms and Conditions.
4. Download the Skyline Collector OVA file.

### Deploy the Skyline Collector OVA

1. Log in to your desired VCS's vSphere Client.
2. In the vSphere Client, select **Deploy OVF Template**.
3. Browse to the location of the downloaded Skyline Collector OVA file and select it.
4. Follow the wizard to complete the deployment. Steps include:
   - Select a name and location for the Skyline Collector VM (\<LocationCode\>sky001).
   - Choose a compute resource (Management Cluster).
   - Review details.
   - Accept the license agreements.
   - Select storage and disk format (Management Cluster VSAN).
   - Configure the network settings (lreg-m01-seg01 and IPv4).
   - Customize template:
     - Root password - store it in Hashivault afterwards.
     - Default Gateway - \<localRegionNetwork\>.1.
     - Domain name.
     - Domain Search Path.
     - Domain Name Servers - \<managementNetwork\>.24, \<managementNetwork\>.25.
     - Network 1 IP address - \<localRegionNetwork\>.56.
     - Network 1 Netmask - 255.255.255.0.
   - Review and finish the deployment.

### Prepare DNS for Skyline Collector

1. Log in to the DNS on VCS AD.
2. Navigate to Forward Lookup Zones.
3. Create a new Host (A) entry:
   - Name - \<LocationCode\>sky001.
   - IP address - \<localRegionNetwork\>.56.
   - Create associated pointer (PTR) record - checked.

### Set Up Skyline Collector

1. Once the VM is deployed, power on the Skyline Collector VM.
2. Open a web browser and navigate to the IP address of the Skyline Collector (https://\<localRegionNetwork\>.56).
3. Use default credentials to log in (login: admin, password: default).
4. Follow the setup wizard to configure the Skyline Collector:
   - Register Collector Token (token to be obtained from Skyline Advisor).
   - Hostname Verification - leave enabled.
   - Proxy - set enabled:
     - IP address - \<localRegionNetwork\>.38.
     - Port - 3128.
     - Authentication - disabled.
   - Click register.
   - Friendly Name - \<CustomerName\> and/or \<locationName\>.
   - Add Product:
     - Select next to the vCenter Server button Add.
       - FQDN - type VCS001 FQDN.
       - Account Username - vcs03 service account.
       - Account Password - vcs03 service account's password.
     - Add second vCenter:
       - FQDN - type VCS002 FQDN.
       - Account Username - vcs03 service account.
       - Account Password - vcs03 service account's password.
     - Add other components (NSX-T, LCM, VCF, LogInsight, VROPS) - if not done at this stage, refer to the section "Add VMware Products to Skyline Collector".
   - Auto-Upgrade:
     - Enable Auto-Upgrade - Set to Yes.
     - Leave other parameters default.
   - Accept the conditions.

## Configuration

### Log In to Skyline Collector

1. Access the Skyline Collector web interface using the IP address of the Skyline Collector.
2. Log in with the admin credentials created during the setup.

### Preparation of the Products to Handle Read-Only Accounts

#### NSX-T

1. Log in to NSX-T.
2. Navigate to System -> Settings -> User Management -> User Role Assignment.
3. Click **Add** and then **Role Assignment for VIDM**.
4. Define **VCS03** Service Account in the **Search User/User Group** field.
5. Under Roles, define **Auditor**.
6. Confirm.

#### LogInsight

1. Log in to Log Insight as an admin.
2. Navigate to Management -> Access Control -> Users and Groups.
3. Click on **New User**.
4. Authentication, change to **VMware Identity Manager**.
5. Domain - type VCS domain.
6. Username - type VCS03 account.
7. Roles - select **View Only Admin**.
8. Confirm.

#### VRA

1. Log in to VRA using an account with sufficient privileges to modify accounts.
2. Navigate to Identity & Access Management -> Active Users.
3. Search for the VCS03 account.
4. Select VCS03 account and click **Edit Roles**.
5. Click **Add Services** below **Assign Service Roles**.
6. From the list, pick **Assembler** with Roles: **Assembler Viewer** and **Assembler Administrator**.
7. Confirm.

#### SDDC Manager

1. Log in to SDDC Manager.
2. Navigate to Administration -> Single Sign On.
3. Click on **+ User or Group**.
4. Specify the VCS03 account.
5. Select VCS03 account and set Role to **VIEWER**.
6. Click **Add**.

### Add VMware Products to Skyline Collector

1. Navigate to the **Configuration** tab.
2. Click **Add Product**.
3. Select the VMware product you want to monitor (e.g., vCenter Server).
4. Enter the required details such as IP address, username, and password.
5. Follow the prompts to add the product to Skyline Collector.

### Alternative Method to Add VMware Products to Skyline Collector

Prepare a CSV file with the following format. Column "thumbprint" can be left empty. Replace the variables with appropriate values for the given objects.

| bulkOperationType | productType | hostName                          | username                          | password                          | thumbprint |
|-------------------|-------------|-----------------------------------|-----------------------------------|-----------------------------------|------------|
| UPDATE/CREATE (choose one)     | OPLOGS      | \<locationCode\>vli001.\<domainName\> | \<VCS003 Service Account\>@\<domainName\>@@vIDM | \<VCS003 Service Account Password\> |            |
| UPDATE/CREATE (choose one)    | VSPHERE     | \<locationCode\>vcs001.\<domainName\> | \<VCS003 Service Account\>@\<domainName\> | \<VCS003 Service Account Password\> |            |
| UPDATE/CREATE (choose one)    | VSPHERE     | \<locationCode\>vcs002.\<domainName\> | \<VCS003 Service Account\>@\<domainName\> | \<VCS003 Service Account Password\> |            |
| UPDATE/CREATE (choose one)    | NSX_T       | \<locationCode\>nsx001.\<domainName\> | \<VCS003 Service Account\>@\<domainName\> | \<VCS003 Service Account Password\> |            |
| UPDATE/CREATE (choose one)    | NSX_T       | \<locationCode\>nsx002.\<domainName\> | \<VCS003 Service Account\>@\<domainName\> | \<VCS003 Service Account Password\> |            |
| UPDATE/CREATE (choose one)    | OPERATIONS  | \<locationCode\>ops001.\<domainName\> | \<OPS001 Username\>               | \<OPS001 Password\>               |            |
| UPDATE/CREATE (choose one)    | VCF         | \<locationCode\>sdm001.\<domainName\> | \<VCS003 Service Account\>@\<domainName\> | \<VCS003 Service Account Password\> |            |
| UPDATE/CREATE (choose one)     | LIFECYCLE   | \<locationCode\>lcm001.\<domainName\> | vcfadmin@local                    | \<vcfadmin@local Password\>       |            |
| UPDATE/CREATE (choose one)    | AUTOMATION  | \<locationCode\>vra001.\<domainName\> | \<VCS003 Service Account\>        | \<VCS003 Service Account Password\> |            |

To import this table into Skyline Collector:

1. Log in to Skyline Collector.
2. Prepare the intake CSV form.
3. Navigate to Configuration -> Bulk Product Operations -> Import & Update Products.
4. Click on **Select a file**.
5. Pick the CSV file.
6. Click on **Confirm Upload**.
7. Click on **Execute Operations**.
8. Validate the success of the operations.
