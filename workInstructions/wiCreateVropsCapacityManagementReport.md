# VCS vROPS report for Capacity Management

## Table of Contents

- [VCS vROPS report for Capacity Management](#vcs-vrops-report-for-capacity-management)
  - [Table of Contents](#table-of-contents)
- [Changelog](#changelog)
  - [Introduction](#introduction)
    - [Purpose](#purpose)
    - [Audience](#audience)
    - [Scope](#scope)
  - [Prerequisites](#prerequisites)
  - [Custom groups creation in vROPS Steps](#custom-groups-creation-in-vrops-steps)
  - [Process for Report creation and scheduling](#process-for-report-creation-and-scheduling)
  - [Import Super metrics](#import-super-metrics)
  - [Create Custom Profiles](#create-custom-profiles)
  - [Enable Allocation Model](#enable-allocation-model)
  - [Import Dashboards](#import-dashboards)
  - [Attachments](#attachments)

# Changelog

| Version | Date       | Description              | Author          |Jira Story|
| ------- | ---------- | ------------------------ | --------------- |----------|
| 0.1     | 23/02/2022 | First version            | Berte Petru     |DHC-3791  |

## Introduction

### Purpose

Create a vROPS capacity report.

### Audience

- VCS Operations

### Scope

Create vROps capacity reports.

## Prerequisites

1. vROPS 7.5.
2. Admin access on vROPS.

## Custom groups creation in vROPS Steps

- Login to vROPS using **Local Admin account**.

  ![enter image description here](images/wiCreateVropsCapacityManagementReport/logInTovROPS.jpg)
- Click on **Environment -> Custom Groups - > New Custom Group**.
- Provide below details in group:

  - **Name** – Group Name.
  - **Group type** – Environment.
  - **Policy** – Default Policy.
  - **Keep Group membership up to date** – Checked.

    ![enter image description here](images/wiCreateVropsCapacityManagementReport/newCustomGroup.jpg)

- Click on **OK**
- Create other group using same method for Virtual Machine and vSAN Datastores.

## Process for Report creation and scheduling

- Create Custom Group using above steps
- Click on **Details** and **Import** attached view.
  
 ![enter image description here](images/wiCreateVropsCapacityManagementReport/clusterUtilizationZip.jpg)
  
   ![enter image description here](images/wiCreateVropsCapacityManagementReport/createCustomGroup.jpg)

- Click on **Reports** and **Import** attached Report.
  
  ![enter image description here](images/wiCreateVropsCapacityManagementReport/createCustomGroup2.jpg)

- Select imported report and click on **schedule**
- Click on **+** for new schedule.
  
  ![enter image description here](images/wiCreateVropsCapacityManagementReport/createCustomGroup3.jpg)

- Select schedule as per requirement, provide email address and select SMTP rule, Click on OK.
  
  ![enter image description here](images/wiCreateVropsCapacityManagementReport/createCustomGroup4.jpg)

- Create Virtual Machine configuration report using referring above method and using below attached view and report.

  1.View:
  
  ![enter image description here](images/wiCreateVropsCapacityManagementReport/dpcVirtualMachineConfigurationZip.jpg)

  2.Report:
  
  ![enter image description here](images/wiCreateVropsCapacityManagementReport/vmConfigurationReportZip.jpg)

- Create VM undersized and oversized report using referring above method and using below attached view and report.

  1.View:
  
  ![enter image description here](images/wiCreateVropsCapacityManagementReport/oversizedVirtualMachinesZip.jpg)

  2.Report:
  
  ![enter image description here](images/wiCreateVropsCapacityManagementReport/vmOversizeAndUndersizeReportZip.jpg)

- Create vSAN Datastore report using referring above method and using below attached view and report.

  1.View:
  
  ![enter image description here](images/wiCreateVropsCapacityManagementReport/vsanDatastoresViewZip.jpg)

  2.Report:
  
  ![enter image description here](images/wiCreateVropsCapacityManagementReport/vsanDatastoreReportZip.jpg)

- Create VM Count report using referring above method and using below attached view and report.

  1.View:
  
  ![enter image description here](images/wiCreateVropsCapacityManagementReport/vmCountTrendZip.jpg)

  2.Report:
  
  ![enter image description here](images/wiCreateVropsCapacityManagementReport/vmCountTrendReportZip.jpg)

- Create ATOS Cluster Compute Capacity Report using referring above method and using below attached view and report.

  1.View:
  
  ![enter image description here](images/wiCreateVropsCapacityManagementReport/atosClusterComputeCapacityZip.jpg)

  2.Report:
  
  ![enter image description here](images/wiCreateVropsCapacityManagementReport/atosClusterComputeCapacityReportZip.jpg)

## Import Super metrics

- Navigate to Administration – Configuration – Super Metrics.
- Import below Super Metrics:
  
  ![enter image description here](images/wiCreateVropsCapacityManagementReport/supermetricJson.jpg)

## Create Custom Profiles

- Navigate to Administration – Configuration – Custom Profiles. Create custom Profiles as shown below.
  
  ![enter image description here](images/wiCreateVropsCapacityManagementReport/customProfiles.jpg)

## Enable Allocation Model

- Navigate to Administration – policies – Policy Library. Select Policy which is used and edit it.
  
  ![enter image description here](images/wiCreateVropsCapacityManagementReport/enableAllocationModel.jpg)

- Select Cluster Compute Resource in Analysis Settings and set required values.
  
  ![enter image description here](images/wiCreateVropsCapacityManagementReport/enableAllocationModel2.jpg)

- Select all Custom Profiles which we have created.

## Import Dashboards

- Navigate to Dashboards – Manage Dashboards.
  
  ![enter image description here](images/wiCreateVropsCapacityManagementReport/importDashboards.jpg)

- Select Import Dashboards.
  
  ![enter image description here](images/wiCreateVropsCapacityManagementReport/importDashboards2.jpg)

- Import below attached Dashboards:
  
  ![enter image description here](images/wiCreateVropsCapacityManagementReport/dashboardZip.jpg)

## Attachments

The attachments that are needed to CreateDHCvROPSreportForCapacityManagement are

| Name of the attachment              |
| ----------------------------------- |
| ATOSClusterComputeCapacityReportZip|
| ATOSClusterComputeCapacityZip|
| ClusterUtilizationZip|
| ClusterUtilizationZip|
| DPCVirtualMachineConfigurationZip|
| VmConfigurationReportZip|
| SupermetricJson|
| VmConfigurationReportZip|
| VMCountTrendReportZip|
| VMcountTrendZip|
| VMoversizeAndUndersizeReportZip|
| vSANDatastoreReportZip|
| vSANDatastoreReportZip|

The attachments can be downloaded from the original document
 [VCS vROPS report for Capacity Management](https://atos365.sharepoint.com/:w:/r/sites/DHCDevSecOpsTeam/Shared%20Documents/Code%20and%20documentation%20review/DHC%20vROPS%20report%20for%20Capacity%20Management/DHC%20vROPS%20report%20for%20Capacity%20Management.docx?d=w4e8623f81619467cb4fce8b70b7f3b92&csf=1&web=1&e=pChXUa)
