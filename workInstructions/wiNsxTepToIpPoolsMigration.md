# Title: NSX TEPs migration from DHCP to NSX IP Pools

# List of Changes

| Date       | Issue     | Author              | TOS | Description                                                           |
| ---------- | --------- | ------------------- | --- | --------------------------------------------------------------------- |
| 02/10/2025 | VCS-17041 | Mariusz Stanek  |     | Initial version                                                       |

# Related Documents

| Document                                                                                    |
| ------------------------------------------------------------------------------------------- |
|                                          |

# Features Summary

# NSX-T TEPs readressing

Part of VCS-2.0.1 is TEPs readressing from DHCP to IP Pool. It can be done manually or by ansible playbook. This change can cause short temporary downtime (few seconds only) when tunnels are re-established. Downtime can be observed in edges `Tunnels` tab.

> IMPORTANT NOTE: Please make sure that you have recent NSX backup before readressing TEPs.

## Readressing example for better understanding

> IMPORTANT NOTE: number of TEPs addressed assigned in below Lab example can differ from Production deployment. It is related with number of Uplinks/Interfaces assigned to each Edge and Host. Important is to have exactly the same number of TEPs before and after change but with different IPs assigned.

In NX2 Lab environment before TEPs readdressing we have following structure:

Edges:

- NSX001: gre22edge101 with static IPs 172.22.37.2 and 172.22.37.3
- NSX001: gre22edge102 with static IPs 172.22.37.4 and 172.22.37.5
- NSX002: gre22ecn01edg01 with IP 172.22.37.10 assigned from NSX002 IP Pool range gre22Ipp001 (172.22.37.10 - 172.22.37.140)
- NSX002: gre22ecn01edg02 with IP 172.22.37.11 assigned from NSX002 IP Pool range gre22Ipp001 (172.22.37.10 - 172.22.37.140)

Hosts:

- NSX001: gre22mgt005 with IPs 172.22.33.25 and 172.22.33.26 assigned by DHCP from range 172.22.33.20 and 172.22.33.120
- NSX001: gre22mgt006 with IPs 172.22.33.23 and 172.22.33.27 assigned by DHCP from range 172.22.33.20 and 172.22.33.120
- NSX001: gre22mgt007 with IPs 172.22.33.21 and 172.22.33.28 assigned by DHCP from range 172.22.33.20 and 172.22.33.120
- NSX001: gre22mgt008 with IPs 172.22.33.22 and 172.22.33.24 assigned by DHCP from range 172.22.33.20 and 172.22.33.120
- NSX002: gre22cmp002 with IPs 172.22.33.20 assigned by DHCP from range 172.22.33.20 and 172.22.33.120

During TEPs readdressing we create following ranges:

- gre22-ippool-mgt-hosts-tep: for hosts TEPs in Management domain (range 172.22.33.4 - 172.22.33.31)
- gre22-ippool-cmp-hosts-tep: for hosts TEPs in Compute domain (range 172.22.33.32 - 172.22.33.254)
- gre22-ippool-mgt-edges-tep: for edges TEPs in Management domain (range 172.22.37.4 - 172.22.37.15)
- gre22-ippool-cmp-edges-tep: for edges TEPs in Compute domain (range 172.22.37.16 - 172.22.37.254)

Finally In NX2 Lab environment after TEPs readdressing we have following structure:

Edges:

- NSX001: gre22edge101 with IPs 172.22.37.4 and 172.22.37.5 assigned from NSX001 IP Pool gre22-ippool-mgt-edges-tep (range 172.22.37.4 - 172.22.37.15)
- NSX001: gre22edge102 with IPs 172.22.37.6 and 172.22.37.7 assigned from NSX001 IP Pool gre22-ippool-mgt-edges-tep (range 172.22.37.4 - 172.22.37.15)
- NSX002: gre22ecn01edg01 with IP 172.22.37.16 assigned from NSX002 IP Pool gre22-ippool-cmp-edges-tep (range 172.22.37.16 - 172.22.37.254)
- NSX002: gre22ecn01edg02 with IP 172.22.37.17 assigned from NSX002 IP Pool gre22-ippool-cmp-edges-tep (range 172.22.37.16 - 172.22.37.254)

