# VxRail VCF Stretch Cluster Compute

- Table of Contents
{:toc}

# Changelog
  
| Version | Date       | Description              | Author       |
| ------- | ---------- | ------------------------ | --------------- |
| 0.1     | 14/04/2020 | First version | Łukasz Stasiak|
| 0.2     | 23/04/2020 | Review        | Maciej Losek  |

# 1. Introduction

## 1.1 Purposed

The purpose of this document is to describe steps needed to stretch CMP cluster on VxRail.

## 1.2 Scope

The scope of this document covers the following:

1. VxRail nodes reset.
2. Stretch a cluster for VMware Cloud Foundation on Dell EMC VxRail.

# 2. VxRail nodes reset procedure

If needed reset VxRail nodes that will be used to expand the cluster to factory settings. This procedure is described in chapter 'VxRail 4.7 Nodes' of DPC.Next ESXi Refreshing procedure document <br><https://msdevopsconfluence.fsc.atos-services.net/pages/viewpage.action?spaceKey=DPC&title=DPC.Next+ESXi+refreshing><br>

All nodes that will be used to expand the cluster needs to be visible from the vCenter Vxrail plugin.
<br>(add instruction...)<br>

# 3. Stretch a cluster for VMware Cloud Foundation on Dell EMC VxRail

Steps to stretch a cluster for VMware Cloud Foundation on Dell EMC VxRail are already described in [VMware Cloud Foundation on Dell EMC VxRail Admin Guide](vcfOnVxrailAdministering.pdf)

NOTE!!! DNS records for all of the hosts used to expand the cluster needs to be created before proceeding with cluster expansion.

To stretch a cluster for VMware Cloud Foundation on Dell EMC VxRail, perform the steps described in chapter 'Stretch a Cluster for NSX-T in VMware Cloud Foundation 3.9' in following order:

1. Execute step 1 and 2 to prepare existing cluster to be stretched.

2. Deploy VSAN Witness appliance described in chapter 'Deploy and configure witness appliance' in [dhcVsanWitnessAppliance](dhcVsanWitnessAppliance.md)

3. Execute 'Deploy and configure witness appliance' step from [dhcStretchComputeCluster](dhcStretchComputeCluster.md) document.

4. Execute step 4 from [vcfOnVxrailAdministering](vcfOnVxrailAdministering.pdf) chapter 'Stretch a Cluster for NSX-T in VMware Cloud Foundation 3.9' to expand cluster with Availability Zone 2 hosts in vCenter.
   More details how to expand cluster in vCenter are described in step 2 of chapter 'Expand the VxRail Cluster'.

   For the ESXi Management Username same account as for existing management cluster needs to be used. This account should be already added to Vault during management cluster creation for each existing ESXi MGMT hosts. Follow steps below to add entry for new hosts:

   - Login to <https://HashiVaultFQDN:8200>
   - Go to Secrets -> secret -> < customerCode > -> < locationCode > -> servers
   - First create folder and add accounts related to new Host. Click 'Create secret +'
   - In the 'Path for this secret' add at the end of line Management hostname, i.e: mec09mgt005
   - Under 'Version data' section type 'root' username as a key, and type password in the 'value' field. Click 'Add'
   - Type 'management' username as a key, and type password in the 'value' field. Click 'Save'

5. Execute step 5 from [vcfOnVxrailAdministering](vcfOnVxrailAdministering.pdf) chapter 'Stretch a Cluster for NSX-T in VMware Cloud Foundation 3.9' to add a hosts in SDDC Mannager.
   More details how to add hosts to cluster in SDDC Manager are described in chapter 'Add the VxRail Hosts to the Cluster in VMware Cloud Foundation'.

6. Next step is to stretch the cluster in vCenter and to configure vSphere HA options. Please execute steps described in [dhcStretchComputeCluster](dhcStretchComputeCluster.md) document, chapter 'Stretch CMP cluster'.

7. To validate the stretched cluster creation follow the step 12 'Validate that stretched cluster operations are working correctly by logging in to the vSphere Web
Client.' from [vcfOnVxrailAdministering](vcfOnVxrailAdministering.pdf) document.
