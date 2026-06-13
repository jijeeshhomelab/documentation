# Onboard SOXDB into DHC production

- [Onboard SOXDB into DHC production](#onboard-soxdb-into-dhc-production)
  - [Changelog](#changelog)
  - [Documentation referalls](#documentation-referalls)
  - [Introduction](#introduction)
  - [Audience](#audience)
  - [Scope](#scope)
  - [Pre-requisite](#pre-requisite)
  - [Onboarding](#onboarding)
    - [Open the traffic](#open-the-traffic)
    - [Test the traffic](#test-the-traffic)
  - [Onboarding Request](#onboarding-request)
    - [Test SOXDB credentials](#test-soxdb-credentials)
    - [Enable SOXDB automation](#enable-soxdb-automation)
    - [Validate integration](#validate-integration)
    - [File Upload Process](#file-upload-process)
  - [Data Export Process](#data-export-process)
  - [Data Lifecycle and Retention Procedures](#data-lifecycle-and-retention-procedures)

## Changelog

| Date       | TOS     | Issue   |    Author         |    Description    |
| ---------- | ------- | ------- | ----------------- | ----------------- |
| 19-12-2024 | DHC 2.1 |   VCS-14574  | Tomasz Korniluk | Draft of WI how to onboard DHC production instance into SOXDB |
| 17-01-2025 | DHC 2.1 |   VCS-14306 | Tomasz Korniluk | Final review |

## Documentation referalls

| Name      | Location     |  Description    |
| ---------- | ------- | ------- |
| wiSoxDBIntegration.md| [wiSoxDBIntegration.md](wiSoxDBIntegration.md) | SOXDB integration work instruction |

## Introduction

This document outlines the steps how to rollout and integrate into SOXDB already existing DHC production instance.

## Audience

DHC devsecops team members which delivers support for the DHC production instances

## Scope

The scope covers the onboarding process from existing DHC production instance to SoxDB, including prerequisites, connection testing, and automation procedures.

Note: WI not applicabled for DHC new deployments, in that case follow document mentioned inside chapter **Documentation referalls**

## Pre-requisite

The following pre-requisites inputs needs to be collected before starting with SOXDB onboarding process:

1. Collect current name of Customer registered inside Atos ServiceNow instance (prod instance)
2. Capture network trace between DHC management workload domain component (ans001) and SOXDB instance (``https://globalview.it-solutions.atos.net/sox/#/``)
3. Collect for the affected DHC production environment NAT IP (outgoing traffic IP that will be used to establish connection with SOXDB instance)
4. Collect port number(default 443) required to establish communication between DHC management workload domain component (ans001) and SOXDB instance
5. Collect vCenter instances FQDNs for DHC management and compute workload domains
6. Validate if TCP 443 port is open between DHC management workload domain Terminal server (TSS001) and affected vCenter instances (in case blocked allow the traffic)
7. Validate if TCP WINRM port is open between DHC management workload domain component Ansible machine and Terminal server (TSS001) (in case blocked allow the traffic)

## Onboarding

The following chapter explains steps to onboard existing DHC production instance into SOXDB.

### Open the traffic

Based on the collected input data from Pre-requisite chapter create a network request for Atos Network Team to allow traffic between DHC and SOXDB, please use the following request for the referrence:**RITM013684084**

**Note:** Make sure to provide inside network request correct source DHC NAT IP and destination SOXDB IP with default destination TCP port 443.

### Test the traffic

Logon into affected DHC management workload domain Ansible node (ans001) and execute below command to test if traffic is open into SOXDB production (use SOXDB ip and default 443 TCP port)

 ```shell
    telnet IP PORT
 ```

## Onboarding Request

Contact the SOXDB support team - Marta Pradun or Klaus Halbig (Service Owner) to onboard new production DHC Customer into SOXDB application and obtain appropriate credentials for the scan automation.
Ensure to use the email channel only and encrypt sensitive information. Put the appropriate Atos TSM in CC for the affected DHC production instance.

Below is an example email request:

```text
Dear SOXDB,

Please onboard Production Customer Acme01 into SOXDB to deliver the data:

- VMware / ESXi user accounts and groups report
- Active Directory user accounts with groups report

1.) ServiceNow Customer name: Acme (`<ServiceNowCustomerFOName>` e.g. Acme01)
2.) Type of data to upload: VMware / ESXi user accounts and groups, Active Directory user accounts with groups
3.) Upload done via API call
4.) File format: csv
5.) File name: `<CustomerName>`.csv
6.) DHC source NAT IP: XXX.XXX.XXX.XXX
7.) Notification recipients: <enter the proper DevSecOps distribution list) and TSM email addresses

Provide the API user credentials and the SOXDB endpoint URL to upload the data.
````

### Test SOXDB credentials

**Upload a file using CURL**: This command uploads a file to the specified URL using the provided SOX user credentials. Make sure to run this command from ans001.

```shell

    curl -u SOX_USER_ID:'PASSWORD' -v -T DEFINE_FILE_PATH SOX_URL

```

**Explanation of used parameters:**

**SOX_USER_ID** - unique username provided by SOXDB team to authorize the upload data API call

**PASSWORD** - unique password credentialsp rovided by SOXDB team to authorize the upload data API call

**DEFINE_FILE_PATH** - folder and file path with data for the upload (e.g. /opt/Acme01.csv), for the connection tests use empty csv file

**SOX_URL** - Full url into SOXDB production instance with correct Customer name suffix, in example: ``https://globalview.it-solutions.atos.net/sox/Acme01``

**Note:** Consult correct upload url with SOXDB support team

### Enable SOXDB automation

Make sure to follow document: [wiSoxDBIntegration.md](wiSoxDBIntegration.md)

Document explains exportAndImportToSoxDB.yml playbook execution to enable schedule SOXDB scan and validation.

### Validate integration

Upon successful upload of CSV files to the SoxDB environment (both or either of DEV and PROD instances), users will receive an automated notification email confirming the upload.

**Note:** Make sure that are provided correct recipients for SOXDB team.

### File Upload Process

 1. Upload Locations:

     - SoxDB PROD: [PROD URL](https://globalview.it-solutions.atos.net/sox/#/)

 2. Automation Script: The automation script facilitates the upload of CSV files to the specified SoxDB instance. Ensure that the CSV file conforms to the expected format prior to uploading.

 3. Notification: Once the file upload is successfully completed, an automated email notification will be sent to the designated recipients. The email will confirm that the file has been uploaded successfully. Screenshot attached for reference.

![SOXDB](images/wiSoxDBIntegration/soxdb1.PNG)

## Data Export Process

TSM can access the uploaded data from SoxDB PROD: [PROD URL](https://globalview.it-solutions.atos.net/sox/#/)

Exporting Results: Users can export the resulting data directly from the SoxDB interface. Follow the prompts within the UI to select the desired data and initiate the export.

Screenshot attached for reference.

  ![SOXDB](images/wiSoxDBIntegration/soxdb.PNG)

## Data Lifecycle and Retention Procedures

- If no data is sent from DHC to SOXDB within 10 days, the data will be marked for deletion.
- Deleted data will not be visible in the SOXDB GUI.
- Access to exported data will only be available in PROD SOXDB.
- Notifications will be rolled out to provide updates about the status of data uploads and deletions.
- In DEV SOXDB, automation runs every week day at 9 AM CET (vCenter scan).
- In DEV SOXDB, automation runs every week day at 8 AM CET (Active Directory scan).
- In PROD SOXDB, automation runs every week day at 9 AM CET (vCenter scan).
- In PROD SOXDB, automation runs every week day at 8 AM CET (Active Directory scan).
