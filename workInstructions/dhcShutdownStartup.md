# VCS Startup and Shutdown

# Changelog

| Date       | TOS     | Issue                                                                                   | Author      | Description                                                                                                                                                                                                 |
|------------|---------|-----------------------------------------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 10/2/2021  |         | First Version                                                                           | Marcin Gala | First Version                                                                                                                                                                                               |
| 12/8/2021  | VCS 1.4 | DHC-2630                                                                                | Marcin Gala | Updated Instruction To Set DRS To Manual Mode Instead Of Disabling It (now There Is No Need To Export Resource Pools), Included VSphere Cluster Services Shutdown/startup As They Are New Entity In VCF 4.1 |
| 6/9/2021   |         | Removed Info About Restoring Resource Pools From Snapshots As It Is No Longer Necessary | Marcin Gala | Removed Info About Restoring Resource Pools From Snapshots As It Is No Longer Necessary                                                                                                                     |
| 22/02/2022 | VCS 1.6 | DHC-4037                                                                                | Marcin Gala | Added Remark About KMS Server Reachability Check Before VCS Environment Shutdown                                                                                                                            |
| 24/05/2022 | VCS 1.5 | DHC-4680                                                                                | Marcin Gala | Added More Descriptive Info About NSX-T Managers Addresses, Modified VROps Shutdown/startup, Modified Sequence Of Workspace One Access / IDM Startup/shutdown                                               |

## Introduction

### Purpose

Shutdown and startup the entire VCS stack (Management Domain and Workload Domains).

### Audience

- VCS Operations

### Scope

The scope of this document is to provide validated procedure of shutdown and startup of entire VCS stack that
includes:

- Prerequisite actions to perform before VCS stack shutdown/startup,
- Workload domain shutdown and startup procedure,
- Management domain shutdown and startup procedure .

Elements excluded from scope:

- Shutdown / startup sequence of physical network devices (TOR switches, physical firewall) as they are not a part of VCS BOM.

## Used abbreviations

| Abbreviation | Description                                 |
|--------------|---------------------------------------------|
| VI           | Virtual Infrastructure                  |
| VCS          | VMware Cloud Services                       |
| vROPs        | vRealize Operations Manager |
| vRLCM        | vRealize Suite Lifecycle Manager |
| ADC          | Active Directory Controller |
| TSS          | Terminal Services Server |

## Target audience

- RS&D DPC Architects
- RS&D DPC Build Engineers
- RS&D DPC Deployment Managers
- Cloud Tower Service Managers

# VCS Shutdown

You must shut down the system components in a strict order to avoid data loss and faults in the components

## Prerequisites

- Verify that you have direct console access to the ESXi hosts using SSH or Web GUI and their IPMI consoles (like iDRAC, BMC).
- Verify that no VMs are running on snapshots.
- Verify that you have saved the account passwords to a location external from the Cloud Foundation system you are shutting down.
  - HashiVault local user credentials,
  - IPMI access credentials for ESXi hosts,
  - root credentials for ESXi hosts,
  - `administrator@vsphere.local` credentials,
  - domain admin credentials and local administrator credentials to AD controllers.
  - admin credentials to NSX-T Managers (nsx001 and nsx002)
- Verify that **saved account passwords are valid before performing VCS stack shut down**.
- Verify that valid backups of all management VMs, tenant VMs, NSX-T management and NSX-T for VI workload domains are available and saved to a location external from the VCS environment you are shutting down.
- If a data protection solution (f.e. SRM) is running on any of the domains, verify that it is properly shut down according to the vendor instructions.
- Verify that the environment is healthy (no major alarms in vROps and in the management and workload vCenter Servers). If the vSAN encryption is enabled in the workload domain,  check on the workload vCenter Server if alert about the KMS server reachability is present. If the environment will be shutdown with the active alert, the ESXi hosts might startup with unmounted vSAN disk groups. This will happen when the KMS server is not reachable after ESXi host startup. Before shutting down environment solve the issue with KMS servers reachability from vCenter Server and ESXi hosts.

## Shut Down the VI Workload Domain

### Shutdown Order of the VI Workload Domain

| Shutdown Order | VCS Component |
|----------------|----------------|
| 1              | Workload VMs |
| 2              | NSX-T Edge Nodes |
| 3              | NSX-T Local Managers |
| 4              | vCenter Server |
| 5              | vSphere Cluster Services |
| 6              | ESXi hosts |

### Shutdown procedure of the VI Workload Domain

