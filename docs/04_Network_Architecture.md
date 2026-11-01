# 04 — Network Architecture

## 4.1 High-Level Design Diagram
```mermaid
graph TD
    %% ===== Classes / Colors =====
    classDef router fill:#ffe6cc,stroke:#e0a46b,stroke-width:1px,color:#333;
    classDef switch fill:#fff9c4,stroke:#d4c34f,stroke-width:1px,color:#333;
    classDef server fill:#e3f2fd,stroke:#64b5f6,stroke-width:1px,color:#111;
    classDef fw fill:#f3e5f5,stroke:#ab47bc,stroke-width:1px,color:#111;
    classDef client fill:#eeeeee,stroke:#bdbdbd,stroke-width:1px,color:#111;

    %% ===== Nodes =====
    Internet((Internet))

    Fritzbox["Fritzbox Router<br><home-router-ip>"]
    DLAN["DLAN Link<br>(temporary uplink)"]

    SG105E["TP-Link SG105E<br>Smart Managed Switch"]
    SG105["TP-Link SG105<br>Unmanaged<br>VLAN10 LAN"]

    ProxmoxNIC0["Proxmox NIC0<br>vmbr0 / VLAN1"]
    Proxmox10G["Proxmox 10G NIC<br>vmbr1 / VLAN10"]

    OPN_WAN["OPNsense WAN<br>vtnet0 (DHCP)"]
    OPN_LAN["OPNsense LAN<br>vtnet1 (<lab-gateway>/24)"]

    HomelabClients["Homelab Clients<br><lab-subnet>/24"]

    %% ===== Connections =====
    Internet --> Fritzbox
    Fritzbox --> DLAN
    DLAN --> SG105E

    SG105E -->|"Port 4<br>VLAN1 untagged"| ProxmoxNIC0
    SG105E -->|"Port 5<br>VLAN10 untagged"| Proxmox10G
    SG105E -->|"Port 3<br>VLAN10 untagged"| SG105
    SG105E -->|"Port 1<br>VLAN1 untagged"| Fritzbox

    ProxmoxNIC0 --> vmbr0["vmbr0<br><mgmt-subnet>/24"]
    Proxmox10G --> vmbr1["vmbr1<br>LAN bridge<br>no IP"]

    vmbr0 --> OPN_WAN
    vmbr1 --> OPN_LAN

    OPN_LAN --> HomelabClients

    %% ===== Class assignments =====
    class Fritzbox,DLAN router;
    class SG105E,SG105 switch;
    class ProxmoxNIC0,Proxmox10G,vmbr0,vmbr1 server;
    class OPN_WAN,OPN_LAN fw;
    class HomelabClients client;
```
<!-- pagebreak -->

---

> **Addressing redacted.** Exact IPs, the VLAN port map and the master PDF live in a private repository. This published version keeps the design reasoning and the configuration approach; any address shown as `<lab-subnet>`, `<home-lan>` or `<lab-gateway>` is a placeholder.

## 4.2 What actually runs

The diagram above is the design. This is what is plugged in and answering today.

| Host | Role | Segment | Notes |
|---|---|---|---|
| OPNsense | firewall + router + DHCP + NTP | `<lab-gateway>` | only 80 (redirect) and 443 (WebGUI) exposed |
| Proxmox node | hypervisor | VLAN1 management | hosts the VMs below |
| Services VM | the websites, Postgres, the tooling | `<lab-subnet>` | nginx, containers, loopback-only database |
| **NAS** | file storage and backup target | home LAN | 2×4TB WD Red in RAIDZ1, SMB + NFS shares |
| **Pentest box** | deliberately vulnerable practice target | `<lab-subnet>` (isolated) | Metasploitable 2 — see below |

## 4.3 Why the vulnerable box lives there

Metasploitable 2 is a machine that is *supposed* to be broken: telnet, rsh, an
unauthenticated VNC, Samba 3, an Apache from 2008. Keeping it useful means keeping it
away from everything that matters, without making it awkward to use.

Three rules do the work:

1. **It sits on the lab subnet, not the home LAN.** Its only route out is through the
   firewall, and the firewall only allows what a scan needs.
2. **Nothing on the LAN may initiate a connection to it.** Any traffic from the lab
   towards the home network is denied by default, so a box that gets owned stays owned
   in one place.
3. **It is never a target from the internet.** No port forwarding, ever — the whole
   point is to practise on it while it is unreachable to anyone else.

The NAS is the opposite case: it holds the backups, so it lives on the home LAN, is
reachable from the lab only through an explicit rule, and is never exposed. The
segmentation is what lets a broken machine and the backup of everything else exist on
the same switch.

Inside the lab subnet everything still uses version detection, TLS and a real scan
policy. A target that answers "everything is open" teaches nothing about triage — see
[[Vantage]] for how the findings are graded.
