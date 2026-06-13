# VSAN Witness Appliance

## Changelog

| Date       | TOS     | Issue                                                                                   | Author      | Description                                                                                                                                                                                                 |
|------------|---------|-----------------------------------------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 25/11/2019  |         |                                                                                      | Przemyslaw Bojczuk | First version |
| 27/06/2022  |         | CESDHC-185                                                                           | Marcin Gala | Removed activity related to ESXi Shell enablement as it is not required to configure vSAN Witness Appliance |

## Introduction

### Purpose

Install vSAN Witness appliance.

### Audience

- VCS Operations
- VCS Engineers

### Scope

The scope of this document covers the following:

  1. vSAN Witness Appliance deployment requirements.
  2. Installation and initial configuration steps.

## vSAN Witness Appliance requirements

The purpose of the Witness host is to store witness components for virtual machine objects, that will be used to obtain quorum in Split-Brain failure scenario allowing one AZ (Avability Zone) to act as an isolated site and the second AZ as an active site.  The vSAN Witness host can be either a physical ESXi host or packaged vSAN Witness Appliance (pre-configured virtual machine which runs ESXi and is distributed as an OVA template).
Deploying the vSAN Witness Appliance is the recommended deployment choice for a vSAN Witness Host in VMware Cloud Services. There is a maximum of 1 Witness host per vSAN Stretched Cluster.  
**A vSAN Witness Appliance is provided with each release of vSAN. Upon initial deployment of the vSAN Witness Appliance, it is required to be the same as the version of vSAN.**  
The vSphere host that the vSAN Witness Appliance runs on, is not required to be the same version.

## Licensing

A license is hard coded in the vSAN Witness Appliance and is provided for free by VMware. No additional licenses are needed.
Physical ESXi acting as vSAN Witness do not have embedded license, as such standard or upper license is needed. vSAN Witness is not part of vSAN cluster and do not need vSAN license.

## Version

A vSAN Witness Appliance is provided with each release of ESXi. The underlying vSphere version is the same as the version running vSAN. Upon initial deployment of the vSAN Witness Appliance, it is required to be in the same as the ESXi version in VCS environment.

## vSAN Witness Appliance Size

When using a vSAN Witness Appliance, the size is dependent on the configurations and this is decided during the deployment process. For a vSAN Witness Appliance Size this will depend on the number of components and base on the development testing scenario. All deployment sizes are listed below:

1. Medium - Supports up to 500 VMs/21,833 Witness Components
    - Compute - 2 vCPUs
    - Memory - 16GB vRAM
    - ESXi Boot Disk - 12GB Virtual HDD
    - Cache Device - 10GB Virtual SSD
    - Capacity Device - 350GB Virtual HDD

2. Large - Supports over 500 VMs/64,000 Witness Components
    - Compute: 2 vCPUs
    - Memory - 32 GB vRAM
    - ESXi Boot Disk - 12GB Virtual HDD
    - Cache Device - 10GB Virtual SSD
    - Capacity Devices - 3x350GB Virtual HDD
    - 8GB ESXi Boot Disk*, one 10GB SSD, three 350GB HDDs
    - Supports a maximum of 64,000 witness components

vSphere 7.0 introduced `Extra Large` size which supports up to 64,000 Witness Components and is intended for very large environments - up to 64 vSAN two-node clusters.

## Network

Please see lldSoftwareDefinedNetworks for Network requirements.

Sections:

- Requirements for Underlay Network - Stretched Clusters
- DataCenter failure - Stretched Clusters scenario

## Storage

Cache Device Size: Each vSAN Witness Appliance deployment option has a cache device size of 10GB. This is sufficient for each for the maximum of 64,000 components. In a typical vSAN deployment, the cache device must be a Flash/SSD device. Because the vSAN Witness Appliance has virtual disks, the 10GB cache device is configured as a virtual SSD. There is no requirement for this device to reside on a physical flash/SSD device. Traditional spinning drives are sufficient.

Capacity Device Sizing: capacity device can support up to 21,833 components. vSAN Stretched Cluster can support a maximum of 64,000 components.  Each Witness Component is 16MB, as a result, the largest capacity device that can be used for storing of Witness Components is approaching 350GB.

## Location

