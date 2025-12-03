# 05 - Switch and VLAN Configuration

## 5.1 VLAN Plan
| VLAN                      | ID | Purpose                                                    |
| ------------------------- | -- | ---------------------------------------------------------- |
| **Default LAN**           | 1  | Home network from Fritzbox (management + WAN for OPNsense) |
| **Homelab / Pentest LAN** | 10 | OPNsense LAN + isolated virtual network                    |

## 5.2 SG105E VLAN Implementation
Untagged VLANs on Physical Ports

SG105E does not support hybrid tagging per enterprise-grade standards; instead it uses PVID-based untagging.
| Port | PVID    | VLAN Membership | Mode                      |
| ---- | ------- | --------------- | ------------------------- |
| 1    | 1       | VLAN1 untagged  | WAN uplink                |
| 2    | 1 or 10 | admin access    | variable                  |
| 3    | 10      | VLAN10 untagged | uplink to unmanaged SG105 |
| 4    | 1       | VLAN1 untagged  | Proxmox management        |
| 5    | 10      | VLAN10 untagged | Proxmox 10G NIC           |

## 5.3 Port Plan
```mermaid
graph TD
    %% ===== Classes / Colors =====
    classDef subgraphBg fill:#fff7a8,stroke:#d4ba3c,stroke-width:1px,color:#333;
    classDef port fill:#fffef2,stroke:#d4ba3c,stroke-width:1px,color:#333;
    classDef device fill:#e3f2fd,stroke:#64b5f6,stroke-width:1px,color:#111;
    classDef label fill:#ffffff,stroke:none,color:#111,font-weight:bold;

    %% ===== Label über dem Switch =====
    SG105ELabel["TP-Link SG105E smart managed switch"]
    class SG105ELabel label

    %% ===== SG105E Subgraph (ohne sichtbaren Titel) =====
    subgraph SG105E[" "]
        class SG105E subgraphBg

        P1["Port 1<br>PVID 1<br>VLAN1 untagged"]
        P2["Port 2<br>PVID 1 oder 10<br>Admin-Port"]
        P3["Port 3<br>PVID 10<br>VLAN10 untagged"]
        P4["Port 4<br>PVID 1<br>VLAN1 untagged"]
        P5["Port 5<br>PVID 10<br>VLAN10 untagged"]

        class P1,P2,P3,P4,P5 port
    end

    %% Label mit Subgraph verbinden (nur fürs Layout)
    SG105ELabel --> SG105E

    %% ===== Externe Geräte =====
    Fritzbox["Fritzbox Router"]
    AdminDevice["Admin-Laptop / MacBook"]
    SG105["TP-Link SG105 unmanaged<br>(VLAN10 LAN)"]
    ProxmoxNIC0["Proxmox NIC0<br>Management"]
    Proxmox10G["Proxmox 10G NIC<br>Homelab"]

    %% ===== Links =====
    Fritzbox --> P1
    AdminDevice --> P2
    SG105 --> P3
    ProxmoxNIC0 --> P4
    Proxmox10G --> P5

    class Fritzbox,AdminDevice,SG105,ProxmoxNIC0,Proxmox10G device
```

<!-- pagebreak -->