
# Enterprise Network Design, Configuration & Security

## Project Overview

This project focuses on the configuration, security, and testing of a simulated enterprise network using Cisco Packet Tracer. Starting with an academic OSPF lab, I expanded the network to include departmental VLANs, inter-VLAN routing, secure remote administration, and network failover testing.

The purpose of this project was to gain practical experience with enterprise networking and understand how routing, switching, and security work together to maintain network connectivity.

The network consists of three Cisco routers, three switches, and four workstations connected across four LAN subnets.

## Project Objectives

- Configure IPv4 addressing and subnetting across multiple LANs.
- Establish dynamic routing using OSPF.
- Separate departmental broadcast traffic using VLANs.
- Configure inter-VLAN communication using router-on-a-stick.
- Secure remote router administration using SSH.
- Verify network connectivity through ping and traceroute.
- Evaluate network resilience during a simulated link failure.
- Document network configurations, troubleshooting, and testing results.

## Tools and Technologies

| Tool or Technology | Purpose |
|---|---|
| Cisco Packet Tracer | Network simulation and testing |
| Cisco IOS | Router and switch configuration |
| IPv4 | Network addressing and subnetting |
| OSPFv2 | Dynamic routing and route convergence |
| VLANs | Departmental network segmentation |
| IEEE 802.1Q | VLAN trunking |
| Router-on-a-Stick | Inter-VLAN routing |
| SSHv2 | Secure remote router administration |
| RSA Keys | SSH cryptographic authentication support |
| ICMP | Connectivity verification |
| Traceroute | Network path and failover analysis |

## Network Architecture

The network uses a triangular router topology to provide an alternative routing path if a connection between routers becomes unavailable.

The Administration and Finance departments are assigned separate VLANs to organize network traffic and reduce unnecessary broadcast traffic between departments.

### Network Topology

![Enterprise Network Topology](screenshots/01_network_topology.png)

### Network Components

| Component | Quantity |
|---|---:|
| Cisco 2911 Routers | 3 |
| Cisco 2960-24TT Switches | 3 |
| Workstations | 4 |
| Total Network Devices | 10 |
| LAN Subnets | 4 |
| Router-to-Router Links | 3 |
| Departmental VLANs | 2 |

## IPv4 Addressing and Subnetting

Different subnet sizes were selected based on the addressing requirements of each network segment. Point-to-point router connections use /30 subnets to minimize unnecessary IP address allocation.

| Network | Network Address | Subnet Mask | Default Gateway |
|---|---|---|---|
| Administration - VLAN 10 | 172.16.1.16/28 | 255.255.255.240 | 172.16.1.17 |
| Finance - VLAN 20 | 172.16.1.48/28 | 255.255.255.240 | 172.16.1.49 |
| PC-B LAN | 10.10.10.0/24 | 255.255.255.0 | 10.10.10.1 |
| PC-C LAN | 172.16.1.32/29 | 255.255.255.248 | 172.16.1.33 |

### Router-to-Router Connections

| Connection | Network Address |
|---|---|
| R1 - R2 | 192.168.10.0/30 |
| R1 - R3 | 192.168.10.4/30 |
| R2 - R3 | 192.168.10.8/30 |

## OSPF Dynamic Routing

OSPFv2 was configured across all three routers using Area 0. Dynamic routing allows routers to exchange network information and automatically calculate alternative routes when the network topology changes.

### OSPF Neighbor Verification

The OSPF neighbor table was examined to confirm that routers successfully established neighbor relationships in the FULL state.

![OSPF Neighbor Verification](screenshots/02_ospf_neighbors.png)

### Routing Table Verification

The routing table on R2 was examined to verify that the Finance network was successfully learned through OSPF.

![OSPF Routing Table](screenshots/03_ospf_routing_table.png)

## VLAN Segmentation and Inter-VLAN Routing

Two VLANs were configured to separate the Administration and Finance departments into different broadcast domains.

| VLAN ID | Department | Switch Access Port |
|---|---|---|
| 10 | Administration | Fa0/18 |
| 20 | Finance | Fa0/10 |

### VLAN Configuration

![VLAN Configuration](screenshots/04_vlan_configuration.png)

### 802.1Q Trunk Configuration

A trunk connection between switch S1 and router R1 was configured to carry traffic from VLANs 10 and 20 over a single physical connection.

![Trunk Verification](screenshots/05_trunk_verification.png)

### Router-on-a-Stick Configuration

Router subinterfaces were configured on R1 to provide separate default gateways and enable communication between the two departmental VLANs.

![Router Subinterfaces](screenshots/06_router_subinterfaces.png)

## IPv4 Connectivity Testing

Connectivity testing was performed using ICMP echo requests to verify that workstations could communicate across departmental VLANs and remote LANs.

### Inter-VLAN Connectivity

![Inter-VLAN Connectivity](screenshots/07_inter_vlan_connectivity.png)

### Remote LAN Connectivity - PC-B

