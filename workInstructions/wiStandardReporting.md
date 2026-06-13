# Standard Reporting

## Table of Contents

- [Standard Reporting](#standard-reporting)
  - [Auth Source](#auth-source)
    - [Import User Groups from vIDM](#import-user-groups-from-vidm)
  - [RBAC (Role-Based Access Control)](#rbac-role-based-access-control)
    - [Scope](#scope)
    - [Role](#role)
    - [Custom Groups](#custom-groups)
  - [Assign Scope and Role to User Group](#assign-scope-and-role-to-user-group)
  - [Dashboard Management](#dashboard-management)
    - [Remove Dashboard from “Everyone” Group](#remove-dashboard-from-everyone-group)
    - [Assign Dashboard to User Group](#assign-dashboard-to-user-group)
  - [External Reports](#external-reports)
  - [Reverse Proxy](#reverse-proxy)
  - [IDM as Landing Page](#idm-as-landing-page)
  - [Note on Default vIDM User Group in vROps](#note-on-default-vidm-user-group-in-vrops)

## List of Changes

| Version | Date       | Author       | Issue    | Changes           |
|---------|------------|--------------|----------|-------------------|
| 0.1     | 22.11.2024 | Rachel Beulah | VCS-14360 | Document creation |
| 0.2     | 20.12.2024 | Rachel Beulah | VCS-14578 | Documentation update |
| 0.3     | 28.03.2025 | Rachel Beulah | VCS-15204 | Assigning dashboard to user group |
| 0.4     | 21.07.2025 | Rachel Beulah | VCS-16672 | Update 'Dashboard Sharing to User Groups' section |
| 0.5     | 13.10.2025 | Rachel Beulah | VCS-17234 | Consolidated documentation under "Standard Reporting" |
| 0.6     | 04.11.2025 | Rachel Beulah | VCS-17604 | Corrected link for documentation and detailed explanation for each section |

## Additional References

| Reference Name | Link |
|-----------------|------|
| Integration of AD with vIDM | [workInstructions/wiIntegrateADwithVidm.md](wiIntegrateADwithVidm.md) |
| Removing Dashboard from Everyone Usergroup | [workInstructions/wiSetvRopsDashboardPermission.md](wiSetvRopsDashboardPermission.md) |
| Dashboards in Aria Operations | [design/lldReporting.md#43-dashboards](../design/lldReporting.md#43-dashboards) |
| Scope and Role in Aria Operations | [design/lldReporting.md#431-scope-and-role-in-aria-operations](../design/lldReporting.md#431-scope-and-role-in-aria-operations) |
| Reverse Proxy WI | [workInstructions/wiReverseproxy.md#automation-method](wiReverseproxy.md#automation-method) |

## Introduction

### Purpose

This document explains the end-to-end process of setting up Standard Reporting in Aria Operations.
It includes integrating authentication sources (vIDM), defining RBAC (Scope, Role, Custom Groups), collecting External reports, assigning dashboards to user groups, and enabling access through reverse proxy and IDM.

## Auth Source

Authentication sources are required for integrating external identity systems with Aria Operations.
This allows users to log in with their existing credentials (vIDM).

### Import User Groups from vIDM

vIDM (VMware Identity Manager) is already configured with Aria Operations under "Authentication Sources"; however, the user groups from vIDM still need to be imported into Aria Operations.

**Playbook:(in manage phase)**

```bash
ansible-playbook importUserGroupVidm.yml
```

This playbook import user groups from vIDM into vROps under **Control Panel->Access Control->User Groups**, making them available for assigning roles and scopes.

**Inputs required:**

- Customer user groups that need to be imported into vROps with the domain name. – e.g., ```group1@domain1.next, group2@domain2.next```

> Note: Before importing usergroup, ensure the customer directory is integrated with vIDM.  
If not, follow the instructions here: **Integration of AD with vIDM** (see [Additional References](#additional-references) table).

---

## RBAC (Role-Based Access Control)

RBAC (Role-Based Access Control) ensures that users only see and perform actions relevant to their assigned scope and role.

### Scope

A scope defines the boundary of what a user or group can access in Aria Operations, such as a vCenter, cluster, or resource pool.

**Playbook:(in manage phase)**

```bash
ansible-playbook createVropsScope.yml
```

This playbook creates or updates scopes based on a configuration file.

**Inputs required:**

The configuration file, named ```scopes.yaml```, defines which adapters, clusters, or objects should be part of the scope and should be located in the user's home directory.(refer, [roles/dhc-createVropsScope/templates/scope.j2](https://github.com/GLB-CES-PrivateCloud/DHC-Manage/blob/develop/roles/dhc-createVropsScope/templates/scope.j2))

**Sample "scope.yaml" file**

```yaml
Scope_nsx:       #SCOPE_NAME
  NSX Overview:
    NSX World:
      NSXT Adapter Instance for gre12nsx001.nx1dhc01.next:
        Management Cluster [gre12nsx001.nx1dhc01.next]:
          Edge Clusters [gre12nsx001.nx1dhc01.next]:
            gre12ecn101:
              gre12edg101: {}
```

#### Figure: Sample scope

![image](/workInstructions/images/wiStandardReporting/scopeSample.png)

### Role

A role defines what actions a user can perform inside vROps.

**Playbook:(in manage phase)**

```bash
ansible-playbook createRole.yml
```

This playbook creates a custom role ```Role-readonly``` in **Control Panel->Access Control->Roles**, with the required privileges to view dashboards, reports, and perform non-admin actions.
This role gives users the ability to view and interact with dashboards and reports while restricting admin-level access.

### Custom Groups

Custom groups help organize resources (like VMs, clusters, or datacenters) into meaningful sets, making monitoring and RBAC assignment easier. It allows you to group resources based on tags such as vCenter cluster, resource pool, or datacenter.

**Playbook:(in manage phase)**

```bash
ansible-playbook createCustomGroup.yml
```

**Inputs required:**

- Custom group name

> Note:  By default, it groups resource pools with the tag "resourcepool", but the code needs further development to support creating custom groups based on specified tags for particular resources.

#### Figure: Sample Custom Group

![image](/workInstructions/images/wiStandardReporting/customgroupVcenter.png)
![image](/workInstructions/images/wiStandardReporting/customgroupvrops.png)
![image](/workInstructions/images/wiStandardReporting/customgroupvrops1.png)

---

## Assign Scope and Role to User Group

After scopes and roles are created, they must be assigned to user groups so users get the right access.

**Playbook:(in manage phase)**

```bash
ansible-playbook assignPermissiontoUsergrp.yml
```

This playbook assigns a specific scope and role to a user group in vROps. It updates the vROps user group permissions using API calls.

**Inputs required:**

- User group name
- Scope name
- Role name

---

## Dashboard Management

Dashboards provide visual insights into the infrastructure. Access can be managed by removing dashboards from the “Everyone” group and assigning them to specific user groups.

### Remove Dashboard from “Everyone” Group

By default, dashboards may be shared with the “Everyone” group. This playbook removes those dashboards to prevent unrestricted visibility.

**Playbook:(in manage phase)**

```bash
ansible-playbook setvRopsDashboardPermission.yml
```

This playbook removes all dashboards from the “Everyone” user group using a Python Selenium script. Refer to: **Removing Dashboard from Everyone Usergroup** (see [Additional References](#additional-references) table).

### Assign Dashboard to User Group

Dashboards need to be assigned to user groups to provide them with visibility and access to the relevant dashboards.

**Playbook:(in manage phase)**

```bash
ansible-playbook assignDashboardToUsergrp.yml
```

This playbook is automated using a Python Selenium script that selects the dashboard and shares it with a specific user group.

**Input File Required:**
The input must be a YAML file named ```input.yml```, located in the user’s home directory. This file defines which dashboards are assigned to which user groups.

**Sample "input.yml" file:**

```yaml
dashboardsList:
  - dashboard_name: "Cluster Capacity"
    usergroup_name: "rsce-<locationcode>-customertenant2"
  - dashboard_name: "VM Availability"
    usergroup_name: "rsce-<locationcode>-customertenant1"
  - dashboard_name: "NSX Logical Switch Inventory"
    usergroup_name: "rsce-<locationcode>-customertenant1"
```

For a full list of dashboards, refer to:
**Dashboards in Aria Operations** (see [Additional References](#additional-references) table)

To understand how scope and role are applied in dashboards, refer to:
**Scope and Role in Aria Operations** (see [Additional References](#additional-references) table)

---

## External Reports

External reports, such as RV Tools, Nessus, and Patching, etc., are collected and hosted them on a web server(httpgateway server), and displayed them as a dashboard inside vROps using the Text Widget option.

### Configure Webserver

On the HTTP Gateway server (hgw), Nginx is installed and configured to host external reports. A DNS alias named ```dashboard``` is created for this server, allowing the hosted reports to be accessed using this alias.

**Playbook:(in deploy phase)**

```bash
ansible-playbook configureWebserver.yml
```

#### Figure: Nginx Configuration in hgw server

![image](/workInstructions/images/wiStandardReporting/reports.png)

### Collect External Reports

Collect External reports from external tools such as RV Tools, Nessus, Patching, etc., onto the Ansible server, convert them to HTML files, change the file permissions, and host them on the web server.

**Playbook:(in manage phase)**

```bash
ansible-playbook externalReportsVrops.yml
```

> Open ```https://dashboard/```in browser, you should see the newly added report folders.
>
> **Note:**
An NFS share is proposed to be mounted on both the Ansible and Web servers at ```/opt/reports/dhcReports```. This setup is currently under review and pending confirmation.

### Import Dashboard for External Reports

The dashboards are imported into Aria Operations, which are configured with text widgets to display reports that are hosted on the web server.

**Playbook:(in manage phase)**

```bash
ansible-playbook importVropsExternalReportDashboards.yml
```

> Check the dashboards in Aria Operation, whether it display content(report) which are hosted in Webserver.

#### Figure: Sample Dashboard with Report in Aria Operations

![image](/workInstructions/images/wiStandardReporting/externalDashboard.png)
![image](/workInstructions/images/wiStandardReporting/externalDashboard1.png)

### Scheduling External Reports

Setting up a schedule for the external reports playbook to ensure dashboards always show the latest reports.

**Playbook:(in manage phase)**

```bash
ansible-playbook cronJobForExternalReportsVrops.yml
```

---

## **Reverse Proxy**

The reverse proxy acts as a front-end gateway that hides internal system details from external users, securely routing traffic to backend services like vROps, NSX-T and vIDM.

Refer to: **Reverse Proxy WI** (see [Additional References](#additional-references) table)

---

## IDM as Landing Page

vIDM (Identity Manager) is configured as the landing page for users to access multiple appliances through SSO.

**Playbook:(in manage phase)**

```bash
ansible-playbook createWebAppVidm.yml
```

This playbook creates web applications in vIDM and assigns them to specific user groups.
Each web app points to a vROps dashboard or external report URL.

**Inputs required:**

- Web app name
- Target URL (Appliance URL)
- User group name (to assign access)

This ensures that users can log in via vIDM and directly access their assigned dashboards from the landing page.

## Note on Default vIDM User Group in vROps

When VMware Identity Manager (vIDM) is integrated with vROps, any user authenticated through vIDM can access vROps. If the user does not belong to a user group with a specified scope and role, they are automatically assigned to the default user group named **"VIDM"**, which by default may have access to all objects.

To secure the environment and avoid unintended broad access:

- **Assign the "VIDM" user group a minimal role**, such as read-only access or limited access to only the home page.
- **Create an empty scope** (a scope containing no resources) and assign it to the "VIDM" group.
  
This ensures that users falling into this default group have minimal permissions.
