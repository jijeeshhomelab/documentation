# Managing VMware Tools Location for VMware Hosts

Changelog

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1     | 10/04/2025 | Adrian Iulian Chiriac | Initial version |

## Introduction

### Purpose

This document provides step-by-step instructions to:

1. Create a new location to hold the VMware Tools images.
2. Set a specific path for the ProductLocker location on all hosts in a cluster.
3. Query the ProductLocker location for all hosts in a cluster.

### Audience

- DHC Engineers
- DHC Operations
- Integration Architects

### Scope

This document covers configuring the following items:

- VMware Tools

### Related Documents

|          Documentation         |
|--------------------------------|
| For more details, refer to the official VMware blog post: [Configuring a VMware Tools Repository in vSphere 6.7U1 – VMware vSphere Blog](https://blogs.vmware.com/vsphere/2019/01/configure-a-vmware-tools-repo-in-vsphere-6-7u1.html). |

## Configuration Steps

1. Open your vSphere Client and navigate to your vSAN Datastore.
2. Create a new directory to hold the VMware Tools images:

   ```shell
   /vmfs/volumes/vsanDatastore/vmtoolsRepo
   ```

3. Download the latest VMware Tools bundle from [VMware Tools Download](https://www.vmware.com/go/tools).
4. Extract the bundle into the newly created directory. Ensure that both the `vmtools` and `floppies` directories are present in the extracted location.
5. Make a note of the full path to this directory (e.g., `/vmfs/volumes/vsanDatastore/vmtoolsRepo`), as it will be used in subsequent steps.

### Pre-configuration

- Access to a vSphere environment with the required permissions.
- PowerCLI installed on your workstation.
- Knowledge of the cluster name and desired ProductLocker path.
- Access to a vSAN Datastore or other storage location.

### Actual configuration

1. Open PowerShell with administrative privileges.
2. Connect to your vCenter server using PowerCLI:

   ```powershell
   Connect-VIServer -Server <vCenterServer>
   ```

3. Run the following script to set the ProductLocker location for all hosts in the specified cluster:

   ```powershell
   $allhosts = get-cluster "clustername" | get-vmhost

   foreach ($hostname in $allhosts) {
       Get-VMhost -Name $hostname | % { $_.ExtensionData.UpdateProductLockerLocation_Task("/vmfs/volumes/vsanDatastore/vmtoolsRepo") }
   }
   ```

   - Replace `"clustername"` with the name of your cluster.
   - Replace `"/vmfs/volumes/vsanDatastore/vmtoolsRepo"` with the path to your VMware Tools repository.

4. Verify that the script completes without errors.

### Post-configuration

1. Open PowerShell with administrative privileges.
2. Connect to your vCenter server using PowerCLI:

   ```powershell
   Connect-VIServer -Server <vCenterServer>
   ```

3. Run the following script to query the ProductLocker location for all hosts in the specified cluster:

   ```powershell
   $allhosts = get-cluster "clustername" | get-vmhost

   foreach ($hostname in $allhosts) {
       Get-VMhost -Name $hostname | % { $_.ExtensionData.QueryProductLockerLocation() }
       Write-Host $hostname -ForegroundColor Green
   }
   ```

   - Replace `"clustername"` with the name of your cluster.

4. Review the output to confirm the ProductLocker locations.