Hosts:

- NSX001: gre22mgt005 with IPs 172.22.33.10 and 172.22.33.11 assigned from NSX001 IP Pool gre22-ippool-mgt-hosts-tep (range 172.22.33.4 - 172.22.33.31)
- NSX001: gre22mgt006 with IPs 172.22.33.6 and 172.22.33.7 assigned from NSX001 IP Pool gre22-ippool-mgt-hosts-tep (range 172.22.33.4 - 172.22.33.31)
- NSX001: gre22mgt007 with IPs 172.22.33.8 and 172.22.33.9 assigned from NSX001 IP Pool gre22-ippool-mgt-hosts-tep (range 172.22.33.4 - 172.22.33.31)
- NSX001: gre22mgt008 with IPs 172.22.33.5 and 172.22.33.6 assigned from NSX001 IP Pool gre22-ippool-mgt-hosts-tep (range 172.22.33.4 - 172.22.33.31)
- NSX002: gre22cmp002 with IP 172.22.33.35 assigned from NSX002 IP Pool gre22-ippool-cmp-hosts-tep (range 172.22.33.32 - 172.22.33.254)

## NSX-T TEPs readressing - playbook

Important notes:

1. As a prerequisite Transport Node Profile must be assigned to the Cluster as below for both NSX Managers in `System` > `Fabric` > `Hosts` > `Transport Node Profile` and `System` > `Fabric` > `Hosts` > `Clusters`:

   ![NSX002 Profile](images/wiNsxTepToIpPoolsMigration/nsx002-profile-1.png)

   ![NSX002 Profile](images/wiNsxTepToIpPoolsMigration/nsx002-profile-2.png)

   Below 2 pictures show situation when Transport Node Profile is not assigned to the cluster:

   ![NSX002 Profile](images/wiNsxTepToIpPoolsMigration/nsx002-profile-3.png)

   ![NSX002 Profile](images/wiNsxTepToIpPoolsMigration/nsx002-profile-4.png)

   Important note: If Transport Node Profile is not assigned to the Cluster then changing TEPs from DHCP to IP Pool is not possible.

   If Transport Node Profile is not assigned then it can be fixed as follows:

   - Navigate to `System` > `Fabric` > `Hosts` > `Clusters`:

    ![Profile Fix 1](images/wiNsxTepToIpPoolsMigration/profile-fix-1.png)

   - Click checkbox next to the cluster which must be fixed:

   ![Profile Fix 2](images/wiNsxTepToIpPoolsMigration/profile-fix-2.png)

   - Choose `CONFIGURE NSX`:

   ![Profile Fix 3](images/wiNsxTepToIpPoolsMigration/profile-fix-3.png)

   - Choose `Transport Node Profile` from dropdown menu:

   ![Profile Fix 4](images/wiNsxTepToIpPoolsMigration/profile-fix-4.png)

   - Transport Node Profile will be applied to each Host in particular Cluster:

   ![Profile Fix 5](images/wiNsxTepToIpPoolsMigration/profile-fix-5.png)

   - Finally Transport Node Profile will be visible in `Clusters` and in `Transport Node Profile` sections:

   ![Profile Fix 6](images/wiNsxTepToIpPoolsMigration/profile-fix-6.png)

   ![Profile Fix 7](images/wiNsxTepToIpPoolsMigration/nsx002-profile-2.png)

