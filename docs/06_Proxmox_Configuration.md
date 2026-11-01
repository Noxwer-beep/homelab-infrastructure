# 06 - Proxmox Configuration

## 6.1 Network Interfaces Configuration
```
auto lo
iface lo inet loopback

// Management NIC
auto nic0
iface nic0 inet manual

// LAN NIC for OPNsense
auto enp10s0
iface enp10s0 inet manual

// vmbr0 -- Management + OPNsense WAN
auto vmbr0
iface vmbr0 inet static
    address <mgmt-subnet>/24
    gateway <home-router-ip>
    bridge-ports nic0
    bridge-stp off
    bridge-fd 0

// vmbr1 -- OPNsense LAN (no IP on host!)
auto vmbr1
iface vmbr1 inet manual
    bridge-ports enp10s0
    bridge-stp off
    bridge-fd 0
```
### 6.2 Proxmox Network Diagram

```mermaid
graph TD
    %% ===== Classes / Colors =====
    classDef host fill:#e3f2fd,stroke:#64b5f6,stroke-width:1px,color:#111;
    classDef fw fill:#f3e5f5,stroke:#ab47bc,stroke-width:1px,color:#111;
    classDef vm fill:#eeeeee,stroke:#bdbdbd,stroke-width:1px,color:#111;

    %% ===== Proxmox Host =====
    subgraph ProxmoxHost["Proxmox Host"]
        NIC0["Physical NIC0<br>VLAN1"]
        NIC10G["Physical 10G NIC<br>VLAN10"]

        vmbr0["vmbr0<br><mgmt-subnet>/24<br>GW <home-router-ip>"]
        vmbr1["vmbr1<br>no IP<br>LAN bridge"]

        NIC0 --> vmbr0
        NIC10G --> vmbr1
    end

    %% ===== OPNsense VM =====
    subgraph OPNsense["OPNsense firewall VM"]
        WAN["WAN vtnet0<br>DHCP via Fritzbox"]
        LAN["LAN vtnet1<br><lab-gateway>/24"]
    end

    vmbr0 --> WAN
    vmbr1 --> LAN

    %% ===== Homelab VMs =====
    subgraph Homelab["Homelab VMs / Container"]
        VM1["VM / LXC<br><lab-subnet>"]
        VM2["VM / LXC<br><lab-subnet>"]
    end

    LAN --> VM1
    LAN --> VM2

    class ProxmoxHost,NIC0,NIC10G,vmbr0,vmbr1 host;
    class OPNsense,WAN,LAN fw;
    class Homelab,VM1,VM2 vm;
```
## 6.3 Key Rules
- vmbr0 must NEVER lose its static IP (fail-safe management)
- vmbr1 must NEVER have an IP (isolated, routed by OPNsense)
- OPNsense interacts only with the vtnet virtual interfaces
- VLANs are handled physically by switch, not by Proxmox
<!-- pagebreak -->

---

> **Addressing redacted.** Exact IPs, the VLAN port map and the master PDF live in a private repository. This published version keeps the design reasoning and the configuration approach; any address shown as `<lab-subnet>`, `<home-lan>` or `<lab-gateway>` is a placeholder.
