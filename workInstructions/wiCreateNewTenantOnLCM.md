# Work Instruction: Tenant Creation in vRealize Suite LCM

## Table of Contents

- [Changelog](#changelog)
- [Introduction](#introduction)
  - [Purpose](#purpose)
  - [Scope](#scope)
  - [Audience](#audience)
- [Prerequisites](#prerequisites)
- [Tenant Creation Procedure](#tenant-creation-procedure)
  - [Step 1: Access vRealize Suite Lifecycle Manager (LCM)](#step-1-access-vrealize-suite-lifecycle-manager-lcm)
  - [Step 2: Navigate to Tenant Management](#step-2-navigate-to-tenant-management)
  - [Step 3: Add Tenant - Basic Details](#step-3-add-tenant---basic-details)
  - [Step 4: Configure Directory Details](#step-4-configure-directory-details)
  - [Step 5: Product Association](#step-5-product-association)
  - [Step 6: Precheck](#step-6-precheck)
  - [Step 7: Summary](#step-7-summary)
- [Common Errors and Troubleshooting](#common-errors-and-troubleshooting)
  - [IDM Sync Failure](#idm-sync-failure)

## Changelog

| Date | Description | Author |
|------|-------------|--------|
| 2025-10-16 | Initial creation of Tenant Creation WI | Aparna Kadam    |
| 2025-12-19 | VCS-17766 idm sync issue solution      | Martin P Mathew |

---

## Introduction

### Purpose

This document provides step-by-step instructions for creating a new tenant in **vRealize Suite Lifecycle Manager (LCM)**.  
It serves as a reference for cloud administrators performing multi-tenancy setup in VMware Aria Automation (vRA) on-premises environments.

### Scope

Covers all configuration parameters, input validation, and verification steps needed for tenant creation.

### Audience

- VMware Automation Administrators  
- DevOps Consultants working on vRA multi-tenancy setup  

## Prerequisites

| Prerequisite | Screenshot | Validation |
|--------------|------------|------------|
| Access to vRealize Suite LCM with admin privileges | ![LCM Login Page](images/newTenantCreationViaLCM/lcmlogin.png) |  Log in successfully using admin credentials. Verify dashboard loads. |
| Verify Multitenancy is enable | ![LCM Tenant Management page](images/newTenantCreationViaLCM/isMultitenancyEnable.png) |  Open Tenant Management Page. Verify page loads. Add Tenant button is enabled. |
| DNS entries for Tenant FQDN and IDM | ![DNS Record Example](images/newTenantCreationViaLCM/addtenanthosttolcm.png) <br> ![DNS Record Example](images/newTenantCreationViaLCM/addenanthosttovra.png) |  Add two A records: Ex. `demotenant.gre42vra001.nx4dhc01.next` → vRA IP, Ex. `demotenant.nx4dhc01.next` → IDM IP <br> [ ] Verify with `nslookup <tenant-fqdn>` and `ping <tenant-fqdn>` |
| Wildcard SSL certificate for Ex. `*.gre42vra001.nx4dhc01.next`,<br>`*.gre42vra002.nx4dhc01.next`,<br>`*.gre42vra003.nx4dhc01.next`,<br>`*.gre42vra004.nx4dhc01.next` | ![Wildcard Certificate](images/newTenantCreationViaLCM/certificate.png) |  Verify CN/SAN matches. <br>  Ensure certificate is active/trusted in LCM <br>   Test HTTPS for tenant FQDN(s) <br>  Verify expiration date is valid |

✅ **Notes:**  

- Ensure firewall rules allow LCM to communicate with all vRA components.  
- Validate certificate expiration and trust chain before proceeding.  
- Confirm administrative credentials have sufficient privileges for tenant creation.

## Tenant Creation Procedure

### Step 1: Access vRealize Suite Lifecycle Manager (LCM)

1. Log in to **vRealize Suite Lifecycle Manager** using admin credentials: `https://<lcm-fqdn>/vrlcm`
2. Navigate to **Identity and Tenant Management** → **Tenant Management**.

![Screenshot: LCM Home Page](images/newTenantCreationViaLCM/lcmlogin.png)

### Step 2: Navigate to Tenant Management

1. Click **Tenant Management** → **Add Tenant**.
2. The *Create Tenant Wizard* opens.

![Screenshot: Add Tenant Button](images/newTenantCreationViaLCM/isMultitenancyEnable.png)

### Step 3: Add Tenant - Basic Details

![Screenshot: Tenant Details Page](images/newTenantCreationViaLCM/addtenantstep1.png)

| Field | Description |
|--------|--------------|
| Tenant Name | Unique tenant system ID (lowercase, no spaces) |
| User Name | Create Administrator user for new Tenant|
| First Name | First Name of User|
| Last Name | Last Name of User  |
| EmailID | Provide email ID |
| Password | Provide Administrator password.|

Fill in the required details, and the **NEXT** button will appear.  
**Store the new tenant administrator credentials on HashiVault.**

![Screenshot: Tenant Details Page](images/newTenantCreationViaLCM/addtenantstep2.png)

Click on **NEXT** Button.

### Step 4: Configure Directory Details

![Screenshot: Tenant Directory Details](images/newTenantCreationViaLCM/addtenantstep3.png)

Select the directory that you want to attach to the tenant. and fill the Bind and Administrator user credentials.

![Screenshot: Tenant Directory Details](images/newTenantCreationViaLCM/addtenantstep4.png)

Click on **Validate** Button which will validate the credentials.

![Screenshot: Tenant Directory Details](images/newTenantCreationViaLCM/addtenantstep5.png)

Click on **Save and NEXT**.

### Step 5: Product Association

Select the product that you want to associate.

![Screenshot: Tenant Directory Details](images/newTenantCreationViaLCM/addtenantstep6.png)

Click on **SAVE AND Next**

### Step 6: Precheck

![Screenshot: Tenant Directory Details](images/newTenantCreationViaLCM/addtenantstep7.png)

Click on **RUN Precheck**

![Screenshot: Tenant Directory Details](images/newTenantCreationViaLCM/addtenantstep8.png)

Click on **SAVE AND Next**

### Step 7: Summary

![Screenshot: Tenant Directory Details](images/newTenantCreationViaLCM/addtenantstep9.png)

Review all entered details before proceeding.  
Click **Create Tenant** to initiate provisioning.

![Screenshot: Tenant Directory Details](images/newTenantCreationViaLCM/addtenantstep10.png)

## Common Errors and Troubleshooting

| Issue | Cause | Resolution |
|--------|--------|-------------|
| Certificate requirement does not conform | Invalid or mismatched tenant FQDN certificate | Ensure wildcard certificate `*.gre42vra001.nx4dhc01.next` is applied |
| Failed to read HTTP message | Malformed payload or API body | Validate JSON structure and content-type |
| Tenant not visible in list | LCM service cache not updated | Refresh or restart LCM service |
| LDAP Bind Failed | Invalid credentials or network | Verify credentials, ports, LDAP configuration |

### IDM Sync Failure

This sync issue occurs because required user attributes (e.g., first name, last name, email) are not configured in Active Directory.

Login to tenant **VRA** Page and cross check the members in **Enterprise Groups** under **Identity and Access Management**.  
Note: in the below image, the total member count is zero.  

![Screenshot: IDM Sync Failure](images/newTenantCreationViaLCM/idmSyncIssue.png)

If you encounter a sync failure, follow the steps below:

Login to the web page `https://<tenant name>.<domain name>/` using tenant administrator credentials.  
For example: `https://demotenant.nx4dhc01.next/`.  
After a successful login, navigate to the top right corner and select **Administration Console** as shown in the screenshot below.

![Screenshot: Navigate to Administration Console](images/newTenantCreationViaLCM/gotoAdminConsole.png)

Select **Identity and Access Management** and click on **Setup** on the right side.  
From that page, select the **User Attributes** section and uncheck the **Required** checkbox near first name, last name, email (see the screenshot below).

![Screenshot: Uncheck User Attributes](images/newTenantCreationViaLCM/uncheckUserAttribute.png)

After saving the configuration, click the **Manage** button on the right side under **Identity and Access Management**.  
Select the **Directories** section and perform a resync by clicking **Sync Now**.

After **Sync Now** is successful, Login to tenant **VRA** Page and cross check the members in **Enterprise Groups** under **Identity and Access Management**.  

![Screenshot: Tenant Directory Details](images/newTenantCreationViaLCM/addtenantstep11.png)
