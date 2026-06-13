# Configure NSX-T Components using configureNsxt.yml ansible playbook
  
# Changelog

| Version | Date       | Description                    | Author               |
| ------- | ---------- | ------------------------------ | -------------------- |
| 0.1     | 26.08.2024 | First version                  | Paweł Żurawski       |
| 0.2     | 23.02.2026 | Corrected edge variable naming | Stanislaw Kilanowski |
  
# Introduction

It is important to understand that since VCS 2.0 , VCS SDN Design introduced the use of a new NSX-T 4.x function called Project.  
Projects allow for limiting the scope of NSX-T object that can be manipulated using API, also it is limiting the scope that can be viewed in GUI. This allows providing access to a specific project for Customers, as such all objects that can be configured by Customer both from SSR and from GUI will no longer be part of configureNsxt.yml ansible playbook.  
  
Objects that will no longer be part of configureNsxt.yml ansible playbook are:

- T1 logical router
- NAT rules
- FW rules
- logical segments
  
Following objects are still possible to be configured using configureNsxt.yml ansible playbook.

- NSX-T Project
- T0 VRF logical router
- T0 Template logical router
- NSX-T Edge Node
- NSX-T Edge Cluster
- Vlan Transport Zone
  
It is important to understand a distinction between SSR/GUI configuration that can be done by Customer and Order-to-Ticket approach used in configureNsxt.yml ansible playbook. That is executed only by Atos and Customer will never have access to it.
  
configureNsxt.yml ansible playbook using customerInfraVars.yml file as input file for creating NSX-T components, how to generate and use customerInfraVars.yml file please see following WI:  
wiCustomerInfraVars.md  
In this document sections of wiCustomerInfraVars.md will be explained in detail but only for NSX-T components that will be described in this document.  
  
## Purpose
  
The purpose of this Work Instruction is to describe how to use configureNsxt.yml ansible playbook to configure NSX-T components that are not part of NSX-T Projects.  
  
## Audience
  
- VCS Engineering
- VCS Operations
  
## Scope
  
This document covers the following tasks and activities:
  
- Create and Configure NSX-T VLAN Transport Zone
- Create and Configure NSX-T Edge Cluster
- Create and Configure NSX-T Edge Node
- Create and Configure T0 VRF Logical Router
- Create and Configure T0 Template Logical Router
- Create and Configure NSX-T Project
  
# Deployment steps

## Create and Configure NSX-T Edge Node

### What is NSX-T Transport Zone

NSX-T introduced a concept on Transport Zone, we have two types of NSX-T Transport Zones

- NSX-T Overlay Transport Zone  
- NSX-T VLAN Transport Zone
  
NSX-T Transport zone allows to group NSX-T Edge Nodes, NSX-T Host Nodes into groups that will be able to handle specific VLAN Segments or Overlay Segments.  
NSX-T Overlay Transport Zones need to be configured on NSX-T Host Node (ESXi Node) to allow ESXi Host maintaining Overlay Segment (Logical Switch), for that reason in VCS design only single OTZ is used, as such additional OTZ will not be configured and are not included configureNsxt.yml ansible playbook and is not part of this document.  
NSX-T VLAN Transport Zone (VTZ), VTZ can decide if VLAN (connected to VTZ) will be able to be used in NSX-T Edge Node (connected to VTZ), both NSX-T Edge Node and VLAN Segment need to be configured using the same VTZ in order to VLAN Segment will be used on NSX-T Edge as uplink segment connecting VCS NSX-T network with outside word.  
  
### prerequisited to create NSX-T VLAN Transport Zone

None
  
### customerInfraVars.yml section related to NSX-T VLAN Transport Zone

```yaml

  transportZone:
    action: ignore
    spec:
      - deployName: gre26vtz001
        transport Type: VLAN
        description:

```

action: by default action is ignored, if you want to create a new VTZ please change it to create  
deployName: VTZ name, please see VCS naming convention LLD for more details
transportType: VLAN , please do not change that value
description: optional description if needed
  
## Create and Configure NSX-T Edge Cluster

### What is NSX-T Edge Node
  
NSX-T Edge Node is a Virtual Machine that is used as in-line VM for Nort-South transit, allowing for IP manipulations as (Routing Protocols like OSPF,BGP , NAT , Load-Balancing), each T0 and T1 logical router need to be connected to NSX-T Edge Cluster.  
  
### prerequisited to create NSX-T VLAN Transport Zone
  