2. IP Pools naming convention used for VCS-2.0 new deploy is as follows:

   - {{ locationCode }}-ippool-mgt-hosts-tep: for hosts TEPs in Management domain (range a.b.c.4 - a.b.c.31),
   - {{ locationCode }}-ippool-cmp-hosts-tep: for hosts TEPs in Compute domain (range a.b.c.32 - a.b.c.254),
   - {{ locationCode }}-ippool-mgt-edges-tep: for edges TEPs in Management domain (range d.e.f.4 - d.e.f.15),
   - {{ locationCode }}-ippool-cmp-edges-tep: for edges TEPs in Compute domain (range d.e.f.16 - d.e.f.254).

3. Read README.md for role `dhc-migrateNsxTepToIpPool`.

4. Read next section about Manual change to know how to verify what was changed.

5. Execute a playbook which will add IP Pools to platformConfig.yml, configure IP Pools in NSX managers and migrate TEPs to IP Pools. Playbook can run partially with --tags (see README.md). Prefered way to run playbook is in below order:

- Playbook run for platformConfig.yml update with new IP Pools definitions only (verify `/opt/dhcConfig/group_vars/all/platformConfig.yml`):

```bash
ansible-playbook migrateNsxTepToIpPool.yml --tags "update"
```

IP pools presence can be verified in `/opt/dhcConfig/group_vars/all/platformConfig.yml`, search for section looking as below taken from lab environment. IP addresses will be different but structure is the same:

```bash
# IP Pools added by Ansible playbook
# List of NSX IP Pools

ipPools:
    tep:
      hosts:
        mgt:
          displayName: "gre22-ippool-mgt-hosts-tep"
          description: "ESXi MGT Host Overlay TEP IP Pool"
          start: "172.22.33.4"
          end: "172.22.33.31"
          cidr: "172.22.33.0/24"
          gw: "172.22.33.1"
        cmp:
          displayName: "gre22-ippool-cmp-hosts-tep"
          description: "ESXi CMP Host Overlay TEP IP Pool"
          start: "172.22.33.32"
          end: "172.22.33.254"
          cidr: "172.22.33.0/24"
          gw: "172.22.33.1"
      edges:
        mgt:
          displayName: "gre22-ippool-mgt-edges-tep"
          description: "NSX MGT Edge Overlay TEP IP Pool"
          start: "172.22.37.4"
          end: "172.22.37.15"
          cidr: "172.22.37.0/24"
          gw: "172.22.37.1"
        cmp:
          displayName: "gre22-ippool-cmp-edges-tep"
          description: "NSX CMP Overlay TEP IP Pool"
          start: "172.22.37.16"
          end: "172.22.37.254"
          cidr: "172.22.37.0/24"
          gw: "172.22.37.1"

# IP Pools added by Ansible playbook
```

- Playbook run for IP Pools creation in NSX001 and NSX002 (verify in `Networking` > `IP Address Pools`):

```bash
ansible-playbook migrateNsxTepToIpPool.yml --tags "collect,create"
```

- Playbook run for all TEPs migration to IP Pool:

```bash
ansible-playbook migrateNsxTepToIpPool.yml --tags "collect,migrate"
```

## NSX-T TEPs readressing - manual

