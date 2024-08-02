---
title: MULTIPLE SPANNING TREE PROTOCOL (MSTP)
date: 2024-08-1 00:00:00 +0800
categories: [CISCO, CCNP]
tags: [cisco, ccnp]     # TAG names should always be lowercase
---

In most scenarios, implementing PVST (Per-VLAN Spanning Tree) or Rapid-PVST (Rapid Per-VLAN Spanning Tree) is sufficient for a simple network design. However, in a large enterprise network, the number of interconnected switches can lead to an increase in BPDU (Bridge Protocol Data Unit) advertisements and frequent recalculations of the best path to the root bridge whenever there is a topology change. To address this issue, the Multiple Spanning Tree Protocol (MSTP) can be employed.

MSTP is a Layer 2 protocol designed to manage the overload of BPDU advertisements that occur with each VLAN’s STP instance, as seen with PVST. It works by grouping switches into what is known as an MST Region, which confines BPDU advertisements within this specific group of switches. This approach minimizes the need for STP recalculations across the entire network, limiting them only to the switches within the MST Region when a topology change occurs.

# MST Region
A switches that has been configured and assigned into specific group or instance creating a single virtual switch from the perspective of the switches that are not belong to the group.

![](https://i.imgur.com/bDOMZLl.png)

# MSTP Key Terminology

>**KeyTerms**
>
>**Internal Spanning Tree (IST)**
>- IST is associated with VLANs that are not assigned to a specific MST Instance (MSTI).
>- By default, all VLANs are initially associated with MST instance 0.
>  
>**MST Instances (MSTI)**
>- MSTI is a group of multiple VLANs that share a single BPDU advertisement per MST instance, rather than generating a BPDU for each VLAN individually.
>- MSTI instance values can range from 1 to 15.
{: .prompt-tip }


# MSTP Configuration
This section will demonstrate the basic configuration of MSTP using the diagram below. Please note that all switches are initially configured with two additional VLANs (VLAN 10 and VLAN 20). The rest of the settings are at their default values, and MST has not yet been configured on the switches.

![](https://i.imgur.com/OQZks7D.png)

```console
show vlan brief
```

![](https://i.imgur.com/aky8XZf.png)

## Enable MSTP Mode

By default, MSTP is not enabled on switches. To enable it, use the following CLI command on switches SW4, SW5, SW6, and SW7.
```console
configure terminal
	spanning-tree mode mst
```
### Checking MST Configuration
Once MSTP is enabled, all VLANs are automatically assigned to Instance 0 by default, as shown in the picture below.
```console
show spanning-tree mst configuration
```
![](https://i.imgur.com/FYn3n60.png)


## MSTP Configuration
We will assign VLAN 10 and VLAN 20 to different instances (Instance 1 and Instance 2) and assign a name to our MST region using the following CLI commands.
```console
configure terminal
	spanning-tree mst configuration
		name REGION_1
		instance 1 vlan 10
		instance 2 vlan 20
```

### Checking MST Configuration
After configuring MSTP, the VLANs are now assigned to their respective instances.

![](https://i.imgur.com/V9npLWJ.png)

## MSTP Tuning
### MSTP Root Switch
Similar to PVST, you can configure which switch becomes the Root for each instance by using either of the CLI commands below.
```console
configure terminal
	spanning-tree mst [instance-number] root [primary | secondary]
```

```console
configure terminal
	spanning-tree mst [instance-number] priority [0 - 61440]
```
### MSTP Interface Cost and Priority
```console
configure terminal
	interface [interface-id]
		spanning-tree mst [instance-number] cost [value]
		spanning-tree mst [instance-number] port-priority [value]
```