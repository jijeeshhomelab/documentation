# Stretched Cluster Witness Traffic Separation Configuration

## Changelog

| Date       | Issue      | Author              | Description                                                                                      |
|------------|------------|---------------------|--------------------------------------------------------------------------------------------------|
| 16/03/2026 | VCS-290    | Radoslaw Dabrowski  | First version - Work Instruction for witness traffic separation configuration                   |

## Introduction

### Purpose

This Work Instruction provides step-by-step procedures for configuring witness traffic separation on VMware vSAN stretched clusters deployed in VCS environments. The configuration eliminates asymmetric routing and return traffic dependency by implementing dedicated VMkernel interfaces and symmetric routing paths for each availability zone.

**Business Outcome**: Achieves <5 second RTO/RPO during site failures by ensuring independent, bidirectional witness connectivity for each site.

### Audience

- VCS Operations
- VCS Engineers
- VMware Infrastructure Administrators

### Scope

This procedure applies to:

- Active-Active vSAN stretched clusters spanning two availability zones (Site A and Site B)
- VCS deployments requiring witness traffic resilience and symmetric routing
- New stretched cluster deployments and existing clusters requiring remediation

**Out of Scope**:

- Single-site clusters (not applicable)
- Passive DR configurations using vSphere Replication or SRM
- Witness host initial deployment (covered in separate WI)

## Prerequisites

### Version Requirements

Verify the following minimum software versions are deployed:

| Component           | Minimum Version     | Verification Command (ESXi)                    |
|---------------------|---------------------|------------------------------------------------|
| vSphere ESXi        | 7.0 Update 3        | `vmware -v`                                    |
| vCenter Server      | 7.0 Update 3        | Check vCenter UI: Menu > About                 |
| vSAN                | 7.0 Update 3        | `esxcli vsan version get`                      |

**Recommended**: vSphere 8.0 or later for enhanced stability

### Network Prerequisites

Before starting configuration, ensure the following network components are in place:

**Site A Network:**

- [ ] Dedicated VLAN for Site A witness traffic (e.g., VLAN 1001)
- [ ] Distributed Port Group created: `VSAN Witness communication <SiteA>` (e.g., LBG)
- [ ] IP subnet allocated for Site A VMkernel interfaces (e.g., `10.10.1.0/24`)
- [ ] Site A local gateway/router configured and operational (e.g., `10.10.1.1`)
- [ ] Routing configured from Site A gateway to witness host location
- [ ] Firewall rules permit vSAN witness traffic (port 2233) from Site A subnet to witness

**Site B Network:**

- [ ] Dedicated VLAN for Site B witness traffic (e.g., VLAN 2001)
- [ ] Distributed Port Group created: `VSAN Witness communication <SiteB>` (e.g., BBP)
- [ ] IP subnet allocated for Site B VMkernel interfaces (e.g., `10.20.1.0/24`)
- [ ] Site B local gateway/router configured and operational (e.g., `10.20.1.1`)
- [ ] Routing configured from Site B gateway to witness host location
- [ ] Firewall rules permit vSAN witness traffic (port 2233) from Site B subnet to witness

**Important**: Site A and Site B subnets **MUST NOT overlap**. Each site must have its own isolated network segment.

### Access Requirements

- [ ] vCenter Administrator privileges (required for VMkernel adapter creation)
- [ ] SSH/root access to all ESXi hosts in both Site A and Site B
- [ ] SSH service (TSM-SSH) enabled on all ESXi hosts
- [ ] Network team confirmation that routing and firewall rules are in place

### Documentation Requirements

Prepare the following information before starting:

| Parameter                          | Site A Value          | Site B Value          |
|------------------------------------|-----------------------|-----------------------|
| Distributed Port Group Name        | (e.g., VSAN_Witness_LBG) | (e.g., VSAN_Witness_BBP) |
| VMkernel VLAN ID                   | (e.g., 1001)          | (e.g., 2001)          |
| VMkernel Subnet                    | (e.g., 10.10.1.0/24)  | (e.g., 10.20.1.0/24)  |
| VMkernel Adapter ID                | (e.g., vmk10)         | (e.g., vmk10) **MUST MATCH** |
| Site A Host 1 IP                   | (e.g., 10.10.1.11)    | N/A                   |
| Site A Host 2 IP                   | (e.g., 10.10.1.12)    | N/A                   |
| Site A Host N IP                   | (e.g., 10.10.1.1N)    | N/A                   |
| Site B Host 1 IP                   | N/A                   | (e.g., 10.20.1.11)    |
| Site B Host 2 IP                   | N/A                   | (e.g., 10.20.1.12)    |
| Site B Host N IP                   | N/A                   | (e.g., 10.20.1.1N)    |
| Site A Gateway                     | (e.g., 10.10.1.1)     | N/A                   |
| Site B Gateway                     | N/A                   | (e.g., 10.20.1.1)     |
| Witness Host IP                    | (e.g., 192.168.100.50) | (e.g., 192.168.100.50) |
| Witness Network (for static route) | (e.g., 192.168.100.0/24) | (e.g., 192.168.100.0/24) |

**Critical**: VMkernel Adapter ID (e.g., `vmk10`) **MUST be identical on all hosts in both sites** for proper vSAN failover behavior.

## Procedure

### Phase 1: Create VMkernel Adapters via vCenter UI

This phase creates dedicated VMkernel adapters on all ESXi hosts in both sites.

#### Step 1.1: Create VMkernel Adapters on Site A Hosts

For **each ESXi host in Site A**:

1. Log in to the **vSphere Client** (vCenter Server)

2. Navigate to **Menu** > **Hosts and Clusters**

3. Expand the stretched cluster and select the first **Site A ESXi host**

4. Click the **Configure** tab

5. Under **Networking**, select **VMkernel adapters**

6. Click **Add Networking** button to launch the Add Networking wizard

7. **Select connection type**: Select **VMkernel Network Adapter** → Click **Next**

8. **Select target device**:

   - Select **Browse** next to "Select an existing network"
   - Choose the Site A distributed port group: `VSAN Witness communication <SiteA>` (e.g., LBG)
   - Click **OK** → Click **Next**

9. **Port properties**:

   - Leave all checkboxes unchecked (no vMotion, Management, etc.)
   - Click **Next**

10. **IPv4 settings**:

    - Select **Use static IPv4 settings**
    - **IPv4 address**: Enter the IP address for this host (e.g., `10.10.1.11` for first host)
    - **Subnet mask**: Enter subnet mask (e.g., `255.255.255.0`)
    - **Default gateway**: **Leave unchecked** (gateway will be configured via static routes)
    - **DNS server addresses**: Leave blank (optional)
    - Click **Next**

11. **Ready to complete**: Review settings → Click **Finish**

12. **Verify VMkernel adapter created**:

    - In the VMkernel adapters list, locate the newly created adapter
    - **Note the VMkernel ID** (e.g., `vmk5`, `vmk10`) - this will appear in the "Device" column
    - **IMPORTANT**: This VMkernel ID **MUST be the same on ALL hosts** (Site A and Site B)

13. **Repeat steps 3-12 for all remaining Site A hosts**, ensuring:

    - Each host gets a unique IP address from the Site A subnet
    - **All hosts use the SAME VMkernel ID** (e.g., if first host is `vmk10`, all must be `vmk10`)

**Expected Result**: All Site A hosts have a new VMkernel adapter with:

- Same VMkernel ID (e.g., `vmk10`)
- Unique IP addresses in Site A subnet (e.g., `10.10.1.11`, `10.10.1.12`, etc.)
- Connected to Site A distributed port group

#### Step 1.2: Create VMkernel Adapters on Site B Hosts

For **each ESXi host in Site B**:

1. Follow **the exact same procedure as Step 1.1** (steps 1-13), with the following Site B values:

   - **Distributed port group**: Select Site B port group (e.g., `VSAN Witness communication BBP`)
   - **IPv4 addresses**: Use Site B subnet (e.g., `10.20.1.11`, `10.20.1.12`, etc.)
   - **VMkernel ID**: **MUST match Site A** (e.g., if Site A used `vmk10`, Site B must also use `vmk10`)

**Expected Result**: All Site B hosts have a new VMkernel adapter with:

- **Same VMkernel ID as Site A** (e.g., `vmk10`)
- Unique IP addresses in Site B subnet (e.g., `10.20.1.11`, `10.20.1.12`, etc.)
- Connected to Site B distributed port group