1. Shut down workload domains VMs.
  
    1.1 On the SDDC Manager Dashboard, navigate to the workload domain.

    1.2 Click the launch link (Launch link icon that indicates a link in the SDDC Manager UI that launches another web UI) for the vCenter Server instance that is displayed in the Service tabs in the domain details window for that workload domain.

    1.3 Locate the VMs for that workload domain.  

      - Shut down these VMs.  

      >Note: Each workload domain includes NSX-T Edge nodes. You will shutdown NSX-T Edge nodes in the next step.

      - Perform the above steps for each VI workload domain

2. Shut down the NSX-T Edge Nodes in the Virtual Infrastructure workload domain (using Power > Shut down Guest OS option from vCenter Server on the VM).

3. Shut down the NSX-T Local Managers for each Virtual Infrastructure workload domain (using Power > Shut down Guest OS option from vCenter Server on the VM).

4. In the cluster monitoring tab in the vCenter Server verify that there are no resync operations on the vSAN storage for the VI workload domain cluster.

5. Shut down vCenter Server for each VI workload domain (using Power > Shut down Guest OS option from vCenter Server on the VM).

6. Shut Down the vSphere Cluster Services Virtual Machines in the Virtual Infrastructure Workload Domain.

    In VCF, to ensure availability of services such as vSphere DRS and vSphere HA to the workloads running on the clusters independent of the vCenter Server instance availability, there is a locally managed deployment of vSphere Cluster Services.

    6.1 In a Web browser, log in to the virtual infrastructure workload domain ESXi host that runs the first vSphere Cluster Services virtual machine by using the VMware Host Client.  

    6.2 Right-click the first vSphere Cluster Services virtual machine and select Guest OS > Shut down.

    6.3 In the confirmation dialog box, click Yes.  

    6.4 Repeat the procedure to shut down the remaining vSphere Cluster Services virtual machines on the virtual infrastructure workload domain ESXi hosts that run them.

7. Shut down the ESXi hosts for each workload domain.

   You must use ESXCLI command, which supports setting the vSAN mode when entering maintenance mode.

    7.1 For each ESXi host, connect and log in to the ESXi console using SSH.  

    7.2 Place each host into maintenance mode using the following command, with the noAction option included.

    ```shell
    esxcli system maintenanceMode set -e true -m noAction
    ```

    7.3 After a few minutes, confirm each host is in maintenance mode by repeating the command.

    ```shell
    esxcli system maintenanceMode set -e true -m noAction
    ```

    It should return the following:

    ```shell
    Maintenance mode is already enabled.
    ```

    7.4 Shut down all the ESXi hosts in each VI workload domain.

    ```shell
    poweroff
    ```

    7.5 Repeat the above steps for each VI workload domain.

## Shut Down the Management Domain

### Shutdown Order of the Management Domain

| Shutdown Order | VCS Component |
| ---------------- | ---------------- |
| 1              | vROPs cluster |
| 2              | Network Insight |
| 3              | Log Insight |
| 4              | Workspace ONE Access |
| 5              | vRLCM |
| 6              | Non-VCF management VMs except TSS1 and ADCs |
| 7              | NSX-T Edge Nodes |
| 8              | NSX-T Local Manager |
| 9              | SDDC Manager |
| 10             | vCenter Server |
| 11             | TSS 1, ADCs |
| 12             | vSphere Cluster Services |
| 13             | ESXi hosts |

### Shutdown procedure of the Management Domain

1. Shut down vROPs in the Management Domain.

    1.1 In a Web browser, log in to the vRealize Operations Manager by using the administration interface.  

    ```text
    https://<locationCode>ops002/admin
    ```

    1.2 On the System status page, click Take cluster offline.  

    1.3 In the Take cluster offline dialog box, provide the reason for the shutdown and click Ok.

    1.4 Wait for all nodes to have gone offline and for the cluster status to reflect 'Offline' in the Admin UI.

    1.5 Log in to the management domain vCenter Server and shut down the vROPs cluster nodes:

      - power down Master Replica node (using Power > Shut down Guest OS option from vCenter Server on the VM),

      - power down Master node (using Power > Shut down Guest OS option from vCenter Server on the VM).

2. Shutdown Network Insight appliance VMs (Platform appliance and Proxy VM).

    Log in to the management domain vCenter Server and shut down Network Insight Platform appliance and Network Insight Proxy VM - vni001, vnc001 - (using Power > Shut down Guest OS option from vCenter Server on the VM).