1. Login to nsx001 and nsx002 because below actions must be done for both Managers.
2. As a prerequisite Transport Node Profile must be assigned to the Cluster as below for both NSX Managers in `System` > `Fabric` > `Hosts` > `Transport Node Profile` and `System` > `Fabric` > `Hosts` > `Clusters`:

   ![NSX002 Profile](images/wiNsxTepToIpPoolsMigration/nsx002-profile-1.png)

   ![NSX002 Profile](images/wiNsxTepToIpPoolsMigration/nsx002-profile-2.png)

   Below 2 pictures show situation when Transport Node Profile is not assigned to the cluster:

   ![NSX002 Profile](images/wiNsxTepToIpPoolsMigration/nsx002-profile-3.png)

   ![NSX002 Profile](images/wiNsxTepToIpPoolsMigration/nsx002-profile-4.png)

   Important note: If Transport Node Profile is not assigned to the Cluster then changing TEPs from DHCP to IP Pool is not possible.

   If Transport Node Profile is not assigned then it can be fixed as follows:

   - Navigate to `System` > `Fabric` > `Hosts` > `Clusters`:

    ![Profile Fix 1](images/wiNsxTepToIpPoolsMigration/profile-fix-1.png)

   - Click checkbox next to the cluster which must be fixed:

   ![Profile Fix 2](images/wiNsxTepToIpPoolsMigration/profile-fix-2.png)

   - Choose `CONFIGURE NSX`:

   ![Profile Fix 3](images/wiNsxTepToIpPoolsMigration/profile-fix-3.png)

   - Choose `Transport Node Profile` from dropdown menu:

   ![Profile Fix 4](images/wiNsxTepToIpPoolsMigration/profile-fix-4.png)

   - Transport Node Profile will be applied to each Host in particular Cluster:

   ![Profile Fix 5](images/wiNsxTepToIpPoolsMigration/profile-fix-5.png)

   - Finally Transport Node Profile will be visible in `Clusters` and in `Transport Node Profile` sections:

   ![Profile Fix 6](images/wiNsxTepToIpPoolsMigration/profile-fix-6.png)

   ![Profile Fix 7](images/wiNsxTepToIpPoolsMigration/nsx002-profile-2.png)

3. Verify if there are IP Pools created in `Networking` > `IP Address Pools`:

   ![NSX001 IP Pools](images/wiNsxTepToIpPoolsMigration/nsx001-1.png)

   ![NSX002 IP Pools](images/wiNsxTepToIpPoolsMigration/nsx002-1.png)

   IP Pools naming convention used for VCS-2.0 new deploy is as follows:

   - {{ locationCode }}-ippool-mgt-hosts-tep: for hosts TEPs in Management domain (range a.b.c.4 - a.b.c.31),
   - {{ locationCode }}-ippool-cmp-hosts-tep: for hosts TEPs in Compute domain (range a.b.c.32 - a.b.c.254),
   - {{ locationCode }}-ippool-mgt-edges-tep: for edges TEPs in Management domain (range d.e.f.4 - d.e.f.15),
   - {{ locationCode }}-ippool-cmp-edges-tep: for edges TEPs in Compute domain (range d.e.f.16 - d.e.f.254).

   If IP Pools are not created then do as follows:

   Execute a playbook which will add IP Pools to platformConfig.yml, configure IP Pools in NSX managers and migrate TEPs to IP Pools. Playbook can run partially with --tags (see README.md). Prefered way to run playbook is in below order:

   - Playbook run for platformConfig.yml update with new IP Pools definitions only (verify `/opt/dhcConfig/group_vars/all/platformConfig.yml`):

   ```bash
   ansible-playbook migrateNsxTepToIpPool.yml --tags "update"
   ```

   IP pools presence can be verified in `/opt/dhcConfig/group_vars/all/platformConfig.yml`, search for section looking as below taken from lab environment. IP addresses will be different but structure is the same:

   ```bash
   # IP Pools added by Ansible playbook
   # List of NSX IP Pools

   ipPools:
       tep:
         hosts:
           mgt:
             displayName: "gre22-ippool-mgt-hosts-tep"
             description: "ESXi MGT Host Overlay TEP IP Pool"
             start: "172.22.33.4"
             end: "172.22.33.31"
             cidr: "172.22.33.0/24"
             gw: "172.22.33.1"
           cmp:
             displayName: "gre22-ippool-cmp-hosts-tep"
             description: "ESXi CMP Host Overlay TEP IP Pool"
             start: "172.22.33.32"
             end: "172.22.33.254"
             cidr: "172.22.33.0/24"
             gw: "172.22.33.1"
         edges:
           mgt:
             displayName: "gre22-ippool-mgt-edges-tep"
             description: "NSX MGT Edge Overlay TEP IP Pool"
             start: "172.22.37.4"
             end: "172.22.37.15"
             cidr: "172.22.37.0/24"
             gw: "172.22.37.1"
           cmp:
             displayName: "gre22-ippool-cmp-edges-tep"
             description: "NSX CMP Overlay TEP IP Pool"
             start: "172.22.37.16"
             end: "172.22.37.254"
             cidr: "172.22.37.0/24"
             gw: "172.22.37.1"

   # IP Pools added by Ansible playbook
   ```

   - Playbook run for IP Pools creation in NSX001 and NSX002 (verify in `Networking` > `IP Address Pools`):

   ```bash
   ansible-playbook migrateNsxTepToIpPool.yml --tags "collect,create"
   ```