**Checkpoint**: Before proceeding, verify:

- [ ] All Site A hosts have VMkernel adapter with ID `vmkX`
- [ ] All Site B hosts have VMkernel adapter with **same ID** `vmkX`
- [ ] IP addressing is correct per site (no overlaps, no duplicates)

---

### Phase 2: Tag VMkernel Adapters for Witness Traffic (ESXi CLI)

This phase tags the VMkernel adapters to designate them for exclusive witness traffic use.

#### Step 2.1: Enable SSH Access on ESXi Hosts (if not already enabled)

For **each ESXi host** (if SSH is not already running):

1. In vSphere Client, select the ESXi host

2. Click **Configure** tab > **System** > **Services**

3. Locate **TSM-SSH** service

4. If Status shows "Stopped", select the service and click **Start**

5. Verify Status changes to "Running"

#### Step 2.2: Tag VMkernel Adapters on Site A Hosts

For **each ESXi host in Site A**:

1. SSH to the ESXi host:

   ```bash
   ssh root@<site_a_host_ip>
   ```

2. Log in with root credentials

3. Verify the VMkernel adapter exists and note its IP address:

   ```bash
   esxcli network ip interface ipv4 get
   ```

   **Expected**: You should see the VMkernel adapter (e.g., `vmk10`) with the correct Site A IP address

4. Tag the VMkernel adapter for witness traffic:

   ```bash
   esxcli vsan network ip add -i <vmkernel_adapter_id> -T=witness
   ```

   **Example** (if VMkernel ID is `vmk10`):

   ```bash
   esxcli vsan network ip add -i vmk10 -T=witness
   ```

5. Verify the tagging was successful:

   ```bash
   esxcli vsan network list
   ```

   **Expected output**:

   ```text
   Interface: vmk10
     IP Protocol: IPv4
     Interface Type: vsan
     Traffic Type: witness
     Multicast supported: true
     Agent: vsan.agent
     Agent V2: vmknicEsx70.agent
     Unicast Agent: vmknicEsx70.unicast.agent
   ```

   **Verify**:

   - `Interface` shows the correct VMkernel ID (e.g., `vmk10`)
   - `Traffic Type` shows **witness**

6. Repeat steps 1-5 for **all Site A hosts**

**Expected Result**: All Site A hosts have VMkernel adapter tagged with `Traffic Type: witness`

#### Step 2.3: Tag VMkernel Adapters on Site B Hosts

For **each ESXi host in Site B**:

1. Follow **the exact same procedure as Step 2.2** (steps 1-6)

2. Use Site B host IPs and verify Site B VMkernel IP addresses

**Expected Result**: All Site B hosts have VMkernel adapter tagged with `Traffic Type: witness`

**Checkpoint**: Verify witness tagging on all hosts:

- [ ] All Site A hosts: `esxcli vsan network list` shows `Traffic Type: witness`
- [ ] All Site B hosts: `esxcli vsan network list` shows `Traffic Type: witness`

---

### Phase 3: Configure Static Routes on ESXi Hosts

This phase configures persistent static routes to ensure symmetric routing via site-local gateways.

#### Step 3.1: Configure Static Routes on Site A Hosts

For **each ESXi host in Site A**:

1. SSH to the Site A ESXi host (if not already connected)

2. Add static route to witness network via Site A gateway:

   ```bash
   esxcli network ip route ipv4 add -n <witness_network> -g <site_a_gateway>
   ```

   **Example** (Witness network: `192.168.100.0/24`, Site A gateway: `10.10.1.1`):

   ```bash
   esxcli network ip route ipv4 add -n 192.168.100.0/24 -g 10.10.1.1
   ```

3. Verify the route was added:

   ```bash
   esxcli network ip route ipv4 list | grep 192.168.100
   ```

   **Expected output**:

   ```text
   192.168.100.0/24  10.10.1.1  vmk10  0  1500
   ```

   **Verify**:

   - Network shows witness subnet (e.g., `192.168.100.0/24`)
   - Gateway shows Site A local gateway (e.g., `10.10.1.1`)
   - Interface shows witness VMkernel (e.g., `vmk10`)

