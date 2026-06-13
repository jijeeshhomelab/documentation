# Changelog

| Version | Date       | Author         | Changes           |
|---------|------------|----------------|-------------------|
| 0.1     | 10.08.2023 | Pawel Zurawski | Document creation |

## Introduction

### Purpose

Explains the process of adding new Security Objects to NSX-T, including: Services,Security Groups,Security Rules

### Audience

- VCS Engineers
- VCS Architects

### Add new Security Objects to NSX-T such as Services, Security Groups, Security Rules

NSX-T objects such as Services, Security Groups, and Security Rules are created using ansible automation based on YAML files stored in DHC-Firewall/microsegmentationImports

Any changes in any file stored in the DHC-Firewall repository need approval from at least one of the persons mentioned in the CODEOWNERS file under DHC-Firewall repository

It is required that any optional feature like DR, or TANZU will have a separate YAML file that describes additional elements that need to be created.

Additional separate YAML files are required for Management NSX-T and Workload NSX-T.

Elements in the "feature" file must not be duplicated with elements from the mdNsxt.yml file.

Each of the YAML files needs to consist of 4 sections as described below.

```yaml
###### List of services
mdServices:
  - displayName: "<Service Name>"
    protocol: "<protocol>"
    destinationPort: "<port_number>"

##### List of security groups with IP
mdSecurityGroupsIp:
  - description: "<description>"
    name: "<security group name>"
    members: ["<member IP address>"]

##### List of security groups based on dynamic membership
mdSecurityGroupsDynamic:
  - description: "Internal Proxy_APPLYTO"
    name: "{{ customerCode }}seg001_APPLYTO"
    members: ["<member sufix>"]

##### Distributed Firewall rules
    
mdDfwRules:
  - name: "<rule name>"
    section: "<dfw section name>"
    source: [<security group name>]
    destination: [<security group name>]
    service: "<service name>"
    action: "<action>"
    applyto: [<security group name OR ANY>]
    justification: "<short description why this rule is needed>"
    requestedBy: "<requested DAS ID>"
    jiraStoryNr: "<JIRA STORY NUMBER>"
```

The YAML file needs to be executed by Ansible automation.

Current list of Ansible playbooks that are using YAML files from the DHC-Firewall repository:

| DHC-Firewall YAML file     | ansible playbook using that file                           |
|----------------------------|------------------------------------------------------------|
| mdNsxt.yml                 | DHC-Deploy/createMgmtNsxtMicrosegmentation.yml             |
| mdNsxtVxr.yml              | DHC-Deploy/createMgmtNsxtMicrosegmentation.yml             |
| mdTanzuNsxt.yml            | DHC-Manage/createTanzuMgmtNsxtMicrosegmentation.yml        |
| wdNsxt.yml                 | DHC-Deploy/createWdNsxtMicrosegmentation.yml               |
| wdNsxtAdditionalDomain.yml | DHC-Manage/createMgmtNsxtMicrosegmentationAdditionalWL.yml |
| wdTanzuNsxt.yml            | DHC-Manage/createTanzuWdNsxtMicrosegmentation.yml          |
