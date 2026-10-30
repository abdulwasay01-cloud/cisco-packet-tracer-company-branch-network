# 🌐 Enterprise Branch Office Network Design & Implementation

<div align="center">

![Cisco Packet Tracer](https://img.shields.io/badge/Tool-Cisco%20Packet%20Tracer-blue?style=for-the-badge\&logo=cisco)

![Networking](https://img.shields.io/badge/Domain-Enterprise%20Networking-green?style=for-the-badge)

![License](https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge)

![Status](https://img.shields.io/badge/Status-Completed%20%2F%20Tested-success?style=for-the-badge)

**A robust, secure, and scalable multi-VLAN enterprise branch network featuring automated IP allocation, wireless access points, and Inter-VLAN routing.**

</div>

---

## 📋 Table of Contents

1. [Project Overview](#1-project-overview)
2. [Architecture & Design Concepts](#2-architecture--design-concepts)
3. [IP Addressing & Subnetting Guide](#3-ip-addressing--subnetting-guide)
4. [Step-by-Step Configuration Guide](#4-step-by-step-configuration-guide)

   * [Switch VLAN Setup](#switch-vlan-setup)
   * [Router Sub-Interfaces & Inter-VLAN Routing](#router-sub-interfaces--inter-vlan-routing)
   * [DHCP Server Configuration](#dhcp-server-configuration)
5. [How to Download & Test](#5-how-to-download--test)

---

# 1. Project Overview

## Explanation & Context

This project implements a simulated enterprise network for **XYZ Company's branch office**, designed to operate independently from the main corporate headquarters.

In modern networking, branch offices require efficient traffic management, network segmentation, and secure communication between different departments.

### Business Requirements

The branch office requires:

* One core router.
* One managed Layer 2 switch.
* Wireless connectivity for mobile users.
* Automated IP address allocation using DHCP.
* Network segmentation across three departments.

### Department Segmentation

The network is divided into three departments:

1. **Admin / IT Department** — VLAN 10
2. **Finance / HR Department** — VLAN 20
3. **Customer Service & Reception** — VLAN 30

### Operational Goals

* Automatically assign IP configurations to connected devices using DHCP.
* Separate departmental traffic using VLANs.
* Enable communication between different VLANs through Inter-VLAN routing.
* Provide wireless connectivity through Wireless Access Points.
* Improve network organization, scalability, and security.

---

# 2. Architecture & Design Concepts

To build a scalable and manageable enterprise network, the following networking concepts were implemented.

## 2.1 VLAN Segmentation (Virtual Local Area Networks)

By default, switch ports belong to VLAN 1, placing connected devices within the same broadcast domain.

VLANs allow us to logically divide a physical network into multiple isolated broadcast domains.

In this project, three VLANs are configured:

| Department       | VLAN ID |
| ---------------- | ------: |
| Admin / IT       |      10 |
| Finance / HR     |      20 |
| Customer Service |      30 |

### Benefits of VLANs

* Reduce unnecessary broadcast traffic.
* Separate departmental network traffic.
* Improve network organization.
* Provide a foundation for implementing access control policies.
* Improve network scalability.

## 2.2 Router-on-a-Stick (Inter-VLAN Routing)

Devices belonging to different VLANs cannot communicate directly at Layer 2.

To enable communication between VLANs, a router is configured to perform Inter-VLAN routing.

This project uses the **Router-on-a-Stick** method.

Instead of using separate physical router interfaces for each VLAN, a single physical router interface is divided into multiple logical sub-interfaces.

Each sub-interface is configured with:

* IEEE 802.1Q encapsulation.
* A VLAN ID.
* An IP address that acts as the default gateway for that VLAN.

The physical connection between the router and switch operates as a trunk link, carrying traffic from multiple VLANs.

**Physical Interface:** GigabitEthernet0/0

**Sub-Interfaces:**

* GigabitEthernet0/0.10 — VLAN 10
* GigabitEthernet0/0.20 — VLAN 20
* GigabitEthernet0/0.30 — VLAN 30

## 2.3 Centralized DHCP Server

Manual IP configuration becomes difficult to manage when an enterprise network contains multiple devices.

To simplify IP address management, the Cisco router is configured as a centralized DHCP server.

Three separate DHCP pools are created, one for each VLAN.

Each pool provides:

* IP address.
* Subnet mask.
* Default gateway.
* DNS server information.
* Domain name.

This allows wired and wireless clients to receive their network configurations automatically.

## 2.4 Wireless Access Point (WAP) Integration

Enterprise networks require wireless connectivity for laptops, smartphones, and other mobile devices.

Wireless Access Points provide connectivity between wireless clients and the wired network.

In this project:

* Wireless Access Points provide wireless connectivity.
* Each department can have its own wireless network.
* SSIDs identify the wireless networks.
* WPA2-PSK provides wireless authentication and encryption.
* Access Point switch ports are assigned to their respective VLANs.

This allows wireless clients to become part of the appropriate departmental network.

---

# 3. IP Addressing & Subnetting Guide

## Explanation & Mathematical Breakdown

The project uses the private IPv4 address block:

`192.168.1.0/24`

Since the network contains three departments, the original network must be divided into smaller subnets.

### 3.1 Subnetting Calculation

We need at least three separate subnets.

The subnetting formula is:

`2^n >= Number of Required Subnets`

Where `n` represents the number of borrowed bits.

Calculation:

* Borrowing 1 bit: 2¹ = 2 subnets (Insufficient).
* Borrowing 2 bits: 2² = 4 subnets (Sufficient).

Therefore, we borrow two bits from the host portion.

Original prefix:

`/24`

New prefix:

`/26`

### 3.2 Subnet Mask Calculation

The new subnet mask is:

`255.255.255.192`

Binary representation of the last octet:

`11000000`

Block size calculation:

`256 - 192 = 64`

Therefore, the subnet addresses increment by 64.

The four available subnets are:

1. 192.168.1.0/26
2. 192.168.1.64/26
3. 192.168.1.128/26
4. 192.168.1.192/26

We use the first three subnets for the departments.

### 3.3 IP Addressing Table

| Department       | VLAN ID | Network Address | Subnet Mask     | Usable Host Range             | Default Gateway |
| ---------------- | ------: | --------------- | --------------- | ----------------------------- | --------------- |
| Admin / IT       |      10 | 192.168.1.0     | 255.255.255.192 | 192.168.1.1 – 192.168.1.62    | 192.168.1.1     |
| Finance / HR     |      20 | 192.168.1.64    | 255.255.255.192 | 192.168.1.65 – 192.168.1.126  | 192.168.1.65    |
| Customer Service |      30 | 192.168.1.128   | 255.255.255.192 | 192.168.1.129 – 192.168.1.190 | 192.168.1.129   |

**Important:**

Each /26 subnet contains:

* 64 total IP addresses.
* 1 network address.
* 1 broadcast address.
* 62 usable host addresses.

The router uses the first usable IP address in each subnet as its default gateway.

---

# 4. Step-by-Step Configuration Guide

## 4.1 Switch VLAN Setup

The managed switch is configured to separate the three departments into different VLANs.

Specific switch ports are assigned to their respective VLANs.

A trunk port is configured to carry traffic from all three VLANs to the router.

### VLAN and Port Allocation

| Switch Interface |    VLAN ID | Department        | Port Type |
| ---------------- | ---------: | ----------------- | --------- |
| Fa0/2 – Fa0/4    |         10 | Admin / IT        | Access    |
| Fa0/5 – Fa0/7    |         20 | Finance / HR      | Access    |
| Fa0/8 – Fa0/10   |         30 | Customer Service  | Access    |
| Fa0/1            | 10, 20, 30 | Router Connection | Trunk     |

### Configuration Commands

```cisco
enable
configure terminal

<<<<<<< HEAD
! Configure Access Ports for VLANs
interface range fastEthernet 0/1 - 3
=======
! Create VLANs
vlan 10
 name ADMIN_IT
exit

vlan 20
 name FINANCE_HR
exit

vlan 30
 name CUSTOMER_SERVICE
exit

! Configure Access Ports for VLAN 10
interface range fastEthernet 0/1 - 3
 switchport mode access
 switchport access vlan 10
exit

! Configure Access Ports for VLAN 20
interface range fastEthernet 0/5 - 7
 switchport mode access
 switchport access vlan 20
exit

! Configure Access Ports for VLAN 30
interface range fastEthernet 0/8 - 10
 switchport mode access
 switchport access vlan 30
exit

! Configure Trunk Port to Router
interface fastEthernet 0/4
 switchport mode trunk
<<<<<<< HEAD
end
=======
 no shutdown
exit

end
write memory
```

### Verification Commands

```cisco
show vlan brief
show interfaces trunk
show running-config
```

**Expected Result:**

* VLAN 10, VLAN 20, and VLAN 30 should exist.
* Access ports should belong to their respective VLANs.
* Fa0/1 should operate as a trunk port.

---

## 4.2 Router Sub-Interfaces & Inter-VLAN Routing

The router is configured using the Router-on-a-Stick method.

A single physical interface is divided into three logical sub-interfaces.

Each sub-interface receives an IP address that serves as the default gateway for its corresponding VLAN.

### Configuration Commands

```cisco
enable
configure terminal

! Enable Physical Interface
interface gigabitEthernet 0/0
 no shutdown
exit

! VLAN 10 - Admin / IT
interface gigabitEthernet 0/0.10
 encapsulation dot1Q 10
 ip address 192.168.1.1 255.255.255.192
exit

! VLAN 20 - Finance / HR
interface gigabitEthernet 0/0.20
 encapsulation dot1Q 20
 ip address 192.168.1.65 255.255.255.192
exit

! VLAN 30 - Customer Service
interface gigabitEthernet 0/0.30
 encapsulation dot1Q 30
 ip address 192.168.1.129 255.255.255.192
exit

end
write memory
```

### Understanding the Configuration

**Physical Interface:**

`interface gigabitEthernet 0/0`

Activates the physical router interface connected to the switch.

**Sub-Interface:**

`interface gigabitEthernet 0/0.10`

Creates a logical sub-interface for VLAN 10.

**802.1Q Encapsulation:**

`encapsulation dot1Q 10`

Identifies incoming tagged frames belonging to VLAN 10.

**IP Address:**

`ip address 192.168.1.1 255.255.255.192`

Assigns the default gateway address to VLAN 10.

The same process is repeated for VLAN 20 and VLAN 30.

### Verification Commands

```cisco
show ip interface brief
show running-config
show ip route
```

**Expected Result:**

All three sub-interfaces should be operational when the physical link and trunk configuration are correct.

The routing table should contain three directly connected networks.

---

## 4.3 DHCP Server Configuration

The Cisco router also operates as a centralized DHCP server.

Separate DHCP pools are configured for the three VLANs.

This allows clients to obtain IP addresses automatically without requiring manual configuration.

### Step 1: Exclude Default Gateway Addresses

The default gateway addresses should not be assigned to end devices.

```cisco
enable
configure terminal

ip dhcp excluded-address 192.168.1.1
ip dhcp excluded-address 192.168.1.65
ip dhcp excluded-address 192.168.1.129
```

### Step 2: Create DHCP Pool for Admin / IT

```cisco
ip dhcp pool ADMIN_POOL
 network 192.168.1.0 255.255.255.192
 default-router 192.168.1.1
 dns-server 8.8.8.8
 domain-name admin.com
exit
```

### Step 3: Create DHCP Pool for Finance / HR

```cisco
ip dhcp pool FINANCE_POOL
 network 192.168.1.64 255.255.255.192
 default-router 192.168.1.65
 dns-server 8.8.8.8
 domain-name finance.com
exit
```

### Step 4: Create DHCP Pool for Customer Service

```cisco
ip dhcp pool CS_POOL
 network 192.168.1.128 255.255.255.192
 default-router 192.168.1.129
 dns-server 8.8.8.8
 domain-name cs.com
exit

end
write memory
```

### Understanding DHCP Configuration

| Command                    | Purpose                                                      |
| -------------------------- | ------------------------------------------------------------ |
| `ip dhcp excluded-address` | Prevents specified addresses from being assigned to clients. |
| `ip dhcp pool`             | Creates a DHCP address pool.                                 |
| `network`                  | Defines the subnet from which addresses are assigned.        |
| `default-router`           | Provides the default gateway to clients.                     |
| `dns-server`               | Provides the DNS server address.                             |
| `domain-name`              | Specifies the domain name for DHCP clients.                  |

**Note:** The DNS server is configured as `8.8.8.8` (Google Public DNS). Internet-based DNS resolution requires appropriate upstream connectivity and routing.

### DHCP Verification Commands

```cisco
show ip dhcp pool
show ip dhcp binding
show ip dhcp server statistics
```

**Expected Result:**

* Three DHCP pools should be available.
* Clients should receive IP addresses from their respective VLAN subnets.
* Each client should receive the correct default gateway.
* DHCP bindings should display assigned IP addresses and client information.

---

# 5. How to Download & Test

## Explanation & Verification Steps

Follow these steps to download the project and verify its functionality in Cisco Packet Tracer.

### Step 1: Download the Project

1. Open this GitHub repository.
2. Click the green **Code** button.
3. Select **Download ZIP**.
4. Extract the downloaded ZIP file.

### Step 2: Open in Cisco Packet Tracer

1. Launch Cisco Packet Tracer.
2. Navigate to **File → Open**.
3. Select the downloaded `.pkt` project file.
4. Wait for the network topology to load.

### Step 3: Test DHCP Assignment

1. Select any PC or laptop.
2. Open the device configuration.
3. Navigate to **Desktop → IP Configuration**.
4. Select **DHCP**.
5. Verify that the device receives:

   * IP address.
   * Subnet mask.
   * Default gateway.
   * DNS server.

Check whether the assigned IP address belongs to the correct departmental subnet.

### Step 4: Test Inter-VLAN Routing

Open the command prompt on a device in VLAN 10.

Ping a device located in VLAN 20 or VLAN 30.

Example:

```bash
ping 192.168.1.130
```

A successful ping confirms that communication between the different VLANs is operational, provided the destination device is configured and reachable.

### Step 5: Test Wireless Connectivity

1. Connect a wireless client to the appropriate SSID.
2. Enter the WPA2-PSK authentication password.
3. Verify wireless connectivity.
4. Check whether the client receives an IP address from the correct DHCP pool.
5. Test communication with other devices.

### Step 6: Final Network Verification

Use the following commands on the router:

```cisco
show ip interface brief
show ip route
show ip dhcp binding
show ip dhcp pool
show running-config
```

Use the following commands on the switch:

```cisco
show vlan brief
show interfaces trunk
show mac address-table
```

---

# 📌 Project Summary

This project demonstrates the implementation of a simulated enterprise branch office network using Cisco Packet Tracer.

### Technologies & Networking Concepts Implemented

* Cisco Packet Tracer
* IPv4 Addressing
* Subnetting (/26)
* VLAN Configuration
* Access Port Configuration
* Trunk Port Configuration
* IEEE 802.1Q Encapsulation
* Router-on-a-Stick
* Inter-VLAN Routing
* DHCP Server Configuration
* Wireless Access Point Integration
* WPA2-PSK Wireless Security
* Network Verification and Troubleshooting

### Key Learning Outcomes

Through this project, the following practical networking skills were demonstrated:

1. Designing an enterprise network topology.
2. Dividing a network into multiple subnets.
3. Implementing departmental network segmentation using VLANs.
4. Configuring trunk links between switches and routers.
5. Implementing Inter-VLAN routing using router sub-interfaces.
6. Configuring a centralized DHCP server.
7. Integrating wireless devices into departmental networks.
8. Verifying network connectivity and troubleshooting configuration issues.

---

**Author:** Mohammed Abdul Wasay

**Domain:** Enterprise Networking

**Tool:** Cisco Packet Tracer

**Project Type:** Network Design & Implementation