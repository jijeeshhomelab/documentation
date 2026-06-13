# External Reports Aria Operations

## Table of Contents

- [External Reports Aria Operations](#external-reports-aria-operations)
  - [Table of Contents](#table-of-contents)
  - [List of Changes](#list-of-changes)
  - [Introduction](#introduction)
    - [Purpose](#purpose)
  - [Automation](#automation)
    - [Collect all reports in Ansible server](#collect-all-reports-in-ansible-server)
    - [Host in Webserver](#host-in-webserver)

## List of Changes

| Version | Date       | Author       | Issue    | Changes           |
|---------|------------|--------------|----------|-------------------|
| 0.1     | 06.11.2024 | Rachel Beulah | VCS-14257 | Document creation |
| 0.2     | 17.01.2025 | Rachel Beulah | VCS-14663 | Document update |

## Introduction

### Purpose

Collect all external reports, such as RV Tools, Nessus, Patching, etc., host them on a web server, and display them as a dashboard inside vROps using the Text Widget option.

## Automation

The following playbook will collect the latest reports from external tools such as RV Tools, Nessus, Patching, etc., onto the Ansible server, convert them to HTML files,  change the file permissions, and host them on the web server:

```markdown
ansible-playbook externalReportsVrops.yml
```

### Collect all reports in Ansible server

- It will collect externally generated reports (Like RV tools, Nessus, Patching etc..) onto the Ansible server in the path ```/opt/reports/dhcReports```.

  ![image](/workInstructions/images/wiExternalReportsvROps/CollectReport.png)

**Note: NFS share is created and it is mounted in the Ansible and Webserver /opt/reports/dhcReports.**

### Host in Webserver

- The reports are hosted in the webserver's ```/var/www/html``` directory.
- The reports with different file format such as csv, xml, excel are converted into html file using python script.
- Finally restart nginx services.

Sample Report:
     ![image](/workInstructions/images/wiExternalReportsvROps/SampleNessusReport.png)