4. **Make the route persistent across reboots**:

   Create a dedicated startup script to re-add the route after ESXi reboot:

   ```bash
   cat > /etc/rc.local.d/99-witness-route.sh << 'EOF'
   #!/bin/sh
   # Add static route for vSAN witness traffic - Site A
   esxcli network ip route ipv4 add -n <witness_network_cidr> -g <site_a_gateway_ip>
   exit 0
   EOF
   ```

   Make the script executable:

   ```bash
   chmod +x /etc/rc.local.d/99-witness-route.sh
   ```

   **Note**: Replace `<witness_network_cidr>` and `<site_a_gateway_ip>` with your actual witness network and Site A gateway values

   **Important**: Using a uniquely named script (`99-witness-route.sh`) prevents accidental overwriting of existing ESXi customizations in `local.sh`

5. Verify the startup script was created:

   ```bash
   cat /etc/rc.local.d/99-witness-route.sh
   ```

6. Test connectivity to witness host via the new route:

   ```bash
   vmkping -I vmk10 <witness_host_ip>
   ```

   **Example**:

   ```bash
   vmkping -I vmk10 192.168.100.50
   ```

   **Expected result**:

   ```text
   PING 192.168.100.50 (192.168.100.50): 56 data bytes
   64 bytes from 192.168.100.50: icmp_seq=0 ttl=64 time=2.5 ms
   64 bytes from 192.168.100.50: icmp_seq=1 ttl=64 time=1.8 ms
   ```

   **Verify**:

   - Ping succeeds (packets sent and received)
   - RTT (round-trip time) is reasonable (<200ms per VMware requirements)
   - No packet loss

7. Repeat steps 1-6 for **all Site A hosts**

**Expected Result**: All Site A hosts have:

- Static route to witness network via Site A gateway
- Persistent route configuration (survives reboot)
- Successful vmkping to witness host

#### Step 3.2: Configure Static Routes on Site B Hosts

For **each ESXi host in Site B**:

1. Follow **the exact same procedure as Step 3.1** (steps 1-7), using **Site B gateway** IP

   Add static route:

   ```bash
   esxcli network ip route ipv4 add -n <witness_network_cidr> -g <site_b_gateway_ip>
   ```

2. Create the startup script with Site B gateway:

   ```bash
   cat > /etc/rc.local.d/99-witness-route.sh << 'EOF'
   #!/bin/sh
   # Add static route for vSAN witness traffic - Site B
   esxcli network ip route ipv4 add -n <witness_network_cidr> -g <site_b_gateway_ip>
   exit 0
   EOF
   chmod +x /etc/rc.local.d/99-witness-route.sh
   ```

**Expected Result**: All Site B hosts have:

- Static route to witness network via **Site B gateway**
- Persistent route configuration (survives reboot)
- Successful vmkping to witness host

**Checkpoint**: Verify static routing on all hosts:

- [ ] All Site A hosts: `esxcli network ip route ipv4 list` shows route via Site A gateway
- [ ] All Site B hosts: `esxcli network ip route ipv4 list` shows route via Site B gateway
- [ ] All hosts: `vmkping -I vmk10 <witness_ip>` succeeds

---

## Validation

### Validation Step 1: Verify VMkernel Adapter Configuration

For **each ESXi host** (Site A and Site B):

1. SSH to the host

2. List VMkernel adapters and verify witness VMkernel exists:

   ```bash
   esxcli network ip interface ipv4 get | grep vmk
   ```

   **Verify**:

   - Witness VMkernel adapter (e.g., `vmk10`) is present
   - IP address matches expected value (Site A subnet for Site A hosts, Site B subnet for Site B hosts)
   - Netmask is correct (e.g., `255.255.255.0`)

### Validation Step 2: Verify vSAN Witness Traffic Tagging

For **each ESXi host**:

1. Verify witness traffic tagging:

   ```bash
   esxcli vsan network list
   ```

   **Expected**:

   - `Traffic Type: witness` appears for the VMkernel adapter
   - Interface ID matches (e.g., `vmk10`)

### Validation Step 3: Verify Static Routing

For **Site A hosts**:

1. Verify route exists and uses Site A gateway:

   ```bash
   esxcli network ip route ipv4 list | grep <witness_network>
   ```

   **Expected**: Gateway shows Site A local gateway IP (e.g., `10.10.1.1`)