4. Verify statuses for TEPs `System` > `Nodes` > `Edge Transport Nodes` then click for example on `gre22edg101` and choose `Tunnels`:

   ![NSX001 TEPs](images/wiNsxTepToIpPoolsMigration/nsx001-tunnels-1.png)

   ![NSX001 TEPs](images/wiNsxTepToIpPoolsMigration/nsx001-tunnels-2.png)

   ![NSX002 TEPs](images/wiNsxTepToIpPoolsMigration/nsx002-tunnels-1.png)

   ![NSX002 TEPs](images/wiNsxTepToIpPoolsMigration/nsx002-tunnels-2.png)

5. Verify current Host Transport Node Profile configuration `System` > `Fabric` > `Hosts` > `Transport Node Profile`:

   ![NSX001 Config](images/wiNsxTepToIpPoolsMigration/nsx001-2.png)

   ![NSX001 Config](images/wiNsxTepToIpPoolsMigration/nsx001-3.png)

6. Click `Edit` and again `Edit` to change Transport Node Profile TEPs addressing method (below example for NSX002):

   ![NSX002 Config](images/wiNsxTepToIpPoolsMigration/nsx002-edit-1.png)

7. Change `IPv4 Assignment` to `Use IP Pool` and choose proper `IPv4 Pool`:

   ![NSX002 Config](images/wiNsxTepToIpPoolsMigration/nsx002-edit-2.png)

8. Click `ADD` and `APPLY`:

   ![NSX002 Config](images/wiNsxTepToIpPoolsMigration/nsx002-edit-3.png)

9. Click `SAVE`:

   ![NSX002 Config](images/wiNsxTepToIpPoolsMigration/nsx002-edit-4.png)

10. It is possible to verify way of TEP addressing for particular host node from `System` > `Fabric` > `Hosts` > `Clusters`:

    ![NSX002 Config](images/wiNsxTepToIpPoolsMigration/nsx002-edit-5.png)

    ![NSX002 Config](images/wiNsxTepToIpPoolsMigration/nsx002-edit-6.png)

    ![NSX002 Config](images/wiNsxTepToIpPoolsMigration/nsx002-edit-7.png)

11. In next steps TEPs addressing must be also set to IP Pool for each Edge node (below example for gre22ecn01edg01 from NSX002). Navigate to `System` > `Fabric` > `Nodes` > `Edge Transport Nodes`:

    ![NSX002 Config](images/wiNsxTepToIpPoolsMigration/nsx002-edit-8.png)

12. Change `IPv4 Assignment` to `Use IP Pool` and choose proper `IPv4 Pool`:

    ![NSX002 Config](images/wiNsxTepToIpPoolsMigration/nsx002-edit-9.png)

13. In Tunnels we can see if TEPs are fine after change:

    ![NSX002 Config](images/wiNsxTepToIpPoolsMigration/nsx002-edit-10.png)

## NSX-T TEPs readressing - before and after

1. NSX001 gre22edg101 TEPs addresses before:

   ![NSX001 edge 1 before](images/wiNsxTepToIpPoolsMigration/nsx001-tunnels-1.png)

2. NSX001 gre22edg101 TEPs addresses after:

   ![NSX001 edge 1 after](images/wiNsxTepToIpPoolsMigration/nsx001-tunnels-3.png)