As design by VMware vSAN witness needs to be deployed on third location separate from the location of the other two availability zones. The vSAN Witness Appliance must run on an ESXi 5.5 or greater host. Several scenarios are supported officially:

It can be run in any of the following infrastructure configurations (provided appropriate networking is in place):

On a vSphere environment backed with any supported storage (vmfs datastore, NFS datastore, vSAN Cluster)
On vCloud Air/OVH backed by supported storage
Any vCloud Air Network partner hosted solution
On a vSphere Hypervisor (free) installation using any supported storage (vmfs datastore or NFS datastore)
In VMware Cloud on AWS software-defined data center (SDDC)

## Deploying a vSAN Witness Appliance

Important thing is that the witness appliance does not run virtual machines. Its only purpose is to serve as a vSAN witness. The vSAN Witness Appliance must be deployed on different infrastructure than the Stretched Cluster itself.

### Important

Please note that mentioned below `Management` network is located in a 3rd party site and is not VCS management network. Management network required for vSAN Witness Appliance deployment is totally different network than VCS management and is fully in scope of datacenter team, which is also obliged to deliver IP Address, Netmask, Gateway and PortGroup details such as VLAN-id, required for vSAN Witness deployment.
FQDN, DNS Servers, NTP Servers need to be compliant with VCS Design. VCS Management POD Active Directory Domain Controller servers will act as DNS and NTP servers for vSAN Witness.
Only limitations for 3rd party site are:

- Can not be the same location as either of vSAN Cluster availability Zones. vSAN Witness is part of DR scenario, and as such need to be separated from both vSAN cluster Availability Zones.
- Network (lldSoftwareDefinedNetworks), Storage and Compute requirements need to be met.

**Additionally, please note that each vSAN cluster needs to have a separate Witness appliance. This means, that two Witness appliancess need to be deployed - one for Manageement and one for Compute.**

The workflow to deploy and configure the vSAN witness appliance includes:

1. Download the appliance (OVA file) from the VMware website.
    - > A vSAN Witness Appliance is provided with each release of ESXi. Upon initial deployment of the vSAN Witness Appliance, it is required to be the same as the version of ESXi in clustered environment.
2. Deploy the appliance to a other vSAN host or cluster:
    - Set a name and select location (folder) for the virtual machine. Select a Datacenter for the vSAN Witness Appliance to be deployed to and provide a name for vSAN witness VM.
    - Select a compute resource/cluster for the vSAN Witness Appliance to reside on.
    - Review the details of the deployment and accept the license agreements.
    - Made a decision regarding the expected size of the Stretched Cluster configuration: medium or large. Default is medium.
    - Select a datastore for the vSAN Witness Appliance. This will be one of the datastore available to the underlying physical host. vSAN Witness Appliance can be deployed as thick or thin, as thin VMs may grow over time, so ensure there is enough capacity on the selected datastore.
    - Select a destination network for the Management Network. Select destination network for the Secondary Network (Witness Network) as well but according to `Design Decisions - DataCenter failure` specified in lldSoftwareDefinedNetworks.md to simplicity in new cluster deployment and new VCS instance deployment, VSAN Witness host will use single interface. Secondary Network (Witness Network) vmkernel will be removed during vSAN Witness Appliance configuration by VCS team.
    - In next step customize the deployment properties of this software solution:
      - Set password for root account;
      - **In the "VSAN Traffic" section select `Management` network from drop-down menu as a network that will be used for vSAN Traffic;**
      - Provide details about Management Network: IP Address, Netmask, Gateway, DNS Domain, Witness Hostname, DNS Servers, NTP Servers.

Do not provide details for Secondary Network (Witness Network). Leave it blank.  
At this point, the vSAN Witness Appliance is ready to be deployed. It will need to be powered on manually via the vSphere web client UI later on.  
Once the OVA template is deployed and powered on, launch virtual machine console, login to vSAN Witness Appliance and enable ESXi Shell ans SSH services. Go to 'Troubleshooting Options', select 'Enable SSH' and press Enter.
Alternatively this can be done from GUI. Go to https:\\\appliance_ip, login as root, go to Manage -> Services and start 'TSM-SSH'. Change startup policy to 'Start and stop with host'.
