# Post Deployment Steps for Regional Deployment of vRA OnPrem

# Table of Contents

- [Post Deployment Steps for Regional Deployment of vRA OnPrem](#post-deployment-steps-for-regional-deployment-of-vra-onprem)
- [Table of Contents](#table-of-contents)
- [Changelog](#changelog)
  - [Introduction](#introduction)
    - [Purpose](#purpose)
    - [Audience](#audience)
    - [Scope](#scope)
- [Prerequisites](#prerequisites)
- [Steps to Perform Network Profile Update in Service Broker Form](#steps-to-perform-network-profile-update-in-service-broker-form)

# Changelog

| Date | TOS | Issue | Author | Description |
|------|-----|-------|--------|-------------|
| 09.02.2023 | | CESDHC-6072 | Mohit Bilakhia | Service Broker Steps for Network Profile Update for Regional deployment of vRA OnPrem |

## Introduction

### Purpose

Perform post deployment steps of Service Broker Form for Network Profile Updates for vRA OnPrem.

### Audience

- VCS Operations

### Scope

- Manual modifications in Service Broker Form whenever a new Network Profile is added through Ansible Playbooks.

# Prerequisites

vRA OnPrem for Regional Secondary Site(s) Deployment must be executed successfully.

# Steps to Perform Network Profile Update in Service Broker Form

1. Login to the vRA, Select Cloud Assembly and Open the Default Blueprint example:- **Deploy virtual machine**
![cloudassembly_blueprint](images/wiForServiceBrokerNetworkUpdate/cloudassembly_blueprint.png)

2. In the code section, verify that the new **net_tag** values is present in lists.
![blueprint_modifications](images/wiForServiceBrokerNetworkUpdate/blueprint_modifications.png)

3. Publish the Blueprint to a new version as per below.
    Click on Version Button
    Keep the default version number(auto-incremented value)
    Enter Description according to the changes made
    Please select the checkbox for release the version to catalog
![blueprint_version](images/wiForServiceBrokerNetworkUpdate/blueprint_version.png)

4. Once Version is published, Go to the **Service Broker** Option from the main menu.
![servicebroker_tab_selection](images/wiForServiceBrokerNetworkUpdate/servicebroker_tab_selection.png)

5. Select **Content & Policies** tab and then select **Content Sources** option. You can see the project name present in the table.
![sb_contentsources](images/wiForServiceBrokerNetworkUpdate/sb_contentsources.png)

6. Click on **validate** button and then click on **Save & Import**
![sb_contentsources_validate](images/wiForServiceBrokerNetworkUpdate/sb_contentsources_validate.png)

7. Select **Content & Policies** tab and then select **Content** option. You can see the default blueprint name present in the table.
![sb_contentsources_content](images/wiForServiceBrokerNetworkUpdate/sb_contentsources_content.png)

8. Besides Blueprint name, click on three dots and select **Customize form** option
![sb_contentsources_content_cusomizeform](images/wiForServiceBrokerNetworkUpdate/sb_contentsources_content_cusomizeform.png)

9. A complete service broker form will be visible. Select **Network Profile (net_tag)** field and click on **Delete** option.
![sb_contentsources_content_cusomizeform_netprf_delete](images/wiForServiceBrokerNetworkUpdate/sb_contentsources_content_cusomizeform_netprf_delete.png)

10. This net_tag field which was deleted can be seen inside **Schema Elements**. This net_tag field need to be drag and drop in the same box in **Empty section**
![sb_contentsources_content_cusomizeform_netprf_drag](images/wiForServiceBrokerNetworkUpdate/sb_contentsources_content_cusomizeform_netprf_drag.png)

11. Once added, Select net_tag and in Properties in Appearance Tab, change the label to **Network Profile** and verify that Display is set to Dropdown. Click on Save Button
![sb_contentsources_content_cusomizeform_netprf_name](images/wiForServiceBrokerNetworkUpdate/sb_contentsources_content_cusomizeform_netprf_name.png)