For **Site B hosts**:

1. Verify route exists and uses Site B gateway:

   ```bash
   esxcli network ip route ipv4 list | grep <witness_network>
   ```

   **Expected**: Gateway shows Site B local gateway IP (e.g., `10.20.1.1`)

### Validation Step 4: Verify Witness Connectivity

For **each ESXi host**:

1. Test connectivity via witness VMkernel interface:

   ```bash
   vmkping -I <vmkernel_id> -c 5 <witness_host_ip>
   ```

   **Example**:

   ```bash
   vmkping -I vmk10 -c 5 192.168.100.50
   ```

   **Expected**:

   - 0% packet loss
   - RTT < 200ms (VMware requirement for witness latency)
   - 5 packets sent, 5 received

### Validation Step 5: Verify vSAN Health Status

1. Log in to vCenter Server

2. Navigate to **Menu** > **vSAN** > **Health**

3. Select the stretched cluster

4. Verify:

   - [ ] **Network** section: All checks pass (green)
   - [ ] **Data** section: No network-related warnings
   - [ ] **Cluster** section: Witness host shows as reachable

5. Expand **Network** > **vSAN witness network** and verify:

   - [ ] Witness host connectivity: OK
   - [ ] All ESXi hosts show witness connectivity as healthy

### Validation Step 6: Verify Route Persistence (Optional - Reboot Test)

**Warning**: This test requires ESXi host reboot. Only perform in maintenance window.

For **one test host** (e.g., one Site A host):

1. Put host in Maintenance Mode (vCenter: Right-click host > Maintenance Mode)

2. Reboot the host:

   ```bash
   reboot
   ```

3. Wait for host to boot and exit Maintenance Mode

4. SSH to the host and verify the static route still exists:

   ```bash
   esxcli network ip route ipv4 list | grep <witness_network>
   ```

   **Expected**: Route is present (confirms persistence script works)

5. If successful, repeat for other hosts during planned maintenance windows

---

## Rollback Procedure

If configuration needs to be reverted:

### Rollback Step 1: Remove Witness Traffic Tagging

For **each ESXi host**:

1. SSH to the host

2. Remove witness traffic tag from VMkernel adapter:

   ```bash
   esxcli vsan network ip remove -i <vmkernel_adapter_id>
   ```

   **Example**:

   ```bash
   esxcli vsan network ip remove -i vmk10
   ```

3. Verify removal:

   ```bash
   esxcli vsan network list
   ```

   **Expected**: VMkernel adapter no longer appears in vSAN network list

### Rollback Step 2: Remove Static Routes

For **each ESXi host**:

1. Remove static route:

   ```bash
   esxcli network ip route ipv4 remove -n <witness_network> -g <gateway>
   ```

   **Example** (Site A):

   ```bash
   esxcli network ip route ipv4 remove -n 192.168.100.0/24 -g 10.10.1.1
   ```

2. Remove persistence script:

   ```bash
   rm /etc/rc.local.d/99-witness-route.sh
   ```

3. Verify route removal:

   ```bash
   esxcli network ip route ipv4 list | grep <witness_network>
   ```

   **Expected**: No output (route removed)

### Rollback Step 3: Delete VMkernel Adapters (Optional)

If VMkernel adapters need to be completely removed:

1. In vCenter: Select host > **Configure** > **Networking** > **VMkernel adapters**

2. Select the witness VMkernel adapter (e.g., `vmk10`)

3. Click **Remove** (trash icon)

4. Confirm deletion

5. Repeat for all hosts

**Warning**: Only delete VMkernel adapters if witness traffic separation is being permanently removed. For temporary troubleshooting, keep VMkernel adapters and only remove tags/routes.

---

## Troubleshooting

### Issue 1: VMkernel Adapter Creation Fails

**Symptom**: Cannot create VMkernel adapter in vCenter UI

**Possible Causes**:

- Distributed Port Group does not exist or is misconfigured
- Insufficient vCenter privileges
- ESXi host is disconnected or in Maintenance Mode

**Resolution**:

1. Verify Distributed Port Group exists: vCenter > **Networking** > **Distributed Port Groups**
2. Verify vCenter user has Administrator role
3. Verify ESXi host is Connected and not in Maintenance Mode
4. Check vCenter tasks for detailed error messages

