# Cisco Packet Tracer - Configure Secure Router Access and Interfaces

This repository contains the complete physical topology cabling, device configuration commands, dual-stack (IPv4 & IPv6) addressing, and reflection questions for the **Configure Secure Router Access and Interfaces** Cisco Packet Tracer physical rack lab.

---

## 🌐 Physical Topology & Setup

The activity involves setting up hardware devices on a physical rack and table in Packet Tracer:
* **Cisco 4321 ISR Router (R1)** placed in the Rack.
* **Cisco 2960 Switch** placed in the Rack.
* **Server** placed in the Rack.
* **PC-A** placed on the Table.
* **Console Connection:** DB-9 to RJ-45 console cable connected from PC-A Serial port to R1 Console port.

---

## 📋 Addressing Table

| Device | Interface | IPv4 Address / Prefix | IPv6 Address / Prefix | Link-Local Address | Default Gateway |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **R1** | `GigabitEthernet0/0/0` | `192.168.0.1 /24` | `2001:db8:acad::1 /64` | `fe80::1` | N/A |
| **R1** | `GigabitEthernet0/0/1` | `192.168.1.1 /24` | `2001:db8:acad:1::1 /64` | `fe80::1` | N/A |
| **R1** | `Loopback0` | `10.0.0.1 /24` | `2001:db8:acad:2::1 /64` | `fe80::1` | N/A |
| **PC-A** | NIC | `192.168.1.10 /24` | `2001:db8:acad:1::10 /64` | Manual Link-Local | `192.168.1.1` / `fe80::1` |
| **Server** | NIC | `192.168.0.10 /24` | `2001:db8:acad::10 /64` | Manual Link-Local | `192.168.0.1` / `fe80::1` |

---

## ⚙️ Router R1 Configuration Commands

```text
enable
configure terminal

! 1. Basic Security & Passwords
hostname R1
ip domain-name ccna-lab.com
service password-encryption
security passwords min-length 12
enable secret $cisco!PRIV*
banner motd # Unauthorized access is strictly prohibited! #

! 2. SSH User Setup & Key Generation
username SSHadmin secret 55Hadm!n2020
crypto key generate rsa modulus 1024

! 3. Line Hardening (Console & VTY)
line console 0
 password $cisco!!CON*
 exec-timeout 4 0
 login
exit

line vty 0 4
 password $cisco!!VTY*
 exec-timeout 4 0
 transport input ssh
 login local
exit

! 4. Login Block Security Policy (3 failed attempts within 60s -> block 120s)
login block-for 120 attempts 3 within 60

! 5. Enable IPv6 Unicast Routing
ipv6 unicast-routing

! 6. Interface Configurations
interface GigabitEthernet0/0/0
 description Connection to Server LAN
 ip address 192.168.0.1 255.255.255.0
 ipv6 address 2001:db8:acad::1/64
 ipv6 address fe80::1 link-local
 no shutdown
exit

interface GigabitEthernet0/0/1
 description Connection to PC-A LAN
 ip address 192.168.1.1 255.255.255.0
 ipv6 address 2001:db8:acad:1::1/64
 ipv6 address fe80::1 link-local
 no shutdown
exit

interface Loopback0
 description Management Loopback
 ip address 10.0.0.1 255.255.255.0
 ipv6 address 2001:db8:acad:2::1/64
 ipv6 address fe80::1 link-local
 no shutdown
exit

! 7. Save Configuration
end
copy running-config startup-config
```

---
