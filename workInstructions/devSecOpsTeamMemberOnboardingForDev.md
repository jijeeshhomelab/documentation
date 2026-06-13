<!-- omit in toc -->
# DevSecOps Developers Onboarding

<!-- omit in toc -->
## Table of content

- [Introduction](#introduction)
  - [Purpose](#purpose)
  - [Audience](#audience)
  - [Goal](#goal)
- [Buddy assignment](#buddy-assignment)
- [The new developer responsibility](#the-new-developer-responsibility)
- [Additional tools and access](#additional-tools-and-access)
- [Trainings](#trainings)
- [Training KTs sources](#training-kts-sources)
- [Code and Process useful links](#code-and-process-useful-links)
- [Design useful links](#design-useful-links)
- [Changelog](#changelog)

# Introduction

The onboarding process is a structured approach to welcome the new developer into an existing team and familiarizing them with the team's workflows, processes, and tools.

## Purpose

The purpose of this document is to support new DevSecOps dev team member in the process of integrating and also help the developer feel welcomed and equipped to contribute effectively.

## Audience

This document is a specific onboarding plan for DevSecOps dev team. It implies that the developer already has kowledge and went through the onboarding plan for DevSecOps ops document:

- [ops](https://github.com/GLB-CES-PrivateCloud/DHC-Engineering/blob/master/documentation/devSecOpsTeamMemberOnboarding.md)

## Goal

This guide is meant to help with the onboarding of new developers into the DHC DevSecOps dev team by providing them with the knowledge, tools, and resources necessary to become productive members.

# Buddy assignment

Each new developer will be assigned a Buddy by Team Lead or Line Manager. Main responsabilities for the buddy are:

- to help with aquiring technical knowledge by providing technical documentation and explain where it's not clear.
- to offer encouragement and resources to help the colleague in building their confidence.
- to explain basic operational and process related operations to introduce them to the work culture.
- to support the new colleague's immediate productivity on the job.
- to use goals to set clear expectations and have consistent and weekly check-ins calls to validate training points are properly prepared.
- to propose topics to work on based on the onboarding check-ins.

# The new developer responsibility

The new developer joining DHC DSO is encouraged to be self-driven in their learning path. This includes:

- to acquire necessary troubleshooting skills from the training plan and working on different development tasks
- to develop critical thinking and be autonomous in dealing with topics and troubleshooting/analyzing issues
- to develop proactiveness in proposing new ideas for improvements in code/process
- to know how to look-up information in the web
- to develop an architectural knowledge and creativity in working with the code

The new developer's personal evaluation: onboarding goals/milestones need to be double-checked together with the buddy colleague during the onboarding check-ins.

# Additional tools and access

**For specific customers, there are additional tools and access requests that needs to be set up.**

Ensure you have the proper accesses to the following services. Check the [onboarding for ops](https://github.com/GLB-CES-PrivateCloud/DHC-Engineering/blob/master/documentation/devSecOpsTeamMemberOnboarding.md) for details on contact person and further link for procedure on requesting access.

- Git CES Private cloud repositories access (ex. DHC-Update, DHC-Manage, DHC-Deploy, DHC-Collections, DHC-Documentation, etc.)
- DHC LabHub credentials - access when needed
- Different sharepoint links for: internal KTs, Production Plan tracker, CMDB templates and change implementation plans, customer specific documents, etc.
- SNOW
- JIRA and Confluence
- Group mailbox
- SAaCon
- Customer production environments
- Broadcom account with support contract entitlement (to raise Support Requests)

- Tosca portal
- PISA portal

Additional tools will need to be installed and configured while working on different development topics:

- Visual Studio Code
- Python

Social tools:

- Microsoft Outlook
- Microsoft Teams

# Trainings

Bellow table contains a list of trainings required for DevSecOps development onboarding. For each technologies/tool, a reference to a resource link is provided.

| Technology                             | Minimum requirement                           | Where to find such information?       |
|:---------------------------------------|:----------------------------------------------|:--------------------------------------|
| Access to DEV LAB                   | Ensure that new developer know what DEV environments are there, and where to search for details information (Confluence)  | Check "Useful links", DHC Lab Environments link  |
|                                     | Knows about dev environments, difference between them, how to access them and where to find related documentation  |  Some topics, like vRA on-prem related ones are to be tested on specific test locations; other topics like upgrades is into location scope, etc   |
|            |                                     |                                     |
|  Ansible   |  Familiar with YAML Syntax  |  Online Ansible documentation  |
|            |  Understand the structure of a Playbook in Ansible - Different types of Playbooks (standalone and with roles)  |  DevSecOps KT on Teams shared folder; Online Ansible documentation  |
|            | Understand how to interact with roles (Include role, include task, role main, include vars, include groups vars...) |  DevSecOps KT on Teams shared folder; Online Ansible documentation |
|            |  Understand what an Ansible inventory is, how it is used, and its features | DevSecOps KT on Teams shared folder; Online Ansible documentation |
|            |  Understands the difference between the Playbook, play and task | DevSecOps KT on Teams shared folder; Online Ansible documentation |
|            | Understands what effect does indentation have on tasks, conditionals and parameters | DevSecOps KT on Teams shared folder; Online Ansible documentation |
|            |  Understanding of concepts in Ansible like: Playbooks, roles, modules, collections, inventory files, tasks  | DevSecOps KT on Teams shared folder; Online Ansible documentation |
|            |  Understands the precedence rules for the variables  | DevSecOps KT on Teams shared folder; Online Ansible documentation |
|            | **The new developer to be able to edit or create simple Playbooks:** | Online Ansible documentation |
|            | - Create simple standalone Playbook doing XYZ |  Example Playbook: listTcpToMonitor.yml |
|            | - Create simple Playbook which is triggering roles |  Example Playbook: collectReports.yml; VidmApplicationAccounts.yml |
|            | - Improve existing one  |  Example Playbook: resetAdServiceAccount.yml |
|            | Understand how VMware components interacts with each other (ex. via api call) to be able to troubleshoot failed Playbooks | DevSecOps KT on Teams shared folder |
|            | Knowledge of concepts like loops, conditionals, facts, hosts etc. | Online Ansible documentation; Git Playbooks for examples |
|            | Understand how to use patterns, inventory hosts, group vars, collections and modules | Atos Percipio; Online Ansible documentation |
|            | Understand and code according to Git lint standards | Online lint documentation |
|            | Understand how tags can be used in a Playbook | Example Playbook: configureSrmReport.yml |
|            | Understand how roles are used with Ansible collection in 2.0 and difference between old setup DHC versions  | DevSecOps KT on Teams shared folder |
|            | Understand the method of transporting credentials between plays | Example Playbook: patchLinuxCron.yml |
|            | Understand usage of extra vars in a Playbook  | Example Playbook: resetSddcManagerManagedPasswords.yml |
|            | Understand different types of setting up connection: token, cert, credentials, API/Rest API calls  | Online Ansible documentation; Git Playbooks for examples |
|            | Understand when to define a var in role/default and when to add it in group_vars  | Code and Process useful links: Coding Standards document |
|            | Knows how delegation works  |  Git Playbooks for examples  |
|            | Gain a comprehensive understanding of Ansible errors, including: syntax, type, and name errors, as well as debugging techniques  | Atos Percipio; Online Ansible documentation  |
|            | Be able to update or install an Ansible module and be able to work with Ansible galaxy, know Ansible galaxy structures |  Online Ansible documentation  |
|            | Identify the key features of the Ansible Engine and how it differs from Ansible Tower  |  Atos Percipio; Online Ansible documentation  |
|            | Understand integration between Ansible Playbooks and generation of logs; integration with vRLI | DevSecOps KT on Teams shared folder |
|            | Understand and troubleshoot integration between Ansible Playbooks and AD on managing accounts: creating, listing, reseting passwords, etc. | DevSecOps KT on Teams shared folder; Git documentation WI  |
|            | Understand and troubleshoot global image related Playbooks | Ex. Playbook importGlobalImagesToContentLibrary.yml  |
|            | Understand integration between Ansible and vRA on-prem to be able to troubleshoot and update Playbooks on different topics: password reset, user report, etc. |  DevSecOps KT on Teams shared folder; Git documentation WI   |
|            | Understand and be able to troubleshoot Tosca and Ansible integration  | DevSecOps KT on Teams shared folder; Git documentation WI  |
|            | Understand and be able to troubleshoot backup mechanism: cross-vault Hashi Corp, etc.  |  DevSecOps KT on Teams shared folder; Git documentation WI |
|            | Ability to research Ansible documentation and find modules required for the tasks  | Online Ansible documentation |
|            | Ability to research Ansible DHC documentation and find roles required for the tasks if such exist |  Online Ansible documentation  |
|            | Ability to incorporate other scripting languages like powershell, Python or bash into the Ansible  | Online documentation |
|            | Knows how to use and integrate Jinja2 filters and templates in Ansible  | Online Ansible documentation  |
|            | Understanding of Ansible configuration files  | Online Ansible documentation   |
|            | Parsing JSON (for example getting from API call response specific line/variable to use later in Playbook) | Online Ansible documentation  |
|            |                                     |                                     |
| Ansible tower  | **Understand current integration between git - Ansible tower project:** |                                     |
|            |  - How the guest OS scripts (in Git) are linked to the project (Ansible Tower)   | DevSecOps KT on Teams shared folder  |
|            |  - How to change git branch | DevSecOps KT on Teams shared folder  |
|            |  - Identify where are the credentials and how are those linked for any job template |  Ansible Tower -> Resources -> Credentials and Cyberark |
|            | **Understand integration between git repository and automation tool vRA/vRO:** |                                     |
|            | - Be able to search for hosts in a specific inventory based on domain and location | Ansible Tower -> Inventories -> select correct inventory -> hosts |
|            | - Understand the logic of Ansible different types of jobs triggered with different SSR orders |  Jobs triggered for creating a windows server: "dhc-windows-join-domain"; "dhc-windows-system-setup", etc. |
|            | Be able to trigger simple jobs placing in maintenance (toggle-scom-maintenancemode) or pinging a server (win_ping)  |   Ansible Tower -> different types of jobs  |
|            | Understand a job template components   |    Ansible Tower -> in a job -> details for example   |
|            | Understand how to add for troubleshooting purposes on a job more detailed printed logs and output  |  Ansible Tower -> in a job -> details for example  |
|            | Understand how Ansible tower logs from failed job are sent to vRO and stored on vRLI  |   DevSecOps KT on Teams shared folder   |
|            |                                     |                                     |
|  vRA/vRO and automation components |  **Understand vRO workflow design and schema:** |                                     |
|            | - Understand 1st and 2nd Day SSR logic and vRO workflow schema  |  DevSecOps KT on Teams shared folder  |
|            | - Understand various input/output types in a vRO workflow |  DevSecOps KT on Teams shared folder  |
|            | - Know basic vRO workflow schema elements: scriptable task, decision, action element, workflow element, switch, etc. |  DevSecOps KT on Teams shared folder |
|            | - Understand error handling in a vRO workflow case |  DevSecOps KT on Teams shared folder |
|            | - Knows what are, where to find and how to fill Configurations and Inventory |  DevSecOps KT on Teams shared folder  |
|            | - Knowledge of how to construct the workflow, how to run one and check already existing workflows  |  DevSecOps KT on Teams shared folder  |
|            | - Knows how to integrate vRO with Git and push/pull changes |  DevSecOps KT on Teams shared folder |
|            | Understand and be able to troubleshoot integration between Infoblox - NSXT - vRA on-prem - used for 2nd Days SSRs |  DevSecOps KT on Teams shared folder; working on SSRs |
|            | Be able to troubleshoot vRO/vRA appliances; knoledge about kubectl commands |  Broadcom online documentation |
|            | Understand what is a vRO plug-in and how to update it |  DevSecOps KT on Teams shared folder |
|            | Understand vRA blueprint versioning  |  DevSecOps KT on Teams shared folder  |
|            | Understand how vRA blueprint is released towards Service Broker Content Sources |  Broadcom online documentation |
|            |  Understand cloud accounts (vCenter/NSX)   |  Broadcom online documentation |
|            |  Understand network profiles and related integration concepts: Infoblox range; tagging; NSX network link | DevSecOps KT on Teams shared folder  |
|            |  Understand how Service Broker Custom Forms work |  DevSecOps KT on Teams shared folder  |
|            |  Understand how to create a Service Broker Catalog item |  Broadcom online documentation |
|            |  Be able to configure a new cluster into vRA assembler  |  Broadcom online documentation; Git WI Documentation       |
|            |  Know different types of vRA tags and their scope    |   Broadcom online documentation  |
|            |  Be able to work with API calls to troubleshoot or implement different fixes or solutions: reset a password on a cloud account, access vRA deployments to investigate issues with a a VM deployment   |  DevSecOps KT on Teams shared folder; Git documentation WI  |
|            |  Be able to install and test in Postman different api calls to discover and apply fixes for 2nd Day SSRs  |   DevSecOps KT on Teams shared folder |
|            |  Understand the integration between Infoblox IPAM Plug-in and vRA  |   DevSecOps KT on Teams shared folder; working on SSRs |
|            |  Understand what is the role of a vRA action   |  Broadcom online documentation  |
|            |  Understand what is a vRA subscription |   Broadcom online documentation   |
|            |  Understand the role of custom properties in vRA  |  Broadcom online documentation  |
|            |  Understand the difference between a SOAP and a REST API call and their use cases  |  Broadcom online documentation  |
|            |  Understand vRA Event Topics   |   Broadcom online documentation  |
|            |  Understand multi-tenancy in vRA on-prem  |  Broadcom online documentation  |
|            |  Understand the role of Bearer token and integration between SNOW - Cyberark - vRA/vRO  |  DevSecOps KT on Teams shared folder  |
|            |                                     |                                     |
| GIT  |  Knowledge of concepts like branches, repositories, pull requests etc.  |  Git Wiki, online gitHub documentation |
|            | Knows how to configure Git |  Git Wiki, online gitHub documentation |
|            | Review and test other colleague's PR |  Git Wiki, online gitHub documentation |
|            |  Be able to create branches or remove branches |  Git Wiki; online gitHub documentation; DevSecOps KT on Teams shared folder  |
|            |  Be able to pull, stage and push commits or revert committed changes on a branch  | Git Wiki, online gitHub documentation; DevSecOps KT on Teams shared folder |
|            |  Be able to check differences between different commits  |  Git Wiki, online gitHub documentation; DevSecOps KT on Teams shared folder  |
|            | Knowledge about basic commands in git |  Git Wiki, online gitHub documentation; DevSecOps KT on Teams shared folder |
|            |  Understand difference between Git and GitHub  |   online gitHub documentation  |
|            |  Understand how to resolve conflicts on pull requests |  Git Wiki, online gitHub documentation; DevSecOps KT on Teams shared folder |
|            | Understand global configuration variables | online gitHub documentation; Git Wiki (ex. git config --global) |
|            | Know how to clone a Git repository |   DevSecOps KT on Teams shared folder |
|            | Understand differences between git fetch and git pull  |  online gitHub documentation |
|            | Be able to configure https proxy as well as http proxy |  Git Wiki, online gitHub documentation; DevSecOps KT on Teams shared folder  |
|            |  Backport changes from one git repository version to another or between branches |  Git Wiki, online gitHub documentation; DevSecOps KT on Teams shared folder |
|            |  Understand "cherry pick" method when backporting |  DevSecOps KT on Teams shared folder  |
|            |  Knows how to verify and fix Lint issues |   Online lint documentation |
|            |  Can check what is the base branch of a pull request | Git Wiki, online gitHub documentation; DevSecOps KT on Teams shared folder |
|            |  Knows about the default repository clone on Ansible Core VM, its configuration and ownership | Git Wiki, online gitHub documentation; DevSecOps KT on Teams shared folder |
|            |                                     |                                     |
|  Python; Selenium; PowerShell; JavaScript  |  Be able to print logs for debug purposes  |  Atos Percipio; online documentation |
|            |  Familiarity with Python, PowerShell, JavaScript, Selenium syntax  |  Atos Percipio; online documentation |
|            |  Ability to install Python and any libraries required   |  Atos Percipio; online documentation |
|            |  Ability to install PowerShell and modules |  Atos Percipio; online documentation |
|            | Be able to create a small script in Python, PowerShell, JavaScript |  Atos Percipio; online documentation |
|            | Understand Python dictionaries to efficiently store and access data using key-value pairings   |  Atos Percipio; online documentation |
|            | Understand various Python collections such as deque, named tuples, default dicts, etc. |  Atos Percipio; online documentation |
|            | Learn how to use many common Python modules |  Atos Percipio; online documentation |
|            |  Python, PowerShell, JavaScript: knowledge of concepts like loops, variable types, functions, modules, libraries, etc. |  Atos Percipio; online documentation |
|            | Python, PowerShell: write cleaner code by turning repetitive behaviors into functions |  Atos Percipio; online documentation |
|            | Python, PowerShell, JavaScript: manipulate strings including how to index, slice, iterate and concatenate them |  Atos Percipio; online documentation |
|            |  Python, PowerShell: read, write and manipulate text, CSV and JSON files |  Atos Percipio; online documentation |
|            | Understand PowerShell cmdlets |  Atos Percipio; online documentation |
|            |  Be able to work with PowerShell PowerCLI to manage VMware components: vCenter, vSAN, etc. |  Atos Percipio; online documentation |
|            |  Understand Selenium code in order to update it |  Atos Percipio; online documentation |
|            |  Selenium: learn how to write scripts using Selenium WebDriver in Python |  Atos Percipio; online documentation |
|            | Selenium: learn how to handle different HTML elements like form, table, alert, frame, and dropdown |  Atos Percipio; online documentation  |
|            |  Selenium: learn about design patterns like the page object model, data-driven tests, and adding assertions |   Atos Percipio; online documentation |
|            |  JavaScript: understand and be able to write and troubleshoot code on a vRO workflow |  Atos Percipio; online documentation   |
|            |  Ability to read structures like JSON and XML and translate them so would be human readable |  Atos Percipio; online documentation  |
|            |                                     |                                     |
|  Capable of learning and assessing new technologies  |  **Examples of new technologies/appliences we had to asses:** |                                     |
|            |  **Understand and work with Cyberark:**   |                                     |
|            |  - Import new accounts and passwords in safes |  DevSecOps KT on Teams shared folder |
|            |  - Update a password |  DevSecOps KT on Teams shared folder |
|            | Understand, asses and test SOXDB solution |   While assessing jira story   |
|            | Understand OVAL reporting  |    Jira story; gitHub Playbook    |
|            |                                     |                                     |
|  Capable of understanding uprades and be able to test them in dev env. |    Product upgrades: vRA/vRO, LCM etc.  |  Git WI Documentation  |
|            |  Product patches: vRA; vROps; vCenter; ESXi |   Git WI Documentation    |
|            |                                     |                                     |
|  Advanced linux troubleshooting knowledge  |   Shell scripting: understand shell environment variables  |  Online documentation |
|            | Shell scripting: write a simple bash script |  DevSecOps KT on Teams shared folder; Atos Percipio; Online ubuntu documentation |
|            |  Able to identify bash configuration files |  Ubuntu online documentation |
|            |  Package management: ex. Dpkg, APT |  DevSecOps KT on Teams shared folder; Atos Percipio; Online ubuntu documentation  |
|            | Check and list what processes are running, start and kill a process |  Ubuntu online documentation |
|            |  Identify running time-consuming tasks using different tools: top, atop, htop |   Ubuntu online documentation |
|            |   Be able to configure IP and a new network card  |  Ubuntu online documentation  |
|            |   Understand logrotation concept and how to configure it  |     Ubuntu online documentation  |
|            |  Understand standard linux security tools: firewall; iptables; Firewalld; security gates |  DevSecOps KT on Teams shared folder; Atos Percipio; Online ubuntu documentation |
|            |  Understand what is system hardening  |    Ubuntu online documentation  |
|            |  Detecting and preparing hardware |  Atos Percipio; ubuntu online documentation  |
|            |  Manage users and groups and set up Kerberos authentication |  Atos Percipio; ubuntu online documentation  |
|            |  Set up and troubleshoot mail: postfix  |   DevSecOps KT on Teams shared folder; Atos Percipio; Online ubuntu documentation  |
|            |  Check connection and network issues for the server itself  |  DevSecOps KT on Teams shared folder; Atos Percipio; Online ubuntu documentation  |
|            |  Diagnose a connection to a server   |  Atos Percipio; ubuntu online documentation  |
|            |  Perform remote administration: ssh; telnet,etc  |  Atos Percipio; ubuntu online documentation |
|            |  Be able to reset root password using grub |  Atos Percipio; ubuntu online documentation  |
|            |  Run commands to check system resources, such as memory usage, run levels, boot loaders, and kernel modules  | Atos Percipio; ubuntu online documentation  |
|            |  Be able to match text with Regular Expressions (regex) |   Atos Percipio; ubuntu online documentation |
|            |   Check name resolution  |   Atos Percipio; ubuntu online documentation |
|            |   Check user activity  |   Online documentation  |
|            |   Be able to read/find relevant logs, understand how cron works  |  DevSecOps KT on Teams shared folder; Online ubuntu documentation  |
|            |  Understand, troubleshoot and fix integration issues between nessus , srs and Ansible over the reports (postfix troubleshooting) | DevSecOps KT on Teams shared folder |
|            |                                     |                                     |
|  To be able to interact with iDRAC and bmc |  Understand how iDRAC can be integrated with SAaCon/Citrix and Ansible  |   DevSecOps KT on Teams shared folder  |
|            | Understand how to upgrade software of an hardware component (GSA tool)  |  Broadcom online documentation  |
|            |  Understand how external vCenter access can be allowed from SAaCon for different types of accounts   |  DevSecOps KT on Teams shared folder   |
|            |  Understand and be able to implement password rotation scenarios for BMC/iDRAC/IPMI/etc, either ran from Ansible/SAaCon |  DevSecOps KT on Teams shared folder   |
|            | Can fetch logs/dumps for vendor cases |  DevSecOps KT on Teams shared folder  |
|            |                                     |                                     |
|  DHC |  Knowledge of the DHC Design and documentation |  Design git documentation |
|            | Knowledge about DHC Processes and customers change management |   Useful links internal documentation: build guide, etc. |
|            |  Ability to read the DHC codebase as well as be aware of potential risks if anything would be changed |  DevSecOps KT on Teams shared folder; Atos Percipio; Online ubuntu documentation |
|            |  Knowledge about DHC Stack and at least basic knowledge of each of the components (Avamar, nessus, Infoblox etc.) |  Useful links internal documentation: build guide, etc. |
|            |  Ability to troubleshoot all known issues as well as the new ones without a lot of help from outside (if the issue is not a major one) |  Useful links internal documentation: build guide, etc.; SNOW KB articles |
|            |  Can validate if the firewalls are open and knows how to manage them in DHC  |  DevSecOps KT on Teams shared folder; Atos Percipio; Online ubuntu documentation  |

# Training KTs sources

In order to support the new developer with the above trainings, a list of resource links was prepared. The list includes multiple knowledge trainings and work instructions.

| Training resource                       | Link       |
|:---------------------------------------|:----------|
| Git WI Documentation                      | [DHC-Documentation](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation)   |
| Git Wiki                      | [DHC-Documentation/wiki](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/wiki)   |
| DevSecOps KT on Teams shared folder                      | [sharepoint.com](https://atos365.sharepoint.com/sites/DHCDevSecOpsTeam/Shared%20Documents/Forms/AllItems.aspx?FolderCTID=0x0120008F69FA7C60984F4E974CB6E8770AD5A1&id=%2Fsites%2FDHCDevSecOpsTeam%2FShared%20Documents%2FGeneral)  |
| SNOW KBs                      | [atosglobal.service-now.com/kb](https://atosglobal.service-now.com/now/nav/ui/classic/params/target/%24knowledge.do)        |
| Confluence KT Area                      | [Confluence/KT+Area](https://msdevopsconfluence.fsc.atos-services.net/confluence/spaces/DPC/pages/322076904/KT+Area)        |
| Online Ansible documentation                      | [docs.ansible.com](https://docs.ansible.com/)        |
| Online lint documentation                      | [ansible.readthedocs.io](https://ansible.readthedocs.io/projects/lint/); [yamllint.readthedocs.io](https://yamllint.readthedocs.io/en/stable/)        |
| Git SFA vRA documentation                      | [MAN-SFA/vRA-Documentation](https://github.com/MAN-SFA/vRA-Documentation/tree/AMC-4332/blacklisted%20servers)        |
| Iteration reviews KTs                      | [sharepoint.com/reviews](https://atos365.sharepoint.com/sites/Dev-VMware-Communication/Shared%20Documents/Forms/AllItems.aspx?newTargetListUrl=%2Fsites%2FDev%2DVMware%2DCommunication%2FShared%20Documents&viewpath=%2Fsites%2FDev%2DVMware%2DCommunication%2FShared%20Documents%2FForms%2FAllItems%2Easpx&id=%2Fsites%2FDev%2DVMware%2DCommunication%2FShared%20Documents%2FRecordings%2FIteration%20Review&viewid=f2bcb347%2Dd499%2D4a86%2Da8af%2D81c8d55240c8&OR=Teams%2DHL&CT=1700228856231&clickparams=eyJBcHBOYW1lIjoiVGVhbXMtRGVza3RvcCIsIkFwcFZlcnNpb24iOiIyNy8yMzA5MjkxMTIwOCIsIkhhc0ZlZGVyYXRlZFVzZXIiOmZhbHNlfQ%3D%3D)        |
| Broadcom developer portal                      | [developer.broadcom.com](https://developer.broadcom.com/xapis/VMware-cloud-foundation-api/latest/sddc/)        |
| Broadcom online documentation                      | [techdocs.broadcom.com](https://techdocs.broadcom.com/)        |
| Online gitHub documentation                     | [docs.github.com](https://docs.github.com/en)        |
| Atos Percipio                      | [atos.percipio.com](https://atos.percipio.com/)        |
| PI KTs                      | [sharepoint/PI/recordings](https://atos365.sharepoint.com/sites/CESCTODHCSAFePIplanning/Shared%20Documents/Forms/AllItems.aspx?csf=1&web=1&e=Qh1Ced&OR=Teams%2DHL&CT=1738762490685&clickparams=eyJBcHBOYW1lIjoiVGVhbXMtRGVza3RvcCIsIkFwcFZlcnNpb24iOiI0OS8yNDEyMDEwMDIyMSIsIkhhc0ZlZGVyYXRlZFVzZXIiOmZhbHNlfQ%3D%3D&CID=ee877ea1%2De0c5%2Db000%2D8976%2D946ccb1b28b4&cidOR=SPO&FolderCTID=0x0120009EE746B01E08254C8E3CAB95B8A54EB3&id=%2Fsites%2FCESCTODHCSAFePIplanning%2FShared%20Documents%2FDHC%20Inspect%20and%20Adapt%2FRecordings)        |
| Online ubuntu documentation                      | [help.ubuntu.com](https://help.ubuntu.com/)        |
| How to create a JIRA ticket                      | [Confluence/create-tickets](https://msdevopsconfluence.fsc.atos-services.net/confluence/spaces/DPC/pages/482541602/%E2%9A%99%EF%B8%8FHow+to+create+Jira+tickets+in+the+Environments+Team+backlog)        |

# Code and Process useful links

Further useful documents related to process and work/code standards can be found below:

| Code and Process document                   |   Link   |
|:-------------------------------------------|:--------|
| DHC Releases, DHC Version Matrix          | [DHC-Documentation/wiki/LCM-Version-Matrix](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/wiki/LCM-Version-Matrix)   |
| DHC Lab Environments                    | [conflusence/lab-environments](https://msdevopsconfluence.fsc.atos-services.net/confluence/pages/viewpage.action?spaceKey=DPC&title=DHC+LAB+environments)   |
| Coding Standards                    | [DHC-Documentation/wiki/Coding-standards](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/wiki/Coding-standards)   |
| Documentation Standards                    | [DHC-Documentation/wiki/Documentation-Standards](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/wiki/Documentation-Standards)   |
| Naming Convention                    | [DHC-Documentation/design/naming-convention](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/develop/design/namingConvention.md)   |
| Git Code Development Process                    | [DHC-Documentation/wiki/DHC-Code-Development-Process](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/wiki/DHC-Code-Development-Process)   |
| Code Review Process                    | [DHC-Documentation/wiki/DHC-Code-Review-Process](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/wiki/DHC-Code-Review-Process)   |
| Build guide                    | [workInstructions/dhcBuildGuide](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/develop/workInstructions/dhcBuildGuide.md)   |
| Git commit standard                    | [DHC-Manage/develop](https://github.com/GLB-CES-PrivateCloud/DHC-Manage/blob/develop/hooks/commit-msg)   |
| Wiki Documentation                    | [DHC-Documentation/wiki](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/wiki)   |
| Atos Process Documentation, including SNOW config and CMDB templates   | [sharepoint/process/documentation](https://atos365.sharepoint.com/sites/600002886/CFGM/sitepages/process%20documentation.aspx?RootFolder=%2Fsites%2F600002886%2FCFGM%2FSACM%20Process%20Documents%2FCMDB%20Update%20Templates%2FSNOW%20CMDB&FolderCTID=0x012000FC3D3810BC29C04FA9A4BFEC8B90BB43&View=%7B9E8F5830-A142-4DE9-B29B-63149EBD170F%7D&xsdata=MDV8MDF8fGYyZmIwMWM3ZDE0MTQ4ZjBmYzMyMDhkYjJmNmFjMjAyfDMzNDQwZmM2YjdjNzQxMmNiYjczMGU3MGIwMTk4ZDVhfDB8MHw2MzgxNTU5MDc3NzEwNzE1Mzh8VW5rbm93bnxWR1ZoYlhOVFpXTjFjbWwwZVZObGNuWnBZMlY4ZXlKV0lqb2lNQzR3TGpBd01EQWlMQ0pRSWpvaVYybHVNeklpTENKQlRpSTZJazkwYUdWeUlpd2lWMVFpT2pFeGZRPT18MXxNVFkzT1RrNU16azNOamMyTlRzeE5qYzVPVGt6T1RjMk56WTFPekU1T2pFd1pXUTRPVE0xTFRnMU9EY3RORFEyWlMxaE5EUXhMVFpsWXprNU16SmpZVFJpWWw4M1pESXdOek0wTmkxaU1XTTNMVFF5T0dFdFlUZzRZUzAyWmpjNE1XWmxOREZpTTJOQWRXNXhMbWRpYkM1emNHRmpaWE09fGU5NWNiMmM1NWQ0ZTQ5YzFmYzMyMDhkYjJmNmFjMjAyfDQ1YTFjNTk2Njg1NzRlMDk5NDk5MTliZGMzNjM3OGNm&sdata=TGU0dGxra3lJYjIxaGxLRVkzVWdtTzVydTlNcXRiSHc3c1U4dk1qS0VBWT0%3D&ovuser=33440fc6-b7c7-412c-bb73-0e70b0198d5a%2Cadriana.slabu%40atos.net&OR=Teams-HL&CT=1705937146738&clickparams=eyJBcHBOYW1lIjoiVGVhbXMtRGVza3RvcCIsIkFwcFZlcnNpb24iOiI0OS8yMzExMzAyODcyNCIsIkhhc0ZlZGVyYXRlZFVzZXIiOmZhbHNlfQ%3D%3D)   |

# Design useful links

For a better understanding of how DHC works, consult the following documents:

| Design document                         |   Link   |
|:---------------------------------------|:--------|
| High level Design             | [DHC-Documentation/design/hldDigitalHybridCloud](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/develop/design/hldDigitalHybridCloud.md)   |
| LLD Software Defined Networks             | [DHC-Documentation/design/lldSoftwareDefinedNetworks](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/develop/design/lldSoftwareDefinedNetworks.md)   |
| LLD Cloud Automation Services, Role Based Access Control, Monitoring and Logging, Active Directory             | [DHC-Documentation/design/lldCloudAutomationServices](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/develop/design/lldCloudAutomationServices.md)   |
| LLD Role Based Access Control             | [DHC-Documentation/design/lldDhcRoleBasedAccessControl](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/develop/design/lldDhcRoleBasedAccessControl.md)   |
| LLD Monitoring and Logging             | [DHC-Documentation/design/lldMonitoringLogging](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/develop/design/lldMonitoringLogging.md)   |
| LLD Active Directory             | [DHC-Documentation/design/lldActiveDirectory](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/develop/design/lldActiveDirectory.md)   |
| DHC Overview - History, Features, Links, Way of Working             | [DHC-Documentation/workInstructions/dhcBuildGuide](https://github.com/GLB-CES-PrivateCloud/DHC-Documentation/blob/develop/workInstructions/dhcBuildGuide.md)   |

# Changelog

| Date       | Author                 | Description              |
|:--------- |:--------------------- |:----------------------- |
| 19.02.2025 | Lupu Adriana        | First version            |