### Issue 2: Witness Traffic Tagging Fails

**Symptom**: `esxcli vsan network ip add` command fails with error

**Possible Causes**:

- VMkernel adapter ID incorrect or does not exist
- vSAN is not enabled on the cluster
- ESXi version does not support witness traffic tagging (< 7.0 U3)

**Resolution**:

1. Verify VMkernel adapter ID:

   ```bash
   esxcli network ip interface list
   ```

2. Verify vSAN is enabled: vCenter > Cluster > **Configure** > **vSAN** > **Services**
3. Verify ESXi version: `vmware -v` (must be 7.0 U3 or later)

### Issue 3: vmkping to Witness Fails

**Symptom**: `vmkping -I vmk10 <witness_ip>` shows 100% packet loss

**Possible Causes**:

- Static route not configured or incorrect
- Network firewall blocking traffic
- Witness host unreachable from site network
- Distributed Port Group VLAN/network misconfiguration

**Resolution**:

1. Verify static route is configured:

   ```bash
   esxcli network ip route ipv4 list | grep <witness_network>
   ```

2. Verify VMkernel adapter can reach its own gateway:

   ```bash
   vmkping -I vmk10 <site_gateway_ip>
   ```

3. Test from site gateway to witness (coordinate with network team)
4. Verify firewall rules permit port 2233 from VMkernel subnet to witness
5. Verify Distributed Port Group VLAN matches network team configuration

### Issue 4: Static Route Does Not Persist After Reboot

**Symptom**: Route exists before reboot but disappears after ESXi restart

**Possible Causes**:

- `/etc/rc.local.d/99-witness-route.sh` script not created or not executable
- Script syntax error
- ESXi local configuration backup issue

**Resolution**:

1. Verify script exists and is executable:

   ```bash
   ls -la /etc/rc.local.d/99-witness-route.sh
   ```

   **Expected**: `-rwxr-xr-x` (executable permissions)

2. Verify script contents:

   ```bash
   cat /etc/rc.local.d/99-witness-route.sh
   ```

3. If script missing or incorrect, recreate it (see Phase 3, Step 3.1, step 4)

4. Force ESXi local configuration backup:

   ```bash
   /sbin/auto-backup.sh
   ```

### Issue 5: Asymmetric Routing Still Occurring

**Symptom**: Traffic appears to still route through Inter-DC Link instead of site-local gateway

**Possible Causes**:

- Static route gateway is incorrect (pointing to Inter-DC Link gateway instead of site-local)
- Default route has higher priority than static route
- Network routing configuration issue (coordinate with network team)

**Resolution**:

1. Verify static route gateway matches site-local gateway:

   ```bash
   esxcli network ip route ipv4 list | grep <witness_network>
   ```

2. Verify there is no overlapping route with different gateway:

   ```bash
   esxcli network ip route ipv4 list
   ```

3. Use traceroute to verify path:

   ```bash
   esxcli network diag ping -I vmk10 -c 1 -D <witness_ip>
   ```

4. Coordinate with network team to verify:

   - Witness return traffic routes back through site-local gateway
   - No policy-based routing overriding static routes
   - Firewall NAT rules not causing asymmetric routing

### Issue 6: VMkernel IDs Mismatch Between Sites

**Symptom**: Site A hosts use `vmk10`, but Site B hosts have `vmk11` or different ID

**Impact**: vSAN witness failover may not function correctly

**Resolution**:

1. Identify the VMkernel ID discrepancy

2. Delete and recreate VMkernel adapters on the affected site to match the correct ID

3. **Important**: VMkernel IDs are assigned sequentially. To force a specific ID (e.g., `vmk10`):

   - Temporarily remove higher-numbered VMkernel adapters if necessary
   - Create new adapter (will be assigned next available ID)
   - Recreate previously removed adapters

4. Verify all hosts use identical VMkernel ID:

   ```bash
   esxcli vsan network list
   ```

---

## Post-Implementation Tasks

After successful configuration:

1. **Update Documentation**:

   - [ ] Document VMkernel IDs, IP addresses, and VLAN IDs in network inventory
   - [ ] Update stretched cluster network diagram with witness traffic paths
   - [ ] Record distributed port group names in configuration management database