vSphere Resource Pool where NSX-T VM will be deployed, Resource Pool needed to be created before execution
NSX-T VLAN Transport Zone that will be used to configure NSX-T Edge Node, VLAN Transport Zone needs to be created before execution
  
### customerInfraVars.yml section related to NSX-T VLAN Transport Zone

```yaml

  edgeNode:
    action: ignore
    spec:
      - name:
        size: "large"
        resourcePool: "gre26-c01-user-edge01"
        transportZoneVlan: "gre26vtz001"

```

action: by default action is ignored, if you want to create a new NSX-T Edge Node please change it to create  
name: NSX-T Edge Node name, please see VCS naming convention LLD for more details
size: by default size is set up to large, if you need to change it to Extra Large please change it to xlarge, it is not advisable to change it too small size
resourcePool: vSphere Resource Pool where NSX-T Edge Node VM will be deployed, it is not advisable to change default VCS value
transportZoneVlan: NSX-T VLAN Transport Zone, it is not advisable to change to default value
  
## Create and Configure NSX-T Edge Cluster

### What is NSX-T Edge Cluster

Every Logical Router (T0) needed to be configured with NSX-T Edge Cluster, NSX-T Edge CLuster is used to create SR and DR for each T0/T1 Logical Router, NSX-T Edge Cluster is also used to create T0 Uplink interfaces.  
Each NSX-T Edge Cluster consists 1 to 8 NSX-T Edge Nodes. In VCS design NSX-T Edge Cluster consists of 2 NSX-T Edge Nodes, and this is only allowed number for NSX-T Edge Cluster creations when done from configureNsxt.yml ansible playbook  
  
### prerequisited to create NSX-T Edge Cluster

NSX-T Edge Node that will be part of NSX-T Edge Cluster needs to be created before NSX-T Edge Cluster will be created  
  
### customerInfraVars.yml section related to NSX-T Edge Cluster

```yaml
edgeCluster:
    action: ignore
    spec:
#      name:
      members:
#        - name:
#        - name:
      profile: "nsx-default-edge-high-availability-profile"

```

action: by default action is ignored, if you want to create a new NSX-T Edge Cluster please change it to create
name: NSX-T Edge Cluster name, please see VCS Naming Convention LLD for details
members.name: NSX-T Edge Node VM names, used as NSX-T Edge Cluster members
profile: HA profile used to configure BFD timers, it is not advisable to change default settings
  
## Create and Configure NSX-T T0 Template Logical Router

### What is NSX-T T0 Template

NSX-T T0 Template, is NSX-T T0 Logical Router that is used as Parent to create NSX-T T0 VRF Logical Router.  
It is used only to provide BGP settings that will be shared with each NSX-T T0 VRF connected to this T0 Logical Router.  
No traffic is traversing this Logical Router.  

### prerequisited to create NSX-T T0 Template

NSX-T VLAN Transport Zone that will be used to configure NSX-T Edge Node, VLAN Transport Zone needs to be created before execution
NSX-T Edge Cluster that will be used for hosting T0 Logical Router SR/DR VRFs, needs to be created

### customerInfraVars.yml section related to NSX-T T0 Template

```yaml

  logicalRouterT0template:
    action: ignore
    spec:
      - name:
        edgeClusterName:
        ha_mode: "ACTIVE_ACTIVE"
        ha_failover_mode: NON_PREEMPTIVE
        gatewayFirewallEnabled: false
        uplinkVlanSegment:
          name:
          transportZone: "gre26vtz001"
          segmentType: VLAN
          vlanTag: 0
        uplinkNode1InterfaceName: template-uplink1
        uplinkNode1InterfaceIpAddress: 169.254.10.1
        uplinkNode1InterfaceSubnetMaskLength: 24
        uplinkNode2InterfaceName: template-uplink2
        uplinkNode2InterfaceIpAddress: 169.254.10.2
        uplinkNode2InterfaceSubnetMaskLength: 24
        bgpLocalASN:
        bgpGRESmode: HELPER_ONLY
        bgpGREStimerRestart: 180
        bgpGREStimerStaleRouter: 600

```

action: by default action is ignored, if you want to create a new NSX-T T0 Template please change it to create  
name: T0 Logical Router name, please see VCS Naming Conventions LLD for more details
edgeClusterName: Edge Cluster name that will be used to host T0
bgplocalASN: local BGP AS number, please see network design for this value, this value is mandatory

any other value shouldn't be changed if you are not absolutely sure that you know what are you doing
  