3. Shutdown Log Insight cluster.

    Log in to the management domain vCenter Server and shut down Log Insight Cluster Member VMs - vli001a, vli001b, vli001c - (using Power > Shut down Guest OS option from vCenter Server on the VM).

4. Shutdown Workspace ONE Access appliance (formerly VMware Identity Manager).

    4.1 In a Web browser, log in to vRealize Suite Lifecycle Manager by using the administration interface.

    4.2 Power off the cross-region Workspace ONE Access cluster (applicable also for single node deployment).

      - On the My services page, click Lifecycle operations.

      - In the navigation pane, click Environments.

      - On the Environments page, in the global environment card, click View details.

      - In the VMware Identity Manager section, click the ellipsis icon, and, from the drop-down menu, select Power off.

      - In the Power off VMware Identity Manager dialog box, click Submit.

      - On the Requests page, ensure that the request completes successfully.

5. Shutdown vRLCM appliance VM.

    Log in to the management domain vCenter Server and shut down the vRLCM appliance VM - lcm001 - (using Power > Shut down Guest OS option from vCenter Server on the VM).

6. Shutdown Non-VCF management VMs except ADCs and Terminal Server 1 - adc001, adc002, tss001 - (using Power > Shut down Guest OS option from vCenter Server on the VM).

    Detailed VM list to shutdown:

    | No. | VM name |
    | ----- |--------- |
    | 1 | abx001 |
    | 2 | ans001 |
    | 3 | bil001 |
    | 4 | cas001 |
    | 5 | cas002 |
    | 6 | deb001 |
    | 7 | hgw001 |
    | 8 | hsv001 |
    | 9 | ica001 |
    | 10 | inf001 |
    | 11 | inf003 |
    | 12 | kms001 |
    | 13 | kms002 |
    | 14 | mid001 |
    | 15 | nes001 |
    | 16 | pxy002 |
    | 17 | pxy003 |
    | 18 | srs001 |
    | 19 | tss002 |
    | 20 | wus001 |

    >Note: Don't power off ADCs and Terminal Server 1 VMs - adc001, adc002, tss001 as they will be powered off later.

7. Shut down the NSX-T Edge Nodes in the management domain (using Power > Shut down Guest OS option from vCenter Server on the VM).

8. Shut down the NSX-T Local Manager for management domain (using Power > Shut down Guest OS option from vCenter Server on the VM).

9. Shut down SDDC Manager appliance VM - sdm001 - (using Power > Shut down Guest OS option from vCenter Server on the VM).

10. Shut down management domain vCenter Server.

    10.1 Set DRS automation level of the management cluster to manual to prevent migration of vCSA from the first ESXi host to a different host once you power it on again.

    10.2 Migrate management domain vCenter Server, ADCs, TSS 1 VMs to the first host in the cluster.

    10.3 In the management cluster monitoring tab in the management domain vCenter Server verify that there are no resync operations on the vSAN storage.

    10.4 Shutdown management domain vCenter Server - vcs001 - (using Power > Shut down Guest OS option from vCenter Server on the VM)

11. Log in directly to the first ESXi host in the management domain cluster and shutdown TSS 1 and ADCs VMs - tss001, adc001, adc002 - (using Power > Shut down Guest OS option from vCenter Server on the VM).

12. Shut Down the vSphere Cluster Services Virtual Machines in the Virtual Infrastructure Workload Domain.

    In VCF, to ensure availability of services such as vSphere DRS and vSphere HA to the workloads running on the clusters independent of the vCenter Server instance availability, there is a locally managed deployment of vSphere Cluster Services.

    12.1 In a Web browser, log in to the virtual infrastructure workload domain ESXi host that runs the first vSphere Cluster Services virtual machine by using the VMware Host Client.

    12.2 Right-click the first vSphere Cluster Services virtual machine and select Guest OS > Shut down.

    12.3 In the confirmation dialog box, click Yes.

    12.4 Repeat the procedure to shut down the remaining vSphere Cluster Services virtual machines on the virtual infrastructure management domain ESXi hosts that run them.

13. Shut down the ESXi hosts for management domain.

    You must use ESXCLI command, which supports setting the vSAN mode when entering maintenance mode.

    13.1 For each ESXi host, connect and log in to the ESXi console using SSH.

    13.2 Place each host into maintenance mode using the following command, with the noAction option included.

    ```shell
    esxcli system maintenanceMode set -e true -m noAction
    ```

    13.3 After a few minutes, confirm each host is in maintenance mode by repeating the command.

    ```shell
    esxcli system maintenanceMode set -e true -m noAction
    ```

    It should return the following:

    ```shell
    Maintenance mode is already enabled.
    ```

    13.4 Shut down all the ESXi hosts in each VI workload domain.

    ```shell
    poweroff
    ```