![PC-B Connectivity](screenshots/08_pc_b_connectivity.png)

### Remote LAN Connectivity - PC-C

![PC-C Connectivity](screenshots/09_pc_c_connectivity.png)

All three documented connectivity tests returned four successful replies with 0% packet loss.

## Secure Remote Administration

SSH was configured on R1 to provide encrypted remote administration instead of using Telnet, which transmits information without encryption.

The configuration included:

- SSH version 2.
- RSA key generation.
- Local username and password authentication.
- SSH-only access on VTY lines.

### SSH Configuration

![SSH Configuration](screenshots/10_ssh_configuration.png)

### Remote Access Verification

A successful SSH connection was established from PC-A to R1 to verify that remote administration was functioning correctly.

![SSH Verification](screenshots/11_ssh_verification.png)

## Network Failover and Recovery

A controlled router link failure was simulated to evaluate how OSPF responds when an existing network path becomes unavailable.

The purpose of this test was to determine whether the redundant network topology could maintain connectivity through an alternative route after OSPF convergence.

### Phase 1: Baseline Connectivity

Before disabling the router interface, traceroute confirmed that traffic from PC-A to PC-B followed the original three-hop path.

![Baseline Traceroute](screenshots/12_baseline_traceroute.png)

### Phase 2: Simulated Link Failure

The connection between R1 and R2 was disabled to simulate a network link failure.

After OSPF convergence, traffic was redirected through R3 using an alternative four-hop path. Connectivity testing returned four successful replies with 0% packet loss.

![OSPF Failover](screenshots/13_ospf_failover.png)

### Phase 3: Network Recovery

The disabled interface was restored, allowing OSPF to recalculate the routing path.

Traceroute confirmed that traffic returned to the original three-hop path, and connectivity testing again returned four successful replies with 0% packet loss.

![Network Recovery](screenshots/14_network_recovery.png)

### Failover Test Results

| Test Phase | Routing Path | Traceroute Hops | Packet Loss |
|---|---|---:|---:|
| Normal Operation | R1 - R2 | 3 | 0% |
| Simulated Link Failure | R1 - R3 - R2 | 4 | 0% after convergence |
| Network Recovery | R1 - R2 | 3 | 0% after convergence |

## Troubleshooting Approach

Throughout the project, I used a structured troubleshooting approach to identify configuration and connectivity issues.

This included:

- Checking router and switch interface status.
- Reviewing IPv4 addresses and subnet masks.
- Verifying VLAN membership and access port assignments.
- Confirming trunk configuration and allowed VLANs.
- Examining OSPF neighbor relationships and routing tables.
- Testing workstation connectivity using ping.
- Comparing routing paths using traceroute.
- Verifying SSH configuration through a remote login.

These steps helped identify configuration issues and confirm that the network operated as intended.

## Security Considerations

SSH was implemented to protect remote administrative sessions and login credentials through encryption. Local authentication was configured to require valid credentials before accessing R1.

VLAN segmentation was used to separate departmental broadcast traffic. However, inter-VLAN routing still allows communication between the departments.

Additional security measures that could be implemented in a production environment include:

- Access control lists (ACLs) to restrict unnecessary network traffic.
- Dedicated management VLANs.
- Centralized authentication and access management.
- Network monitoring and centralized logging.
- Consistent SSH security configurations across network devices.

These measures represent potential future improvements and were not implemented as part of this project.

## Final Results

| Verification Test | Result |
|---|---|
| IPv4 Addressing and Connectivity | Successful |
| OSPF Neighbor Relationships | Successful |
| Dynamic Route Advertisement | Successful |
| VLAN Segmentation | Successful |
| 802.1Q Trunking | Successful |
| Inter-VLAN Routing | Successful |
| Remote LAN Connectivity | Successful |
| SSH Remote Administration on R1 | Successful |
| OSPF Failover | Successful |
| Network Recovery | Successful |

## Project Deliverables

- [Technical Project Report (PDF)](Enterprise_Network_Report.pdf)
- [Cisco Packet Tracer Project (.pkt)](Enterprise_Network.pkt)
- [Network Configuration and Testing Screenshots](screenshots/)

The technical report provides additional details about the network architecture, configuration decisions, security considerations, troubleshooting process, and testing results.

## Skills Demonstrated

- Enterprise Network Configuration
- Network Security
- IPv4 Addressing and Subnetting
- Dynamic Routing
- OSPF Configuration
- VLAN Segmentation
- Inter-VLAN Routing
- Secure Remote Administration
- Network Troubleshooting
- Network Failover and Recovery
- Technical Documentation

## Conclusion

This project strengthened my understanding of how routing, switching, and network security work together in an enterprise environment.

One of the most valuable parts of the project was observing how OSPF responded to a failed network connection and verifying that traffic returned to its original path once the connection was restored.

Completing the configuration and testing process also helped me develop practical experience with Cisco IOS, network troubleshooting, secure remote administration, and technical documentation.

