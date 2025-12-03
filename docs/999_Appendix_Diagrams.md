# Appendix – Diagrams

This appendix contains all architectural and troubleshooting diagrams used throughout the documentation.  
They are consolidated here for quick reference and print-friendly output.

---

## A.1 High-Level Network Architecture

```mermaid
graph TD
    classDef router fill:#ffe6cc,stroke:#e0a46b,color:#333,stroke-width:1px;
    classDef switch fill:#fff9c4,stroke:#d4c34f,color:#333,stroke-width:1px;
    classDef server fill:#e3f2fd,stroke:#64b5f6,color:#111,stroke-width:1px;
    classDef fw fill:#f3e5f5,stroke:#ab47bc,color:#111,stroke-width:1px;
    classDef client fill:#eeeeee,stroke:#bdbdbd,color:#111,stroke-width:1px;

    Internet((Internet))

    Fritzbox["Fritzbox Router<br>192.168.178.1"]:::router
    DLAN["DLAN Link<br>(temporary uplink)"]:::router

    SG105E["TP-Link SG105E<br>Smart Managed"]:::switch
    SG105["TP-Link SG105<br>Unmanaged VLAN10"]:::switch

    ProxmoxNIC0["Proxmox NIC0<br>vmbr0 / VLAN1"]:::server
    Proxmox10G["Proxmox 10G NIC<br>vmbr1 / VLAN10"]:::server

    OPN_WAN["OPNsense WAN<br>vtnet0 (DHCP)"]:::fw
    OPN_LAN["OPNsense LAN<br>vtnet1 (10.10.10.1/24)"]:::fw

    HomelabClients["Homelab Clients<br>10.10.10.x/24"]:::client

    Internet --> Fritzbox
    Fritzbox --> DLAN
    DLAN --> SG105E

    SG105E -->|"Port 4<br>VLAN1 untagged"| ProxmoxNIC0
    SG105E -->|"Port 5<br>VLAN10 untagged"| Proxmox10G
    SG105E -->|"Port 3<br>VLAN10 untagged"| SG105
    SG105E -->|"Port 1<br>VLAN1 untagged"| Fritzbox

    ProxmoxNIC0 --> vmbr0["vmbr0<br>192.168.178.10/24"]:::server
    Proxmox10G --> vmbr1["vmbr1<br>LAN bridge<br>No IP"]:::server

    vmbr0 --> OPN_WAN
    vmbr1 --> OPN_LAN
    OPN_LAN --> HomelabClients
```

---

## A.2 Proxmox Virtual Network Layout

```mermaid
graph TD
    classDef host fill:#e3f2fd,stroke:#64b5f6,color:#111;
    classDef fw fill:#f3e5f5,stroke:#ab47bc,color:#111;
    classDef client fill:#eeeeee,stroke:#bdbdbd,color:#111;

    subgraph Proxmox["Proxmox Host"]
        NIC0["Physical NIC0<br>VLAN1"]:::host
        NIC10["Physical 10G NIC<br>VLAN10"]:::host
        vmbr0["vmbr0<br>192.168.178.10/24"]:::host
        vmbr1["vmbr1<br>No IP (LAN bridge)"]:::host
        NIC0 --> vmbr0
        NIC10 --> vmbr1
    end

    subgraph Firewall["OPNsense"]
        WAN["WAN vtnet0<br>DHCP from Fritzbox"]:::fw
        LAN["LAN vtnet1<br>10.10.10.1/24"]:::fw
    end

    vmbr0 --> WAN
    vmbr1 --> LAN

    Client1["VM / LXC<br>10.10.10.x"]:::client
    Client2["VM / LXC<br>10.10.10.x"]:::client

    LAN --> Client1
    LAN --> Client2
```

---

## A.3 Issue Overview Diagram

```mermaid
graph TD
    classDef ok fill:#e3f2fd,stroke:#64b5f6,color:#111;
    classDef warn fill:#fff9c4,stroke:#d4ba3c,color:#333;
    classDef error fill:#ffebee,stroke:#e57373,color:#b71c1c;
    classDef fix fill:#e8f5e9,stroke:#66bb6a,color:#1b5e20;

    A["RAM Voltage Mismatch"]:::error --> A_fix["Use JEDEC RAM only"]:::fix
    B["Wrong Proxmox IP"]:::error --> B_fix["Restore interfaces + IP plan"]:::fix
    C["WAN/LAN Swapped"]:::error --> C_fix["Correct vtnet mapping"]:::fix
    D["ISO Boot Failure"]:::warn --> D_fix["Switch to SeaBIOS firmware"]:::fix
    E["NIC Not Detected"]:::error --> E_fix["Move NIC to primary PCIe slot"]:::fix
    F["OPNsense Live Mode Loop"]:::warn --> F_fix["Remove ISO after install"]:::fix
    G["NVMe Read-Only (IOMMU)"]:::error --> G_fix["Disable IOMMU / use ZFS mirror"]:::fix
```

<!-- pagebreak -->