# VCS Startup

You must power on the system components in a strict order to avoid data loss and faults in the components.

## Prerequisites

- Verify that physical networking infrastructure is up and running (TOR switches, physical firewall etc.).
- Verify that external NTP time source is available.

## Power on the Management Domain

### Startup Order of the Management Domain

| Startup Order | VCS Component |
|----------------|----------------|
| 1              | ESXi hosts |
| 2              | vSphere Cluster Services |
| 2              | ADCs, TSS 1 |
| 3              | vCenter Server |
| 4              | SDDC Manager |
| 5              | NSX-T Local Managers |
| 6              | NSX-T Edge Nodes |
| 7              | vRLCM |
| 8              | Workspace ONE Access |
| 9              | Non-VCF management VMs |
| 10             | Log Insight |
| 11             | Network Insight |
| 12             | vROPs cluster |

### Startup procedure of the Management Domain

1. Power on each ESXi host in the management domain and all the ESXi hosts in VI workload domains and exit maintenance mode:

    1.1 Power on ESXi host using IPMI interface (iDRAC, BMC etc.)

    1.2 Use SSH to connect and log in to the ESXi console.

    1.3 Use the following CLI command to exit maintenance mode.

    ```shell
    esxcli system maintenanceMode set -e false
    ```

    1.4 Repeat the above steps for each ESXi host.

2. Start the vSphere Cluster Services Virtual Machines in the Virtual Infrastructure Management Domain.

    In VCF, to ensure availability of services such as vSphere DRS and vSphere HA to the workloads running on the clusters independent of the vCenter Server instance availability, there is a locally managed deployment of vSphere Cluster Services.

    2.1 In a Web browser, log in to the virtual infrastructure workload domain ESXi host that runs the first vSphere Cluster Services virtual machine by using the VMware Host Client.

    2.2 Right-click the first vSphere Cluster Services virtual machine and select Guest OS > Power on.

    2.3 Repeat the procedure to start the remaining vSphere Cluster Services virtual machines on the virtual infrastructure management domain ESXi hosts that run them.

3. Log in to the first ESXi host in the management domain and power on ADCs and TSS 1 VMs.

    3.1 Power on ADCs VMs and wait for all their services started before powering on the next VM.

    3.2 Power on the TSS1 VM.

    After TSS1 VM is powered on you can perform all the subsequent operations from the TSS inside the environment.

    >**Important**: **You must wait until each VM is powered on and all it services started before powering on the next VM.**

4. Power on management domain vCenter Server.

    4.1 Log in to the first ESXi host in the management domain and power on management domain vCenter Server.

    4.2 Wait for the virtual machine to start and for vSphere services to become available.

5. Set DRS Automation Level of the Management Domain to Automatic

    After powering on the vCenter Server for the management domain, please set DRS automation level to Fully automated for the management domain cluster to provide management virtual machine placement instructions based on anti-affinity rules.

6. Power on SDDC Manager VM using management domain vCenter Server.

    Wait for the virtual machine to power on.

7. Power on NSX-T Local Manager VMs.

    7.1 Power on NSX-T Local Manager VMs - ctl001, ctl002, ctl003 - using management domain vCenter Server.

    7.2 Log in to NSX-T Manager for the management domain by using the user interface. Because Workspace One Access / IDM is not yet running, it is required to use local NSX-T admin account and the following link to force local logon:

     ```text
     https://<locationCode>nsx001/login.jsp?local=true
     ```

    7.3 Verify the system status of NSX-T Managers - on the Appliances page NSX-T Managers cluster should show status Stable and all NSX-T Manager nodes should be available.

8. Power on NSX-T Edge Nodes in the management domain using management domain vCenter Server.

   Wait for the virtual machines to power on.

9. Power on vRealize Lifecycle Manager VM - lcm001 - using management domain vCenter Server.

