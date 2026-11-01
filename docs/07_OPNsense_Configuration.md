# 07 - OPNsense Configuration

## 7.1 Interface Assignment
| vtnet      | Purpose | Connected to                 |
| ---------- | ------- | ---------------------------- |
| **vtnet0** | WAN     | vmbr0 (DHCP via Fritzbox)    |
| **vtnet1** | LAN     | vmbr1 (static <lab-gateway>/24) |

## 7.2 Initial Console Setup (Critical Steps)
##### WAN (vtnet0)
 - Configure IPv4: YES
 - Mode: DHCP
 - No static IPs
 - No IPv6
 - No gateway manually set

##### LAN (vtnet1)
 - Configure IPv4: YES
 - IP: <lab-gateway>
 - Subnet: /24
 - Enable DHCP server: YES
 - Range: <lab-host-a> – <lab-host-b>
 - Enable HTTPS: YES
 - Generate self-signed certificate: YES

## 7.3 Overview
```mermaid
flowchart TD
    %% ===== Classes / Colors =====
    classDef router fill:#ffe6cc,stroke:#e0a46b,stroke-width:1px,color:#333;
    classDef fw fill:#f3e5f5,stroke:#ab47bc,stroke-width:1px,color:#111;
    classDef client fill:#eeeeee,stroke:#bdbdbd,stroke-width:1px,color:#111;

    Fritz["Fritzbox Router<br><home-router-ip>"]
    WAN["OPNsense WAN vtnet0<br>DHCP <home-lan>.x"]
    FW["OPNsense Firewall<br>NAT + Rules"]
    LAN["OPNsense LAN vtnet1<br><lab-gateway>/24"]

    C1["Homelab Client 1<br><lab-subnet>"]
    C2["Homelab Client 2<br><lab-subnet>"]    
    Future["Future Services<br>NAS / DNS / Pentest Lab"]

    Fritz --> WAN
    WAN --> FW
    FW --> LAN

    LAN --> C1
    LAN --> C2
    LAN --> Future

    class Fritz router;
    class WAN,FW,LAN fw;
    class C1,C2,Future client;
```

<!-- pagebreak -->

---

> **Addressing redacted.** Exact IPs, the VLAN port map and the master PDF live in a private repository. This published version keeps the design reasoning and the configuration approach; any address shown as `<lab-subnet>`, `<home-lan>` or `<lab-gateway>` is a placeholder.