2. **Schedule Failover Test**:

   - [ ] Plan maintenance window for failover testing
   - [ ] Coordinate with application teams for impact
   - [ ] Test Site A failure scenario (verify Site B maintains witness connectivity)
   - [ ] Test Site B failure scenario (verify Site A maintains witness connectivity)
   - [ ] Document test results

3. **Monitoring Setup**:

   - [ ] Configure vCenter alarms for vSAN witness connectivity issues
   - [ ] Add vmkping tests to monitoring system (optional)
   - [ ] Document expected latency baselines for operational reference

4. **Update Operational Runbooks**:

   - [ ] Add witness traffic separation configuration to cluster build checklist
   - [ ] Update DR failover procedures to reference independent witness paths
   - [ ] Include troubleshooting steps in operations knowledge base

---

## Related Documentation

- **Design Documentation**: [lldInfrastructure.md](../design/lldInfrastructure.md) - Section "Witness Traffic Resilience in Stretched Clusters"
- **Build Guide**: [dhcBuildGuide.md](dhcBuildGuide.md) - Stretched cluster deployment procedures
- **VMware Documentation**: VMware vSAN Administration Guide - "Configure the VMkernel Adapters for Witness Traffic"

---

## Appendix A: Quick Reference Commands

### VMkernel Adapter Management

```bash
# List all VMkernel adapters
esxcli network ip interface ipv4 get

# List VMkernel adapters with details
esxcli network ip interface list

# Remove VMkernel adapter (CLI - use vCenter UI instead)
esxcli network ip interface remove -i <vmkernel_id>
```

### vSAN Network Configuration

```bash
# Tag VMkernel for witness traffic
esxcli vsan network ip add -i <vmkernel_id> -T=witness

# List vSAN network configuration
esxcli vsan network list

# Remove witness traffic tag
esxcli vsan network ip remove -i <vmkernel_id>

# Verify vSAN version
esxcli vsan version get
```

### Static Route Management

```bash
# Add static route
esxcli network ip route ipv4 add -n <network>/<prefix> -g <gateway>

# List all routes
esxcli network ip route ipv4 list

# Remove static route
esxcli network ip route ipv4 remove -n <network>/<prefix> -g <gateway>

# Example: Add route to witness network
esxcli network ip route ipv4 add -n 192.168.100.0/24 -g 10.10.1.1
```

### Connectivity Testing

```bash
# Ping via specific VMkernel adapter
vmkping -I <vmkernel_id> <destination_ip>

# Ping with packet count
vmkping -I vmk10 -c 5 192.168.100.50

# Ping with size (test MTU)
vmkping -I vmk10 -s 1472 -d 192.168.100.50

# Network diagnostic ping (shows more details)
esxcli network diag ping -I <vmkernel_id> -c 5 -D <destination_ip>
```

### Service Management

```bash
# List all services
esxcli system service list

# Start SSH service
esxcli system service start -n TSM-SSH

# Stop SSH service
esxcli system service stop -n TSM-SSH

# Enable SSH service (persistent)
esxcli system ssh enable

# Disable SSH service
esxcli system ssh disable
```

### Configuration Backup

```bash
# Trigger ESXi configuration backup
/sbin/auto-backup.sh

# View backup status
ls -la /bootbank/state.tgz
```

---

## Appendix B: Example Configuration

This appendix provides a complete example configuration for reference.

### Example Environment

- **Stretched Cluster**: 8 hosts (4 in Site A, 4 in Site B)
- **Site A Location**: LBG (London)
- **Site B Location**: BBP (Dublin)
- **Witness Location**: Cloud-hosted witness appliance

### Example Network Configuration

| Parameter                    | Site A (LBG)                     | Site B (BBP)                     |
|------------------------------|----------------------------------|----------------------------------|
| Distributed Port Group       | `VSAN Witness communication LBG` | `VSAN Witness communication BBP` |
| VLAN ID                      | 1650                             | 1651                             |
| VMkernel Subnet              | 10.10.5.0/24                     | 10.20.5.0/24                     |
| VMkernel Adapter ID          | vmk10                            | vmk10 (MUST MATCH)               |
| Site Gateway                 | 10.10.5.1                        | 10.20.5.1                        |
| Witness Host IP              | 192.168.100.50                   | 192.168.100.50                   |
| Witness Network              | 192.168.100.0/24                 | 192.168.100.0/24                 |