3. NSX001 gre22edg102 TEPs addresses before:

   ![NSX001 edge 2 before](images/wiNsxTepToIpPoolsMigration/nsx001-tunnels-2.png)

4. NSX001 gre22edg102 TEPs addresses after:

   ![NSX001 edge 2 after](images/wiNsxTepToIpPoolsMigration/nsx001-tunnels-4.png)

5. NSX002 gre22ecn01edg01 TEPs addresses before:

   ![NSX002 edge 1 before](images/wiNsxTepToIpPoolsMigration/nsx002-tunnels-1.png)

6. NSX002 gre22ecn01edg01 TEPs addresses after:

   ![NSX002 edge 1 after](images/wiNsxTepToIpPoolsMigration/nsx002-tunnels-3.png)

7. NSX002 gre22ecn01edg02 TEPs addresses before:

   ![NSX002 edge 2 before](images/wiNsxTepToIpPoolsMigration/nsx002-tunnels-2.png)

8. NSX002 gre22ecn01edg02 TEPs addresses after:

   ![NSX002 edge 2 after](images/wiNsxTepToIpPoolsMigration/nsx002-tunnels-4.png)

## NSX-T TEPs readressing - DHCP service disabling - playbook

DHCP service on AD can be disabled if it was used for TEPs addressing only.

Below playbook does as follows:

- Remove DHCP scopes from AD.
- Stop and Disable DHCP service on AD.
- Remove DHCP TEP interface from AD.
- Remove TEP port group `VXLAN (VTEP) - DHCP Network` from vCenter.
- Remove DHCP service monitoring from Aria Operations.

```bash
ansible-playbook removeDHCPFromAd.yml
```

## NSX-T TEPs readressing - DHCP service disabling - manual

DHCP service on AD can be disabled if it was used for TEPs addressing only.  

1. RDP to adc001 Windows server and open `Server Manager` then from `Tools` dropdown menu click on `DHCP`:

   ![DHCP remove 1](images/wiNsxTepToIpPoolsMigration/dhcp-remove-1.png)

2. In `DHCP` open Scope, we should have only one in our Lab case it is `172.22.33.0`, right click and choose `Deconfigure Failover`:

   ![DHCP remove 2](images/wiNsxTepToIpPoolsMigration/dhcp-remove-2.png)

3. Confirm and choose `OK`, then once again `OK` and `Close`:

   ![DHCP remove 3](images/wiNsxTepToIpPoolsMigration/dhcp-remove-3.png)

   ![DHCP remove 4](images/wiNsxTepToIpPoolsMigration/dhcp-remove-4.png)

   ![DHCP remove 5](images/wiNsxTepToIpPoolsMigration/dhcp-remove-5.png)

4. Right click on scope and choose `Deactivate`, then confirm choosing `Yes`:

   ![DHCP remove 6](images/wiNsxTepToIpPoolsMigration/dhcp-remove-6.png)

   ![DHCP remove 7](images/wiNsxTepToIpPoolsMigration/dhcp-remove-7.png)

5. Right click on scope and choose `Delete`, then confirm choosing `Yes` once again `Yes`:

   ![DHCP remove 8](images/wiNsxTepToIpPoolsMigration/dhcp-remove-8.png)

   ![DHCP remove 9](images/wiNsxTepToIpPoolsMigration/dhcp-remove-9.png)

   ![DHCP remove 10](images/wiNsxTepToIpPoolsMigration/dhcp-remove-10.png)

6. Finally you can see:

   ![DHCP remove 11](images/wiNsxTepToIpPoolsMigration/dhcp-remove-11.png)

7. Open `Services` manager and Stop `DHCP Server` service:

   ![DHCP remove 12](images/wiNsxTepToIpPoolsMigration/dhcp-remove-12.png)

   ![DHCP remove 13](images/wiNsxTepToIpPoolsMigration/dhcp-remove-13.png)

