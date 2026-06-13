# Expand Vcf VxRail Nsx-T Edge Cluster

# Changelog

| Date    | TOS        |  Issue             | Author          | Description          |
| ------- | ---------- | ------------------------ | --------------- | --------------- |
| 12/07/2022 | DHCVXR1.0 | First version | Arun Sompura |  Initial document |

# Introduction

This document describes the step-by-step instructions for adding a new edge node(s) to an existing VCF VxRail Edge cluster. This document is intended for the DevSecOps engineers tasked with implementing it.The NSX-T Edge nodes are deployed as a part of edge cluster. Because of the shared nature of the cluster, As the cluster compute workloads are added it is recommended to scale edge cluster.

# Scope

Adding a new edge node(s) to the existing edge cluster includes the 2 main areas:

- Taking all necessary inputs from DevSecOps team before and during playbook execution
- Deployment of edge node(s) based on dynamic inputs.

# Related Documents

| Document |
| -------- |
| |

# Assumptions

There is an assumption that the engineers following this process have an understanding of VMware VCF on VxRail.  
The assumption is that the edge cluster is already available for expansion.  
All playbooks mentioned in this document are located in the *manage* folder in the GIT repository.

**DISCLAIMER!** All screenshots are for illustrative purposes only.

# Infrastructure Requirements

At least one NSX-T Edge Cluster is available in management workload domain or VI workload domain or both before starting with expansion.
Minimum 1 edge node to maximum 4 node(s) can be expanded for a vcf nsx-t edge cluster. Total count of edge nodes for specific edge cluster should not exceed 6.

# Network Requirements

The Nsx-T Edge node(s) need to be added to an existing edge cluster which requires management IP, TEP IPs and TEP gateway, and TEP vLAN details to be updated in input file.

## Step 1 - Prepare Input file for edge cluster expansion

First step of adding a new edge node to the cluster is to update an input file  **expandVcfNsxtEdgeInputVars.yml**
Edge node(s) input file expandVcfNsxtEdgeInputVars.yml is located in /home directory of a user that runs the playbook.  
The expandVcfNsxtEdgeInputVars.yml file should be placed in the user home directory and needs to have a dictionary variable containing information about the edge nod name, TEP IP, TEP gateway, TEP vLAN and the octet for all new edge node(s) to be added.

For adding a node(s) below is the example input file:

```yaml
edgeNode:
  edg103:
    name: "gre02edg103"
    octet: 98
    cidr: "172.22.128"
    managementGateway: 172.22.128.1
    edgeTep1IP: 172.22.132.26
    edgeTep2IP: 172.22.132.27
    edgeTepGateway: 172.22.132.1
    edgeTepVlan: 2804
  edg104:
    name: "gre02edg104"
    octet: 99
    cidr: "172.22.128"
    managementGateway: 172.22.128.1
    edgeTep1IP: 172.22.132.28
    edgeTep2IP: 172.22.132.29
    edgeTepGateway: 172.22.132.1
    edgeTepVlan: 2804
```

The playbook *expansionOfNsxtEdgeCluster.yml* is commissioning edge node(s) to Nsx-T edge cluster using Role which will trigger SDDC Manager REST API.

The playbook contains 3 main parts:

1. Creating DNS entries for new edge node(s) and gather appropriate input variables (file expandVcfNsxtEdgeInputVars.yml and user prompts)
2. Update the inventory (nodes) and "group_vars/all" files with the new entries for the additional nodes.
3. Perform the expansion of edge cluster using SDDC manager REST API.

Apart from the username and password required for accessing Hashivault, the *expansionOfNsxtEdgeCluster.yml* playbook requires a number of inputs in order to expand edge cluster according to the requirements :

| Input/Variable | Description |
| -------- | ------- |
| edgeNodesPassword | Password for the edge node(s) |
| edgeNodeNumbers | Number of the edge node(s) to be added min 1 to max 4 |
| workloadClusterName | Provide workload cluster name for which edge cluster needs to be expanded. |
| hostSite | The value should be set to "primary" or "Secondary" |
| edgeNodeType | The edge node type - either Compute or Management. Possible values - cmp/mgt |
| edgeClusterNumber | By default it's set to "02" because the first cluster is deployed during the initial build. This is applicable only to a CMP cluster type |
| edgeClusterName | Either a Compute or a Management cluster. Possible values: cmp/mgt. Default value: cmp |  
| workloadClusterName |  The hostname of the ESXi host (without FQDN) |

## Step 2 - Create edge node expansion json based on inputs

Once the Inputs has been provided role **dhcvxr-expandVcfNsxtEdgeCluster** will be called in the playbook which has a subtask to convert inputs into a json file which than can be used to trigger SDDC REST API call for edge cluster expansion. Task for JSON creation is *createNsxtEdgeClusterExpansionJson.yml* which gets executed as part of playbook *expansionOfNsxtEdgeCluster.yml*

JSON creation task yml:

```yaml
- name: create Vcf NSX-T Edge cluster expansion JSON file from template
  template:
    src: expandVcfNsxtEdgeCluster.j2
    dest: "/home/{{ lookup('env', 'USER') }}/expandVcfNsxtEdgeCluster.json"
  become: yes
```

## Step 3 - Edge Cluster expansion and Compliance

Once the json file gets triggered next step of execution is expansion of edge cluster which will validate edge cluster expansion based on JSON post successful validation edge cluster expansion will begin.

```yaml
- name: Validate JSON input for NSX-T Edge cluster expansion
  import_tasks: validateNsxtEdgeClusterExpansion.yml

- name: Expand NSX-T Edge cluster based on JSON
  import_tasks: expandNsxtEdgeCluster.yml
 ```

Once it completes successfully Edge password rotation task gets triggered via SDDC manager.
