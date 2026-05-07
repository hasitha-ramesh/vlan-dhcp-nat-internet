# 🌐 Project 6: VLAN + DHCP + NAT + Internet Simulation

## 📌 Overview

This project demonstrates how to build a complete enterprise-style network using:

* VLAN segmentation
* Inter-VLAN Routing (Router-on-a-Stick)
* DHCP (automatic IP assignment)
* NAT/PAT (internet access)
* Internet simulation using ISP router

The network simulates real-world departmental communication with internet connectivity.

---

## 🎯 Objectives

* Segment network using VLANs
* Enable communication between VLANs
* Automatically assign IP addresses using DHCP
* Configure NAT/PAT for internet access
* Simulate internet connectivity using ISP router and server

---

# 🏗️ Network Design

| VLAN | Department | Network         | Gateway      |
| ---- | ---------- | --------------- | ------------ |
| 10   | SALES      | 192.168.10.0/24 | 192.168.10.1 |
| 20   | HR         | 192.168.20.0/24 | 192.168.20.1 |

---

# 🌐 ISP Network

| Device | Interface | IP Address |
| ------- | --------- | ---------- |
| Router0 | G0/1 | 10.0.0.2 |
| Router1 (ISP) | G0/0 | 10.0.0.1 |
| Router1 (ISP) | G0/1 | 8.8.8.1 |
| Server | Fa0 | 8.8.8.8 |

---

# ⚙️ Configuration

---

## 🔹 1. VLAN Configuration (Switch)

Create VLANs:

```bash
enable
configure terminal

vlan 10
name SALES

vlan 20
name HR

🔹 2. Assign Ports to VLANs
interface fa0/1
switchport mode access
switchport access vlan 10

interface fa0/2
switchport mode access
switchport access vlan 20

👉 Each PC is assigned to its department VLAN.

🔹 3. Configure Trunk Port
interface fa0/24
switchport mode trunk

👉 Allows multiple VLANs to travel between switch and router.

🔹 4. Inter-VLAN Routing (Router-on-a-Stick)

Enable interface:

interface g0/0
no shutdown
Configure Subinterfaces
interface g0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0

interface g0/0.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0

👉 Router acts as gateway for each VLAN.

🔹 5. DHCP Configuration

Exclude gateway IPs:

ip dhcp excluded-address 192.168.10.1
ip dhcp excluded-address 192.168.20.1
VLAN 10 DHCP
ip dhcp pool VLAN10
network 192.168.10.0 255.255.255.0
default-router 192.168.10.1
dns-server 8.8.8.8
VLAN 20 DHCP
ip dhcp pool VLAN20
network 192.168.20.0 255.255.255.0
default-router 192.168.20.1
dns-server 8.8.8.8

👉 PCs automatically receive IP addresses.

🔹 6. Configure ISP Connection
Router0
interface g0/1
ip address 10.0.0.2 255.255.255.252
no shutdown
Router1 (ISP)
enable
configure terminal

interface g0/0
ip address 10.0.0.1 255.255.255.252
no shutdown

interface g0/1
ip address 8.8.8.1 255.255.255.0
no shutdown

👉 Simulates ISP and internet connection.

🔹 7. NAT/PAT Configuration

Mark inside interface:

interface g0/0
ip nat inside

Mark outside interface:

interface g0/1
ip nat outside

Create ACL for NAT:

access-list 1 permit 192.168.10.0 0.0.0.255
access-list 1 permit 192.168.20.0 0.0.0.255

Enable PAT:

ip nat inside source list 1 interface g0/1 overload

👉 Allows multiple devices to share one public IP address.

🔹 8. Default Routing
Router0
ip route 0.0.0.0 0.0.0.0 10.0.0.1
Router1
ip route 192.168.10.0 255.255.255.0 10.0.0.2
ip route 192.168.20.0 255.255.255.0 10.0.0.2

👉 Enables internet communication.

🧪 Testing
🔹 Verify DHCP
ipconfig
🔹 Test Connectivity
ping 192.168.20.1
ping 10.0.0.1
ping 8.8.8.8
✅ Expected Results
Test	Result
VLAN Communication	✅ Success
Router Communication	✅ Success
ISP Communication	✅ Success
Internet Simulation	✅ Success
NAT Translation	✅ Success
📸 Screenshots
🔹 Network Topology

🔹 VLAN Configuration

🔹 DHCP Configuration

🔹 NAT Configuration

🔹 Successful Internet Ping

🚀 Future Improvements
ACL security implementation
Firewall integration
Dynamic routing protocols (OSPF/RIP)
Layer 3 switch implementation
📂 Project Files
project6-vlan-dhcp-nat-internet.pkt
/images
README.md
👨‍💻 Author

Hasitha Ramesh

GitHub: https://github.com/hasitha-ramesh