# exportCsv - Python module documentation

- Table of Contents
{:toc}

# 1. List of Changes

| Version | Date       | Description              | Author       |
| ------- | ---------- | ------------------------ | --------------- |
| 0.1     | 23.03.2020 | First version of documentation of Python module for ansible | Sebastian Pucek |

# 2. Introduction

It was necessary to create a process that would automate conversion of an xls or xlsx file into a csv file.
Unfortunately, the existing ansible modules did not allow us to do this type of operation.
It was important for us to solve this problem. We decided to create our own ansible module using Python.
The current version of Python that is used by Ansible on our Prereq VM is 2.7.17.
All created modules should be compatible with this version of Python.

# 3. Requirements

Module - exportCsv.py - to function properly requires two external libraries:

- pandas - in version 0.24.2
- openpyxl - in version 3.0.6

# 4. Module description

## 4.1. Libraries / modules

This module uses external libraries: *pandas* and *openpyxl*.
The source code also imports python / os and ansible modules. All of them are required.

## 4.2. Input data

This module uses input data in order to work properly. Some of these properties are required, others are optional. The list of properties is below.

```yaml
  fields = {
    "filePath": {"required": True, "type": "str"},
    "fileName": {"required": True, "type": "str"},
    "destPath": {"required": False, "default": "/opt/dhc/deploy/microsegmentationImports/", "type": "str"},
    "servSheetName": {"required": False, "default": "Services", "type": "str"},
    "sgSheetName": {"required": False, "default": "SecurityGroups", "type": "str"},
    "workSheetName": {"required": False, "default": "Workload Microsegmentation", "type": "str"},
    "servDestFileName": {"required": False, "default": "servicesNsxt.csv", "type": "str"},
    "sgDestFileName": {"required": False, "default": "securityGroupsNsxt.csv", "type": "str"},
    "workDestFileName": {"required": False, "default": "dfwRulesNsxt.csv", "type": "str"},
  }
```

The code snippet above shows, that the first three elements: *filePath*, *fileName* and *destPath* are required.
Without them the module will fail. The other variables are not required. All of them contain default values.

## 4.3. Arguments description

Below you will find information about arguments used by this module.

| Argument name | Required | Default value | Type | Description |
| ---------- | ---------- | ------------------------ | ---------- | ------------------------ |
| filePath | True | none | string | Stores information about location of the excel file - in xls or xlsx format|
| fileName | True | none | string | Stores the name of the excel file |
| destPath | True | /opt/dhc/deploy/microsegmentationImports/ | string | Stores the destination path of the output csv file |
| servSheetName | False | Services | string | The name of sheet in excel, which contains information about services |
| sgSheetName | False | SecurityGroups | string | The name of sheet in excel, which contains information about security groups |
| workSheetName | False | Workload Microsegmentation | string | The name of sheet in excel, which contains information about workload microsegmentation |
| servDestFileName | False | servicesNsxt.csv | string | The name of the target csv file with services |
| sgDestFileName | False | securityGroupsNsxt.csv | string | The name of the target csv file with security groups |
| workDestFileName | False | dfwRulesNsxt.csv | string | The name of the target csv file with rules |

# 5. Example playbook

```yaml
  - name: Export excel to csv
    hosts: localhost
    tasks:
      - name: Export data from excel to csv
        exportCSV:
          filePath: "/opt/dhc/tests/"
          fileName: "microsegmentation.xlsx"
```