## Create and Configure NSX-T T0 VRF

### What is NSX-T T0 VRF

NSX-T T0 VRF is Logical Router that is created per customer (usually one T0 VRF per customer) allowing providing uplink connection for customer workload.  
In VCS design BGP is used to exchange routes between VCS and DC.

It is important to understand that even there it is on T0 VRF in NSX-T GUI, this is only abstraction in fact this is cluster as T0 VRF is deployed on Edge Cluster that in VCS design consist 2 NSX- Edge Nodes

### prerequisited to create NSX-T T0 Template

NSX-T T0 Template that will be used for this T0 VRF needs to be created.
Edge Cluster that will be used to host T0 VRF needs to be created.
network and BGP design, as there is a lot of network information needed like VLAN id, IP addresses and BGP AS numbers, network design is a prerequisite here, it is not a goal of this document to describe how are BGP or VLANs working

### customerInfraVars.yml section related to NSX-T T0 Template

```yaml

  logicalRouterT0vrf:
    action: ignore
    spec:
      - name:
        linkedT0TemplateName:
        edgeClusterName:
        uplinkVlanSegment:
          name:
          transportZone: "gre26vtz001"
          segmentType: VLAN
          vlanTag: 
        uplinkNode1InterfaceName: uplink1
        uplinkNode1InterfaceIpAddress:
        uplinkNode1InterfaceSubnetMaskLength:
        uplinkNode2InterfaceName: uplink2
        uplinkNode2InterfaceIpAddress:
        uplinkNode2InterfaceSubnetMaskLength:
        bgpNeighbor1IpAddress:
        bgpNeighbor1Password:
        bgpNeighbor1MaxHops: 1
        bgpNeighbor2IpAddress:
        bgpNeighbor2Password:
        bgpNeighbor2MaxHops: 1
        bgpNeighborsAsn:
        bgpNeighborsAllowAsIn: false
        bgpNeighborsKeepAliveTime: 60
        bgpNeighborsHoldDownTime: 180
        bgpNeighborsBfdEnabled: false
        bgpNeighborsBfdInterval: 500
        bgpNeighborsBfdMultiplier: 3

```

action: by default action is ignored, if you want to create new NSX-T T0 VRF please change it to create  
name: T0 Logical Router name, please see VCS Naming Conventions LLD for more details
edgeClusterName: Edge Cluster name that will be used to host T0
linkedT0TemplateName: NSX-T T0 Template name that we will link with T0 VRF
vlanTag: VLAN id used for transit network to connect to DC BGP peers
uplinkNode1InterfaceIpAddress: local IP address assigned to Node 1, please see network design for details
uplinkNode1InterfaceSubnetMaskLength: local mask length for example /24, please see network design for details
uplinkNode2InterfaceIpAddress: local IP address assigned to Node 2, please see network design for details
uplinkNode2InterfaceSubnetMaskLength: local mask length for example /24, please see network design for details
bgpNeighbor1IpAddress: IP address for BGP neighbor on DC site
bgpNeighbor1Password: password that is protecting BGP session
bgpNeighbor2IpAddress: IP address for BGP neighbor on DC site
bgpNeighbor2Password: password that is protecting BGP session
bgpNeighborsAsn: BGP peer AS number, as this is eBGP session it needs to be different than local AS number

any other value shouldn't be changed if you are not absolutely sure that you know what are you doing

## Create and Configure NSX-T Project

### What is NSX-T Project

NSX-T Project is a new feature in NSX-T 4.x and VCS 2.x , it allows limiting scope of allowed to view/modify objects. User who has permission level on Project scope, will see only objects that will be created under that project. Creation of objects under Project is not in scope of this document. configureNsxt.yml ansible playbook will be used only to create raw Project, any objects under NSX-T Project will be created from SSRs

### prerequisited to create NSX-T Project

NSX-T T0 VRF that will be used as Uplink for T1 Logical Routers created under Project  
NSX-T Edge Cluster that will be used to host T1 Logical Router created under Project

### customerInfraVars.yml section related to NSX-T Project

```yaml

  nsxtProject:
    action: ignore
    spec:
      - name:
        t0gateway:
        edgeCluster:

```

action: by default action is ignored, if you want to create a new NSX-T Project please change it to create  
name: NSX-T Project name
edgeCluste: Edge Cluster name that will be used to host T1 Routers created under NSX-T Project
t0gateway: NSX-T T0 VRF name that will be used as uplink for T1 Routers created under NSX-T Project