### Example IP Addressing

| Host                  | Site | VMkernel IP  | Gateway    |
|-----------------------|------|--------------|------------|
| esx-lbg-01.domain.com | A    | 10.10.5.11   | 10.10.5.1  |
| esx-lbg-02.domain.com | A    | 10.10.5.12   | 10.10.5.1  |
| esx-lbg-03.domain.com | A    | 10.10.5.13   | 10.10.5.1  |
| esx-lbg-04.domain.com | A    | 10.10.5.14   | 10.10.5.1  |
| esx-bbp-01.domain.com | B    | 10.20.5.11   | 10.20.5.1  |
| esx-bbp-02.domain.com | B    | 10.20.5.12   | 10.20.5.1  |
| esx-bbp-03.domain.com | B    | 10.20.5.13   | 10.20.5.1  |
| esx-bbp-04.domain.com | B    | 10.20.5.14   | 10.20.5.1  |

### Example Commands for Site A Host (esx-lbg-01)

```bash
# Step 1: Create VMkernel adapter (via vCenter UI)
# - Distributed Port Group: "VSAN Witness communication LBG"
# - IPv4 address: 10.10.5.11
# - Subnet mask: 255.255.255.0
# - VMkernel ID assigned: vmk10

# Step 2: Tag VMkernel for witness traffic
ssh root@esx-lbg-01.domain.com
esxcli vsan network ip add -i vmk10 -T=witness

# Step 3: Add static route
esxcli network ip route ipv4 add -n 192.168.100.0/24 -g 10.10.5.1

# Step 4: Create persistence script
cat > /etc/rc.local.d/99-witness-route.sh << 'EOF'
#!/bin/sh
# Add static route for vSAN witness traffic - Site A LBG
esxcli network ip route ipv4 add -n 192.168.100.0/24 -g 10.10.5.1
exit 0
EOF
chmod +x /etc/rc.local.d/99-witness-route.sh

# Step 5: Verify configuration
esxcli vsan network list
esxcli network ip route ipv4 list | grep 192.168.100
vmkping -I vmk10 -c 5 192.168.100.50

# Expected vmkping output:
# PING 192.168.100.50 (192.168.100.50): 56 data bytes
# 64 bytes from 192.168.100.50: icmp_seq=0 ttl=64 time=3.2 ms
# 64 bytes from 192.168.100.50: icmp_seq=1 ttl=64 time=2.8 ms
# 64 bytes from 192.168.100.50: icmp_seq=2 ttl=64 time=2.9 ms
# 64 bytes from 192.168.100.50: icmp_seq=3 ttl=64 time=3.1 ms
# 64 bytes from 192.168.100.50: icmp_seq=4 ttl=64 time=2.7 ms
#
# --- 192.168.100.50 ping statistics ---
# 5 packets transmitted, 5 packets received, 0% packet loss
# round-trip min/avg/max = 2.7/2.9/3.2 ms
```

### Example Commands for Site B Host (esx-bbp-01)

```bash
# Step 1: Create VMkernel adapter (via vCenter UI)
# - Distributed Port Group: "VSAN Witness communication BBP"
# - IPv4 address: 10.20.5.11
# - Subnet mask: 255.255.255.0
# - VMkernel ID assigned: vmk10 (MUST MATCH Site A)

# Step 2: Tag VMkernel for witness traffic
ssh root@esx-bbp-01.domain.com
esxcli vsan network ip add -i vmk10 -T=witness

# Step 3: Add static route (using Site B gateway)
esxcli network ip route ipv4 add -n 192.168.100.0/24 -g 10.20.5.1

# Step 4: Create persistence script (using Site B gateway)
cat > /etc/rc.local.d/99-witness-route.sh << 'EOF'
#!/bin/sh
# Add static route for vSAN witness traffic - Site B BBP
esxcli network ip route ipv4 add -n 192.168.100.0/24 -g 10.20.5.1
exit 0
EOF
chmod +x /etc/rc.local.d/99-witness-route.sh

# Step 5: Verify configuration
esxcli vsan network list
esxcli network ip route ipv4 list | grep 192.168.100
vmkping -I vmk10 -c 5 192.168.100.50
```