10. Power on Workspace ONE Access appliance VM (formerly VMware Identity Manager.

    10.1 In a Web browser, log in to vRealize Suite Lifecycle Manager by using the administration interface.

    10.2 Power on the cross-region Workspace ONE Access cluster (applicable also for single node deployment)

    10.3 On the My services page, click Lifecycle operations.

    10.4 In the navigation pane, click Environments.

    10.5 On the Environments page, in the global environment card, click View details.

    10.6 In the VMware Identity Manager section, click the ellipsis icon, and, from the drop-down menu, select Power on.

    10.7 In the Power on VMware Identity Manager dialog box, click Submit.

    10.8 On the Requests page, ensure that the request completes successfully.

11. Power on non-VCF management VMs using management domain vCenter Server.

    > **Important: Keep rca001 VM powered off.**
    >
    > **Note: ABX Proxy VM needs about 30 minutes to start all the necessary services - please be patient.**

    Detailed VM list to power on:

    | No. | VM name |
    |---- |---------|
    | 1  | abx001 |
    | 2  | ans001 |
    | 3  | bil001 |
    | 4  | cas001 |
    | 5  | cas002 |
    | 6  | deb001 |
    | 7  | hgw001 |
    | 8  | hsv001 |
    | 9  | ica001 |
    | 10 | inf001 |
    | 11 | inf003 |
    | 12 | kms001 |
    | 13 | kms002 |
    | 14 | mid001 |
    | 15 | nes001 |
    | 16 | pxy002 |
    | 17 | pxy003 |
    | 18 | srs001 |
    | 19 | tss002 |
    | 20 | wus001 |

12. Power on vRealize Log Insight VMs - vli001a, vli001b, vli001c - using management domain vCenter Server.

13. Power on vRealize Network Insight VMs - vni001, vnc001 - using management domain vCenter Server.

14. Start vRealize Operations Manager in the management domain.

    14.1 Power on vROPs VMs - ops002 and ops003 - using management domain vCenter Server

      - power on Master node (using Power > Shut down Guest OS option from vCenter Server on the VM).

      - power on Master Replica node (using Power > Shut down Guest OS option from vCenter Server on the VM)

    14.2 In a Web browser, log in to the vRealize Operations Manager by using the administration interface.

     ```text
     https://<locationCode>ops002/admin
     ```

    14.3 On the System status page, click Bring cluster online.

    14.4 Wait for the vRealize Operations Manager cluster to go online.

## Power on the VI Workload Domain

### Startup Order of the VI Workload Domain VMs

| Startup Order | VCS Component |
|----------------|----------------|
| 1              | vCenter Server |
| 2              | NSX-T Local Managers |
| 3              | NSX-T Edge Nodes |
| 4              | Workload VMs |

### Startup procedure of the VI Workload Domain

1. Start the vSphere Cluster Services Virtual Machines in the Virtual Infrastructure Workload Domains.

    In VCF, to ensure availability of services such as vSphere DRS and vSphere HA to the workloads running on the clusters independent of the vCenter Server instance availability, there is a locally managed deployment of vSphere Cluster Services.

    1.1 In a Web browser, log in to the virtual infrastructure workload domain ESXi host that runs the first vSphere Cluster Services virtual machine by using the VMware Host Client.

    1.2 Right-click the first vSphere Cluster Services virtual machine and select Guest OS > Power on.

    1.3 Repeat the procedure to start the remaining vSphere Cluster Services virtual machines on the virtual infrastructure workload domain ESXi hosts that run them.

2. Power on vCenter Server for the VI workload domain - using management domain vCenter Server.

3. Power on NSX-T Local Manager VMs of the VI workload domain.

    3.1 Power on NSX-T Local Manager VMs of the VI workload domain - using management domain vCenter Server.

    3.2 Log in to NSX-T Manager for the VI workload domain by using the user interface.  Because Workspace One Access / IDM is not yet running, it is required to use local NSX-T admin account and the following link to force local logon:

    ```test
    https://<locationCode>nsx002/login.jsp?local=true
    ```

    3.3 Verify the system status of NSX-T Managers - on the Appliances page NSX-T Managers cluster should show status Stable and all NSX-T Manager nodes should be available.

4. Power on NSX-T Edge Nodes in the VI Workload Domain using management domain vCenter Server.

    Wait for the virtual machines to power on.

5. Power on Workload VMs in the VI workload domain.

    5.1 On the SDDC Manager Dashboard, navigate to the workload domain.

    5.2 Click the launch link (Launch link icon that indicates a link in the SDDC Manager UI that launches another web UI) for the vCenter Server instance that is displayed in the Service tabs in the domain details window for that workload domain.

    5.3 Locate the VMs for that workload domain.

    5.4 Power on these VMs.

6. Repeat the above steps for each VI workload domain.
