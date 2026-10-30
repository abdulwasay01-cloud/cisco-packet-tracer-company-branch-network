# 🌐 Enterprise Branch Office Network Design & Implementation

<div align="center">

![Cisco Packet Tracer](https://img.shields.io/badge/Tool-Cisco%20Packet%20Tracer-blue?style=for-the-badge&logo=cisco)
![Networking](https://img.shields.io/badge/Domain-Enterprise%20Networking-green?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Completed%20%2F%20Tested-success?style=for-the-badge)

*A robust, secure, and scalable multi-VLAN enterprise branch network featuring automated IP allocation, wireless access points, and Inter-VLAN routing.*

</div>

---

## 📋 Table of Contents
- [Project Overview](#-project-overview)
- [Network Requirements & Topology](#-network-requirements--topology)
- [IP Addressing & Subnetting Table](#-ip-addressing--subnetting-table)
- [Step-by-Step Configuration Guide](#-step-by-step-configuration-guide)
  - [1. Switch VLAN Setup](#1-switch-vlan-setup)
  - [2. Router Sub-Interfaces & Inter-VLAN Routing](#2-router-sub-interfaces--inter-vlan-routing)
  - [3. DHCP Server Configuration](#3-dhcp-server-configuration)
- [Testing & Validation](#-testing--validation)
- [Author](#-author)

---

## 🏢 Project Overview
This project simulates a network branch implementation for **XYZ Company** in eastern Australia. The branch operates independently from the corporate headquarters and accommodates three core departments: **Admin / IT**, **Finance / HR**, and **Customer Service / Reception**. 

The architecture guarantees department segregation via VLANs while permitting secure inter-departmental communication through a single Cisco router via **Router-on-a-Stick (Inter-VLAN routing)** and dynamic IP assignment via **DHCP**.

---

## ⚙️ Network Requirements & Topology

* **Hardware Components:** 1x Cisco 2911 Router, 1x Cisco Switch, 3x Wireless Access Points, and multiple end-hosts (PCs, Printers, Laptops, Smartphones, Tablets).
* **VLAN Segmentation:**
  * **VLAN 10** – Admin / IT Department (`192.168.1.0/26`)
  * **VLAN 20** – Finance / HR Department (`192.168.1.64/26`)
  * **VLAN 30** – Customer Service & Reception (`192.168.1.128/26`)
* **Wireless Integration:** Dedicated SSIDs configured on WAPs for each department with WPA2-PSK security.
* **Services:** Centralized Router DHCP pools and Inter-VLAN routing enabled via dot1q encapsulation.

---

## 🔢 IP Addressing & Subnetting Table

Given Base Network: `192.168.1.0/24` with 3 subnets required.
* **Borrowed Bits ($2^n \ge 3$):** 2 bits borrowed $\rightarrow$ **New Subnet Mask:** `255.255.255.192` (`/26`)
* **Block Size:** 64 addresses per subnet.

| Department / VLAN | VLAN ID | Network ID | Subnet Mask | Usable Host Range | Default Gateway |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Admin / IT** | VLAN 10 | `192.168.1.0` | `255.255.255.192` | `192.168.1.1` - `192.168.1.62` | `192.168.1.1` |
| **Finance / HR** | VLAN 20 | `192.168.1.64` | `255.255.255.192` | `192.168.1.65` - `192.168.1.126` | `192.168.1.65` |
| **Customer Service** | VLAN 30 | `192.168.1.128` | `255.255.255.192` | `192.168.1.129` - `192.168.1.190` | `192.168.1.129` |

---

## 🛠️ Step-by-Step Configuration Guide

### 1. Switch VLAN Setup
Configure access ports and trunk ports on the switch:
```text
enable
configure terminal

! Configure Access Ports for VLANs
interface range fastEthernet 0/1 - 3
 switchport mode access
 switchport access vlan 10

interface range fastEthernet 0/5 - 7
 switchport mode access
 switchport access vlan 20

interface range fastEthernet 0/8 - 10
 switchport mode access
 switchport access vlan 30

! Configure Trunk Port connecting to the Router
interface fastEthernet 0/1
 switchport mode trunk
end
