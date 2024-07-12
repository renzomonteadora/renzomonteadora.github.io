---
title: SPANNING TREE PROTOCOL (STP)
date: 2024-07-11 00:00:00 +0800
categories: [CISCO, CCNP]
tags: [cisco, ccnp]     # TAG names should always be lowercase
---

In an enterprise environment, redundancy is one of the key factors in a well-designed network. It enables a network to become more fault-tolerant, and it can increase the throughput of traffic by balancing the load between devices. The simplest way to achieve redundancy is by adding the same device, or, for the sake of this topic, an additional switch in the topology. However, in a layer-2 network, adding switches to the topology introduces problems such as broadcast storms, frame loops, and unstable entries in the MAC address table. This issue is caused by the way Ethernet frames work. To address this problem, the Spanning Tree Protocol (STP) was created and enabled on switches.

STP is a layer-2 protocol that enables switches to become aware of other switches connected within the same network. It uses Bridge Protocol Data Units (BPDU) frames to communicate with other switches, determining which interfaces an Ethernet frame should be forwarded through, thereby avoiding the mentioned problems that may occur in a layer-2 network.


# STP Fundamentals

## STP Iteration

|    Protocol     | Proprietary | Spanning-Tree Calculation |
| :-------------: | :---------: | :-----------------------: |
|     **STP**     | IEEE 802.1D |         All VLANs         |
|    **PVST+**    |    Cisco    |         Per VLAN          |
|    **RSTP**     | IEEE 802.1W |         All VLANs         |
| **Rapid PVST+** |    Cisco    |         Per VLANs         |
|    **MSTP**     | IEEE 802.1S |       Per Instance        |

## STP Key Terminology
>**KeyTerms**
>
>**Root Bridge**
>- a switch that has been elected as a root.
>
>**Bridge Protocol Data Unit (BPDU)**
>- a packet that is used for network switches to process STP.
>
>**BPDU Types:**
>1. Configuration BPDU
>2. Topology Change Notification (TCN) BPDU
>
>**Root Path Cost**
>- a metric used for measuring the path to the root bridge.
>
>**System Priority**
>- a 4-bit value that indicates the hierarchy of switch in the STP topology (default value is 32768).
>- the lowest value is elected as root (value between 0-61440, increments of 4096).
>
>**System ID Extension**
>- a 12-bit value indicates the VLAN that the BPDU correlates to.
>
>**Root Bridge ID**
>- details of the switch that has been elected as a root.
>
>**Local Bridge ID**
>- details of the switch that you are managing currently.
{: .prompt-tip }

## STP Timers

>**Timers**
>
> **Hello Timer**
>- time interval that BPDU is being advertised (default value is ***2 seconds***).
>
>**Max Age**
>- maximum time that a bridge port saves the BPDU information.
>- **802.1D (STP)** = default value is 10 × Hello Timer = 20 seconds
>- **802.1W (RSTP)** = default value is 6 seconds
>
>**Forward Delay**
>- amount of time that a port stays in a ***listening*** and ***learning*** state (default value is ***15 seconds each state***).
>  
>**STP Convergence Time (Approx.)**
>- around **50 seconds** = 30s (Forward Delay) + 20s (Time that the switch needs to realize that the link is either up/down)
{: .prompt-tip }

# Root Bridge Election Process
A part of the process in building an STP topology is the election of which switch will assume the role of the _Root Bridge_. The elected switch serves as a central reference point for all other switches in calculating the path cost and selecting the best path to the frame's destination. This election is crucial in the building loop-free network by knowing which port role (root port, designated port, blocking port, etc.) will be configured on each of the connected interface in a switch. 

## Election Criteria

1. Lowest ***Bridge Priority***.
2. Lowest ***MAC Address***.

## Bridge ID Structure
A *Bridge ID* is a unique value that identifies each switch in an STP topology. It is used to determine which switch will be elected as a *Root Bridge*.

![](https://i.imgur.com/erPzyBf.png)
_Figure 1: Bridge ID Structure_


- **Bridge ID**
	- Bridge Priority
		- System Priority
		- VLAN ID
	- MAC Address

# STP vs RSTP
## Port States
![](https://i.imgur.com/TOiZcBf.png)
_Figure 2: STP and RSTP Port States_

## Port Roles
![](https://i.imgur.com/TP8Jt4s.png)
_Figure 3: STP and RSTP Port Roles_

| **Port Roles**           | Definition                                                                     |
| ------------------------ | ------------------------------------------------------------------------------ |
| **Root Port (RP)**       | A port on a switch that has the closest path (Lowest Cost) to the Root Bridge. |
| **Designated Port (DP)** | A port that can send the best BPDU on its segment.                             |
| **Alternate Port**       | A blocking port that serves as a backup of Root Port.                          |
| **Backup Port**          | A blocking port that serves as a backup of Designated Port.                    |

# STP Configuration
In this section, will show few CLI commands on how an STP topology can be configured and modified. 

## Checking the STP Topology

```console
show spanning-tree
```

![](https://i.imgur.com/xNJWxSC.png){: width="700" height="400"}


## Change STP Mode (STP or RSTP)

```console
configure terminal
	spanning-tree mode [mst | pvst | rapid-pvst]
```

## Bridge Priority Modification
```console
configure terminal
	spanning-tree vlan [vlan-id] priority [0 - 61440]
```

>**Note:** Bridge Priority must be in incriments of 4096
{: .prompt-tip }


## STP Timer Modification
```console
configure terminal
	spanning-tree vlan [vlan-id] [forward-time | hello-time | max-age] [seconds]
```


## Port Modification
### Portfast

- **Enable on all access ports**
	```console
	configure terminal
		spanning-tree portfast default
	```

- **Enable on a specific port**
	```console
	configure terminal
		interface [interface-id]
			spanning-tree portfast
	```

### BPDU Guard

- **Enable on all portfast ports**
	```console
	configure terminal
		spanning-tree portfast bpduguard default
	```

- **Enable on a specific port**
	```console
	configure terminal
		interface [interface-id]
			spanning-tree bpduguard enable
	```

### BPDU Filter

- **Enable on all portfast ports**
	```console
	configure terminal
		spanning-tree portfast bpdufilter default
	```

- **Enable on a specific port**
	```console
	configure terminal
		interface [interface-id]
			spanning-tree bpdufilter enable
	```
