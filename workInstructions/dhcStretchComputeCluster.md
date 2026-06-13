# Stretch Compute Cluster

# Changelog
  
| Version | Date       | Description              | Author       |
| ------- | ---------- | ------------------------ | --------------- |
| 0.1     | 05/03/2020 | First version | Łukasz Stasiak |
| 0.2     | 04/08/2020 | Version for VCF 4.0 | Maciej Losek |

## Introduction

### Purpose

Stretch the existing VCS compute cluster in accordance with Atos Global Delivery standards and portfolio services.

### Audience

- VCS Operations
- VCS Engineers

### Scope

- Stretched CMP cluster setup

## Installation Time

| Component / Task | Installation Time (HH:MM)    |
| :-------------   | :----------: |
|  Stretched CMP cluster setup | 01:30   |

# Deployment steps

## Stretch NSX-T CMP Cluster (VI WD)

Before stretching a CMP cluster on vCF vSAN ReadyNodes ensure that you have enough hosts such that there is an equal number of hosts on each availability zone. This is to ensure that there are sufficient resources in case an availability zone goes down completely.
Make sure that VMware ESXi has been install on all AZ2 hosts and hosts are properly configured (network configuration).

### Commission AZ2 ESXi hosts

Additional hosts have to be added to the existing cluster. This needs to be done running playbook `addVcfDrCmpHosts.yml`.

| Sub-Step       | Action     |
| :------------- | ----------: |
| 1.       | On ansible core VM run command `ansible-playbook addVcfDrCmpHosts.yml`.|
| 2.       | Login to `https://HashiVaultFQDN:8200` and go to Secrets -> secret -> < customerCode > -> < locationCode > -> servers|
| 3.       | First create folder and add accounts related to new Host. Click 'Create secret +'. In the 'Path for this secret' add at the end of line Compute hostname, i.e: mec09cmp005|
| 4.       | Under 'Version data' section type 'root' username as a key, and type password in the 'value' field. Click 'Add' and next click on 'Save'|

### Deploy and configure witness appliance

Deployment and configuration of a witness appliance is described in [dhcVsanWitnessAppliance.md](dhcVsanWitnessAppliance.md) document. Witness appliance should be deployed and initially configured by customer base on provided documentation. As a next step role dhc-configureVsanWitness needs to be executed to add created witness appliance to the vCenter server.

Role dhc-configureVsanWitness needs to be executed to add created witness appliance to the vCenter server.
To execute the dhc-configureVsanWitness role run the playbook on ansible core VM with following command  `ansible-playbook configureCmpVsanWitness.yml`. This will add a witness appliance to the vCenter sever based on defined variables.

### Stretch CMP cluster

To stretch the CMP cluster run playbook 'createStretchClusterCmpDomain.yml' by executing `ansible-playbook createStretchClusterCmpDomain.yml`.

### Validation

Check the vSAN health page. If vSAN HCL DB will be out of date follow the steps below to update the vSAN HCL DB.

| Sub-Step | Action     |
| :------- | :----------: |
| 1.       | Log in to vCenter server using vSphere Web Client with administrator credentials.|
| 2.       | Go to vSAN cluster and click the `Monitor` tab.|
| 3.       | In the `Monitor` tab, click `Health` under vSAN section in the left pane. Click Retest.Fix errors, if any.|
| 4.       | Int NSX-T Manager under System > Fabric > Nodes > Host Transport Nodes > < cluster > , verify that in __TEP IP Address__ section both Interfaces was assigned correct IP address. |

### Trouble shooting and know problems can be found in document: [dhcStretchedClusterTroubleshooting.md](dhcStretchClusterTroubleshooting.md)