8. Right click `DHCP Server` then `Properties` and change `Startup type` to `Disabled` and `OK`:

   ![DHCP remove 14](images/wiNsxTepToIpPoolsMigration/dhcp-remove-14.png)

   ![DHCP remove 15](images/wiNsxTepToIpPoolsMigration/dhcp-remove-15.png)

9. `DHCP Server` is now in `Disabled` state`:

   ![DHCP remove 16](images/wiNsxTepToIpPoolsMigration/dhcp-remove-16.png)

10. RDP to adc002 Windows server and do the same as it was for adc001. If you don't see DHCP scope then it was removed automatically when done on adc001 but `DHCP Server` must be also disabled.

## NSX-T TEPs readressing - DHCP interfaces remove from ADC001 and ADC002

To finalize cleanup it is required to remove Network adapters used for `VXLAN (VTEP) - DHCP Network` from both adc001 and adc002. It must be done one by one because Domain Controllers must be rebooted at the end of process.

Please follow below procedure:

1. Login to vcs001 and find adc001 Windows server. In `Virtual Machine Details` IP address from range `172.22.33.0` (it is our Lab range, search for appropriate to your environment) is also visible as a secondary one so it must be removed. Right click on adc001 VM and choose `Edit Settings`:

   ![DHCP remove 17](images/wiNsxTepToIpPoolsMigration/dhcp-remove-17.png)

2. In our case we see `Network adapter 2` described as used for `VXLAN (VTEP) - DHCP Network`. Uncheck `Connected` and `Connect At Power On` next to appropriate network adapter and click `OK`:

   ![DHCP remove 18](images/wiNsxTepToIpPoolsMigration/dhcp-remove-18.png)

3. Right click on adc001 VM and choose `Edit Settings` once again, then three dots next to appropriate network adapter `Remove device` and `OK`:

   ![DHCP remove 19](images/wiNsxTepToIpPoolsMigration/dhcp-remove-19.png)

4. For verification purpose RDP to adc001 and check `Ethernet settings` there is only one `Ethernet0` interface visible now:

   ![DHCP remove 20](images/wiNsxTepToIpPoolsMigration/dhcp-remove-20.png)

   It can be also verified in `Server Manager` where we see only one Interface and IP address now:

   ![DHCP remove 21](images/wiNsxTepToIpPoolsMigration/dhcp-remove-21.png)

   Verification can be also done in vc001 `Virtual Machine Details` where we also see only IP address now:

   ![DHCP remove 22](images/wiNsxTepToIpPoolsMigration/dhcp-remove-22.png)

5. When adc001 is running properly please do the same for adc002.

6. Port group `VXLAN (VTEP) - DHCP Network` must be also removed, when Network interfaces are removed from both AD.

   Navigate to proper switch in vCenter:

   ![DHCP remove 23](images/wiNsxTepToIpPoolsMigration/dhcp-port-group-1.png)

   Click on `VXLAN (VTEP) - DHCP Network` and check if there are no ports assigned. If ports are assigned then they must be removed from VMs:

   ![DHCP remove 24](images/wiNsxTepToIpPoolsMigration/dhcp-port-group-2.png)

   Right Click on `VXLAN (VTEP) - DHCP Network` and check Delete port group from switch:

   ![DHCP remove 25](images/wiNsxTepToIpPoolsMigration/dhcp-port-group-3.png)

7. Remove monitoring of DHCP services from Aria Operations.

   Login to Aria Operations and navigate to `Operations` > `Applications` > `Manage Telegraf Agents`:

   ![DHCP remove 26](images/wiNsxTepToIpPoolsMigration/dhcp-telegraf-1.png)

   Choose `Services` for adc001 click Delete `DHCPServer` and confirm removal of selected instance from monitoring (do the same for both AD VMs):

   ![DHCP remove 27](images/wiNsxTepToIpPoolsMigration/dhcp-telegraf-2.png)
