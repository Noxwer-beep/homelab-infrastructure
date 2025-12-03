  
<div style="text-align: center; margin-top: 90px;">
  
# **Homelab Infrastructure Documentation**
### **Version 1.1.0**
#### **Technical Architecture & Operational Overview**
  
<br>
  
## **Author**
**Maximilian Jatzkowski**
  
## **Last Updated**
**2025-12-03**
  
<br>
  
---
  
## **Document Control**
  
| Version | Date       | Author                  | Notes                                               |
|--------|------------|-------------------------|-----------------------------------------------------|
| 1.0.0  | 2025-12-02 | Maximilian Jatzkowski   | Initial release of Homelab project documentation   |
| 1.1.0  | 2025-12-03 | Maximilian Jatzkowski   | Restructured chapters, roadmap, issues and lessons |
  
---
  
## **Copyright Notice**
  
This document and all information contained herein are the property of the author and may not be reproduced, distributed, or used for any commercial purpose without explicit permission.
  
Unauthorized distribution is prohibited.
  
---
</div>
  
<div class="pagebreak"> </div>
  
# Table of Content
  
  
  
- [**Homelab Infrastructure Documentation**](#homelab-infrastructure-documentation )
    - [**Version 1.1.0**](#version-110 )
      - [**Technical Architecture & Operational Overview**](#technical-architecture--operational-overview )
  - [**Author**](#author )
  - [**Date**](#date )
  - [**Document Control**](#document-control )
  - [**Copyright Notice**](#copyright-notice )
- [Table of Content](#table-of-content )
- [01 - Project Homelab Overview](#01---project-homelab-overview )
  - [1.1 Executive Summary](#11-executive-summary )
- [02 - Hardware Overview](#02---hardware-overview )
  - [2.1 Server Hardware (Proxmox Host)](#21-server-hardware-proxmox-host )
  - [2.2 Networking Hardware](#22-networking-hardware )
  - [2.3 Switch Configuration Summary](#23-switch-configuration-summary )
- [03 - Future Roadmap](#03---future-roadmap )
      - [Planned additions:](#planned-additions )
- [04 — Network Architecture](#04--network-architecture )
  - [4.1 High-Level Design Diagram](#41-high-level-design-diagram )
- [05 - Switch and VLAN Configuration](#05---switch-and-vlan-configuration )
  - [5.1 VLAN Plan](#51-vlan-plan )
  - [5.2 SG105E VLAN Implementation](#52-sg105e-vlan-implementation )
  - [5.3 Port Plan](#53-port-plan )
- [06 - Proxmox Configuration](#06---proxmox-configuration )
  - [6.1 Network Interfaces Configuration](#61-network-interfaces-configuration )
    - [6.2 Proxmox Network Diagram](#62-proxmox-network-diagram )
  - [6.3 Key Rules](#63-key-rules )
- [07 - OPNsense Configuration](#07---opnsense-configuration )
  - [7.1 Interface Assignment](#71-interface-assignment )
  - [7.2 Initial Console Setup (Critical Steps)](#72-initial-console-setup-critical-steps )
        - [WAN (vtnet0)](#wan-vtnet0 )
        - [LAN (vtnet1)](#lan-vtnet1 )
  - [7.3 Overview](#73-overview )
- [08 – Installation History](#08--installation-history )
  - [8.1 Hardware Bring-Up and Initial System Issues](#81-hardware-bring-up-and-initial-system-issues )
    - [8.1.1 Motherboard Cleaning and Preparation](#811-motherboard-cleaning-and-preparation )
    - [8.1.2 RAM Voltage Mismatch Causing Boot Loop](#812-ram-voltage-mismatch-causing-boot-loop )
    - [8.1.3 Incorrect Drive Selection During Initial Proxmox Installation](#813-incorrect-drive-selection-during-initial-proxmox-installation )
    - [8.1.4 NVMe Reinstallation and Boot Drive Verification](#814-nvme-reinstallation-and-boot-drive-verification )
    - [8.1.5 NIC Not Detected → PCIe Slot Reassignment](#815-nic-not-detected--pcie-slot-reassignment )
  - [8.2 Network Configuration Challenges](#82-network-configuration-challenges )
    - [8.2.1 VLAN and Proxmox Misconfigurations](#821-vlan-and-proxmox-misconfigurations )
    - [Network Bring-Up Overview Diagram](#network-bring-up-overview-diagram )
- [09 – Issues and Solutions](#09--issues-and-solutions )
  - [9.1 Issue: RAM Voltage Mismatch Causing Boot Loop](#91-issue-ram-voltage-mismatch-causing-boot-loop )
  - [9.2 Issue: Proxmox Unreachable (Wrong Static IP Assigned)](#92-issue-proxmox-unreachable-wrong-static-ip-assigned )
  - [9.3 Issue: OPNsense Assigning IP to Proxmox `vmbr0`](#93-issue-opnsense-assigning-ip-to-proxmox-vmbr0 )
  - [9.4 Issue: ISO Not Booting Inside Proxmox VM](#94-issue-iso-not-booting-inside-proxmox-vm )
  - [9.5 Issue: 10G NIC Not Detected](#95-issue-10g-nic-not-detected )
  - [9.6 Issue: OPNsense Boot Loop Into Live Mode](#96-issue-opnsense-boot-loop-into-live-mode )
  - [9.7 Issue: Proxmox Root Filesystem Becomes Read-Only After Enabling IOMMU](#97-issue-proxmox-root-filesystem-becomes-read-only-after-enabling-iommu )
  - [9.8 Issue Flow Summary Diagram](#98-issue-flow-summary-diagram )
- [10 – Lessons Learned](#10--lessons-learned )
  - [10.1 General Hardware Lessons](#101-general-hardware-lessons )
  - [10.2 Proxmox Lessons](#102-proxmox-lessons )
  - [10.3 Firewall / OPNsense Lessons](#103-firewall--opnsense-lessons )
  - [10.4 Networking & VLAN Lessons](#104-networking--vlan-lessons )
  - [10.5 Storage Lessons](#105-storage-lessons )
  - [10.6 Operational Lessons](#106-operational-lessons )
  - [10.7 Summary](#107-summary )
- [11 – Best Practices](#11--best-practices )
  - [11.1 Hypervisor Best Practices](#111-hypervisor-best-practices )
  - [11.2 Networking Best Practices](#112-networking-best-practices )
  - [11.3 OPNsense / Firewall Best Practices](#113-opnsense--firewall-best-practices )
  - [11.4 Storage Best Practices](#114-storage-best-practices )
  - [11.5 Operational Best Practices](#115-operational-best-practices )
  
  
  
  
  
# 01 - Project Homelab Overview
  
## 1.1 Executive Summary
  
This document provides a complete technical and architectural overview of my Homelab project.
It covers hardware configuration, network architecture, Proxmox virtualization, OPNsense routing, VLAN segmentation, issues encountered during implementation, resolutions, troubleshooting workflows, and planned future enhancements.
  
The lab is designed to:
- provide an isolated penetration testing environment
- integrate a virtualized firewall and router (OPNsense)
- support Proxmox node management via a dedicated management network
- simulate enterprise-grade network segmentation
- host additional systems such as DNS servers, NAS storage, monitoring, and cloud backup infrastructure
- The documentation follows standard internal IT project documentation formats, including architectural diagrams, issue registers, and future roadmap guidance.
<div class="pagebreak"> </div>  
  
# 02 - Hardware Overview
  
## 2.1 Server Hardware (Proxmox Host)
| Component                  | Details                                                                               |
| -------------------------- | ------------------------------------------------------------------------------------- |
| **CPU**                    | AMD Ryzen 5 5600X (repurposed from old PC)                                            |
| **RAM**                    | 16GB DDR4 3200MHz CL16 (2×8GB, compatible voltage after removal of mismatched sticks) |
| **Motherboard**            | ASRock B450 Pro4                                                                      |
| **Primary Storage (Host)** | 500GB NVMe M.2 SSD (new purchase, used for Proxmox installation)                      |
| **Additional Storage**     | 1TB HDD (old), 2×4TB WD Red (new, for future NAS / RAIDZ1)                            |
| **Power Supply**           | 350W (limited SATA connectors, worked with SATA splitter approach)                    |
| **GPU**                    | Old GPU temporarily used for troubleshooting (no iGPU on CPU), removed afterward      |
| **NIC**                    | ASUS XG-C100C 10G NIC (PCIe, moved to primary x16 slot for detection)                 |
  
## 2.2 Networking Hardware
| Device                                    | Purpose                                             |
| ----------------------------------------- | --------------------------------------------------- |
| **TP-Link SG105E (Smart Managed Switch)** | VLAN creation, trunking, segmentation               |
| **TP-Link SG105 (Unmanaged Switch)**      | Dedicated VLAN10 Homelab / Pentest LAN              |
| **DLAN Adapter**                          | Temporary WAN uplink from Fritzbox to Homelab room  |
| **Cabling & Labeling**                    | Structured cabling work executed with label printer |
  
## 2.3 Switch Configuration Summary
TP-Link SG105E Configuration (Smart Managed)
| Port  | Connection                  | VLAN Mode                | Notes                              |
| ----- | --------------------------- | ------------------------ | ---------------------------------- |
| **1** | Incoming from DLAN/Fritzbox | VLAN1 untagged           | Main WAN/Heimnetz                  |
| **2** | Free/temporary admin port   | VLAN1 or VLAN10 untagged | Used for MacBook admin device      |
| **3** | Uplink to SG105 unmanaged   | VLAN10 untagged          | Dedicated Pentest/Homelab LAN      |
| **4** | Proxmox NIC0 (Onboard)      | VLAN1 untagged           | Management IP 192.168.178.10       |
| **5** | Proxmox 10G NIC (enp10s0)   | VLAN10 untagged          | Routed internally via OPNsense LAN |
<div class="pagebreak"> </div>  
  
# 03 – Future Roadmap
  
This chapter outlines planned extensions and improvements for the Homelab environment.  
The roadmap is deliberately modular so that individual items can be implemented independently depending on time, budget, and hardware availability.
  
---
  
## 3.1 Short-Term (0–3 months)
  
These items provide immediate value with limited effort and minimal hardware requirements.
  
- **Pi-hole / AdGuard Home (DNS filtering)**  
  Deploy a dedicated DNS filtering solution (VM or container) behind OPNsense to block ads, tracking, and malicious domains for the Homelab VLAN and optionally the home network.
  
- **Automated Configuration Backups**  
  Implement backup routines for:
  - Proxmox configuration
  - OPNsense config.xml
  - TrueNAS configuration
  - Important VM/container configs  
  Backups should be stored on the NAS and optionally synced to an external drive.
  
- **WireGuard VPN on OPNsense**  
  Provide secure remote access to the Homelab network using WireGuard, limited to specific VLANs and management addresses.
  
---
  
## 3.2 Mid-Term (3–12 months)
  
These additions extend functionality and improve resilience and observability.
  
- **TrueNAS SCALE or ZFS-based NAS**  
  Build a dedicated NAS based on ZFS to host:
  - VM disks
  - Backups
  - Media and data shares  
  Integrate with Proxmox via NFS or iSCSI.
  
- **Monitoring Stack (Prometheus, Grafana)**  
  Collect metrics from:
  - Proxmox
  - OPNsense
  - NAS
  - Critical VMs/containers  
  Visualize performance and capacity using Grafana dashboards.
  
- **Pentesting Lab with Active Directory Domain**  
  Create an isolated AD domain (e.g., Windows Server + clients) for penetration testing, Blue-Team exercises, and security research.  
  This lab remains strictly contained within a dedicated VLAN.
  
- **Cloud Backup Environment for Apple Devices**  
  Provide a dedicated backup target (e.g., SMB/Time Machine share on TrueNAS) for Apple devices in the home network.
  
---
  
## 3.3 Long-Term (12+ months)
  
These projects further segment the network and introduce more advanced security concepts.
  
- **VLAN20 – IoT**  
  Create an isolated VLAN20 for IoT devices:
  - No direct access to Homelab VLAN10
  - Restricted outbound Internet access
  - Optional DNS filtering by Pi-hole / AdGuard
  
- **VLAN30 – Guest WiFi**  
  Create a guest network with VLAN30:
  - Strict isolation from home and Homelab networks
  - Rate limiting and time-based access possible
  - DNS filtering and logging through OPNsense
  
---
  
## 3.4 Strategic Goals
  
- Maintain clear separation between **home**, **lab**, and **guest** networks  
- Increase security while keeping administration overhead manageable  
- Establish a robust backup and monitoring strategy  
- Use the Homelab as a learning platform for:
  - virtualization
  - networking and VLANs
  - firewall administration
  - storage and backup concepts
  
<div class="pagebreak"> </div>
  
  
# 04 — Network Architecture
  
## 4.1 High-Level Design Diagram

```
Error: mermaid CLI is required to be installed.
Check https://github.com/mermaid-js/mermaid-cli for more information.

Error: Command failed: npx -p @mermaid-js/mermaid-cli mmdc --theme default --input C:\Users\MAXI~1.JAT\AppData\Local\Temp\crossnote-mermaid2025113-4968-34odo4.gikep.mmd --output c:\Users\maxi.jatzkowski\OneDrive - SecatGmbH\Desktop\Homelab_Documentation\1.2_Homelab_Documentation\assets\867ef0c6b7074bc935bdae2a9b6854e80.png
error: too many arguments. Expected 0 arguments but got 2.

```  

<div class="pagebreak"> </div>  
  
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

```
Error: mermaid CLI is required to be installed.
Check https://github.com/mermaid-js/mermaid-cli for more information.

Error: Command failed: npx -p @mermaid-js/mermaid-cli mmdc --theme default --input C:\Users\MAXI~1.JAT\AppData\Local\Temp\crossnote-mermaid2025113-4968-h4cqxi.psohh.mmd --output c:\Users\maxi.jatzkowski\OneDrive - SecatGmbH\Desktop\Homelab_Documentation\1.2_Homelab_Documentation\assets\867ef0c6b7074bc935bdae2a9b6854e81.png
error: too many arguments. Expected 0 arguments but got 2.

```  

  
<div class="pagebreak"> </div>  
  
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
    address 192.168.178.10/24
    gateway 192.168.178.1
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
  

```
Error: mermaid CLI is required to be installed.
Check https://github.com/mermaid-js/mermaid-cli for more information.

Error: Command failed: npx -p @mermaid-js/mermaid-cli mmdc --theme default --input C:\Users\MAXI~1.JAT\AppData\Local\Temp\crossnote-mermaid2025113-4968-1pvhn10.whv2.mmd --output c:\Users\maxi.jatzkowski\OneDrive - SecatGmbH\Desktop\Homelab_Documentation\1.2_Homelab_Documentation\assets\867ef0c6b7074bc935bdae2a9b6854e82.png
error: too many arguments. Expected 0 arguments but got 2.

```  

## 6.3 Key Rules
- vmbr0 must NEVER lose its static IP (fail-safe management)
- vmbr1 must NEVER have an IP (isolated, routed by OPNsense)
- OPNsense interacts only with the vtnet virtual interfaces
- VLANs are handled physically by switch, not by Proxmox
<div class="pagebreak"> </div>  
  
# 07 - OPNsense Configuration
  
## 7.1 Interface Assignment
| vtnet      | Purpose | Connected to                 |
| ---------- | ------- | ---------------------------- |
| **vtnet0** | WAN     | vmbr0 (DHCP via Fritzbox)    |
| **vtnet1** | LAN     | vmbr1 (static 10.10.10.1/24) |
  
## 7.2 Initial Console Setup (Critical Steps)
##### WAN (vtnet0)
 - Configure IPv4: YES
 - Mode: DHCP
 - No static IPs
 - No IPv6
 - No gateway manually set
  
##### LAN (vtnet1)
 - Configure IPv4: YES
 - IP: 10.10.10.1
 - Subnet: /24
 - Enable DHCP server: YES
 - Range: 10.10.10.100 – 10.10.10.200
 - Enable HTTPS: YES
 - Generate self-signed certificate: YES
  
## 7.3 Overview

```
Error: mermaid CLI is required to be installed.
Check https://github.com/mermaid-js/mermaid-cli for more information.

Error: Command failed: npx -p @mermaid-js/mermaid-cli mmdc --theme default --input C:\Users\MAXI~1.JAT\AppData\Local\Temp\crossnote-mermaid2025113-4968-i6cs3r.f9s6.mmd --output c:\Users\maxi.jatzkowski\OneDrive - SecatGmbH\Desktop\Homelab_Documentation\1.2_Homelab_Documentation\assets\867ef0c6b7074bc935bdae2a9b6854e83.png
error: too many arguments. Expected 0 arguments but got 2.

```  

  
<div class="pagebreak"> </div>  
  
# 08 – Installation History
  
This section provides a full chronological and technical overview of all installation steps, problems encountered, corrections applied, and recovery operations performed during the initial deployment of the Homelab environment.
  
The goal of this chapter is to ensure complete traceability, reproducibility, and long-term documentation of all system bring-up activities.
  
---
  
## 8.1 Hardware Bring-Up and Initial System Issues
  
### 8.1.1 Motherboard Cleaning and Preparation
  
Before the first installation attempt, the motherboard required physical cleaning due to dust accumulation and residual thermal paste near the CPU socket and VRM area. The cleaning process included:
  
- removing the CPU cooler  
- cleaning all exposed surfaces with isopropyl alcohol  
- visually inspecting the PCB for conductive debris  
- reseating RAM modules and cables  
- ensuring correct cooler mounting pressure  
  
After cleaning, the board successfully powered on and completed POST.
  
---
  
### 8.1.2 RAM Voltage Mismatch Causing Boot Loop
  
During early boot attempts, the system entered a repeating **boot loop** (power → shutdown → power).  
Investigation revealed that the RAM ran with an incorrect/missmatched **XMP voltage**, not supported by the motherboard defaults.
  
Resolution:
  
- BIOS reset to factory defaults  
- RAM manually set to JEDEC-compatible voltage (1.2V–1.35V)  
- system booted successfully afterward 
- 1.2V and 1.35V RAM are not compatible with each other
  
---
  
### 8.1.3 Incorrect Drive Selection During Initial Proxmox Installation
  
The initial Proxmox installation was accidentally performed on the SSD instead of the NVMe SSD.
  
Consequences:
  
- very slow overall system performance  
- disk conflicts with planned ZFS mirror  
- boot loader not located where expected  
  
Solution:
  
- wipe the SSD  
- perform a fresh Proxmox installation onto the NVMe SSD  
  
---
  
### 8.1.4 NVMe Reinstallation and Boot Drive Verification
  
A full reinstall was performed on the NVMe storage. During this step:
  
- standard LVM configuration was chosen  
- no ZFS-on-root configuration  
- default Proxmox network bridge created (vmbr0)  
  
The system installed successfully and booted without issues.
  
---
  
### 8.1.5 NIC Not Detected → PCIe Slot Reassignment
  
After installation, the 10G NIC did not appear in Proxmox.
  
Root cause:
  
- NIC was installed in a PCIe slot not providing the required chipset connection.
  
Fix:
  
- move card into a different PCIe slot  
- reboot  
- NIC recognized correctly as `enp10s0`  
  
---
  
## 8.2 Network Configuration Challenges
  
### 8.2.1 VLAN and Proxmox Misconfigurations
  
During early configuration steps, several issues occurred:
  
1. **VLAN1/VLAN10 incorrectly configured**  
   - PVID values on the SG105E switched ports were mismatched  
   - untagged/tagged roles not correctly assigned  
  
2. **Incorrect Proxmox IP configuration**  
   - `vmbr0` was misconfigured  
   - caused complete loss of connectivity  
   - required physical access and temporary GPU installation  
  
3. **Incorrect vtnet assignments in OPNsense**  
   - vtnet0 (WAN) and vtnet1 (LAN) were swapped  
   - caused Proxmox to direct its management interface into the LAN  
   - resulted in a full Proxmox lockout  
  
4. **Manual recovery operations**  
   - temporary GPU installation for console access  
   - repair of `/etc/network/interfaces`  
   - reassignment of WAN/LAN inside OPNsense  
   - validation of DHCP on VLAN10  
   - ensuring VLAN1 remained untagged and isolated  
  
<div class="pagebreak"> </div>  
  
# 09 – Issues and Solutions
  
This chapter documents all issues encountered during the Homelab deployment, including their symptoms, root causes, diagnostic methods, and confirmed solutions.  
The purpose of this section is to make future recovery easier, to prevent recurring errors, and to maintain a technical knowledge base for the system.
  
---
  
## 9.1 Issue: RAM Voltage Mismatch Causing Boot Loop
  
**Symptoms:**  
- System powers on → immediately shuts off → loops  
- No POST  
- No video output  
  
**Root Cause:**  
Mixed voltage RAM modules (1.2V + 1.35V) caused unstable memory training.
  
**Diagnostics:**  
- Removing modules one by one  
- Identifying that both 1.2V and 1.35V modules boot successfully, but not in combination (pairs only)  
  
**Solution:**  
- Remove mismatched high-voltage modules  
- Use identical JEDEC-compatible sticks only  
- System boots consistently afterward
  
---
  
## 9.2 Issue: Proxmox Unreachable (Wrong Static IP Assigned)
  
**Symptoms:**  
- No Web UI  
- No SSH  
- Ping fails  
- Server disappears from LAN  
  
**Root Cause:**  
Management bridge `vmbr0` was accidentally assigned **10.10.10.1**, an internal LAN address used by OPNsense.  
This moved Proxmox into the firewall LAN, isolating it from the main network.
  
**Diagnostics:**  
- Checked switch: port active  
- No ARP entry  
- No DHCP lease → static misconfig  
- Required local console to repair  
  
**Solution:**  
- Install GPU temporarily  
- Boot into shell  
- Restore `/etc/network/interfaces`  
- Reassign correct management IP (e.g. 192.168.178.10/24)
  
---
  
## 9.3 Issue: OPNsense Assigning IP to Proxmox `vmbr0`
  
**Symptoms:**  
- Proxmox suddenly receives an IP from OPNsense  
- Routing conflicts  
- GUI becomes unreachable  
  
**Root Cause:**  
WAN/LAN interfaces inside OPNsense were reversed:  
- vtnet0 (WAN) was configured as LAN  
- vtnet1 (LAN) was configured as WAN  
  
This caused OPNsense to treat vmbr0 as a LAN client and assign DHCP to it.
  
**Solution:**  
- Reassign interfaces inside OPNsense console  
- Correct mapping:  
  - **vtnet0 → WAN (DHCP)**  
  - **vtnet1 → LAN (10.10.10.1/24)**  
- Reboot firewall  
- Connectivity restored
  
---
  
## 9.4 Issue: ISO Not Booting Inside Proxmox VM
  
**Symptoms:**  
- VM shows “No bootable device”  
- OPNsense/other ISOs ignored  
  
**Root Cause:**  
Legacy BIOS mode selected → incompatible with OPNsense image.
  
**Solution:**  
- Change VM firmware to **SeaBIOS**  
- Boot works immediately  
  
---
  
## 9.5 Issue: 10G NIC Not Detected
  
**Symptoms:**  
- Proxmox shows only one network adapter  
- `ip a` returns no 10G interface  
- No link LEDs on NIC  
  
**Root Cause:**  
The XG-C100C card needs a **full x16 or x4 electrical slot**.  
Original slot was chipset-disabled for large PCIe devices.
  
**Diagnostics:**  
- Move card between slots  
- Find slot electrically wired to CPU  
  
**Solution:**  
- Move GPU to secondary slot  
- Install NIC in primary x16 slot  
- NIC detected as `enp10s0` after reboot
  
---
  
## 9.6 Issue: OPNsense Boot Loop Into Live Mode
  
**Symptoms:**  
- After installation OPNsense keeps showing the installer  
- Settings gone after reboot  
- LAN IP resets each time  
  
**Root Cause:**  
The ISO was **not removed** after installation.  
VM rebooted into Live Mode instead of the installed system.
  
**Solution:**  
- Remove ISO from VM hardware → CD/DVD drive  
- Reboot → boots from virtual disk normally
  
---
  
## 9.7 Issue: Proxmox Root Filesystem Becomes Read-Only After Enabling IOMMU
  
**Symptoms:**
- System boots → root filesystem becomes read-only after ~3–10 seconds  
- Docker/LXC/TrueNAS VMs fail  
- Logs show:
`EXT4-fs (dm-1): failed to convert unwritten extents …`
`EXT4-fs error: aborted journal`
`EXT4-fs: Remounting filesystem read-only`
  
- Web UI unreachable  
- SSH read-only  
  
**Root Cause:**  
The NVMe used for Proxmox installation is a **DRAM-less SSD**.  
When enabling **IOMMU**, queue depth and latency increase →  
NVMe firmware triggers **protection mode**, making the drive read-only.
  
This is a known problem with DRAM-less NVMe in virtualization environments.
  
**Failed Solutions Attempted:**  
- Proxmox Recovery Mode → no fix  
- Boot into single-user mode via GRUB:  
`init=/bin/bash`
- Running filesystem repair:
`fsck -yf /dev/mapper/pve-root` or `fsck -yf /dev/pve/root `
- All fsck phases successful → **BUT issue returns after reboot**  
→ because the problem is firmware-level, not filesystem-level.
  
**Working Solution:**  
- **Disable IOMMU** in BIOS  
- Avoid passthrough for the DRAM-less NVMe  
- Use WD Red HDDs as a **ZFS mirror**  
- Keep NVMe only as simple Proxmox boot disk  
- (Optional) Replace with a proper NVMe with DRAM
  
---
  
## 9.8 Issue Flow Summary Diagram
  
A complete overview of all discovered issues:
  

```
Error: mermaid CLI is required to be installed.
Check https://github.com/mermaid-js/mermaid-cli for more information.

Error: Command failed: npx -p @mermaid-js/mermaid-cli mmdc --theme default --input C:\Users\MAXI~1.JAT\AppData\Local\Temp\crossnote-mermaid2025113-4968-191lenu.o41t.mmd --output c:\Users\maxi.jatzkowski\OneDrive - SecatGmbH\Desktop\Homelab_Documentation\1.2_Homelab_Documentation\assets\867ef0c6b7074bc935bdae2a9b6854e84.png
error: too many arguments. Expected 0 arguments but got 2.

```  

<div class="pagebreak"> </div>  
  
# 10 – Lessons Learned
  
This chapter summarizes all important insights gained during the Homelab deployment.  
The following points are based on real-world issues encountered during setup, configuration, and recovery operations.
  
---
  
## 10.1 General Hardware Lessons
  
- **DRAM-less NVMe SSDs are not suitable for hypervisors or passthrough workloads**  
  NVMe without onboard DRAM becomes unstable under IOMMU load and may enter firmware-level read-only mode.  
  → Always use NVMe with DRAM or enterprise SSDs for Proxmox boot devices.
  
- **PCIe slot selection matters**  
  Some NICs (e.g., XG-C100C) require x4/x8/x16 electrical lanes and will not initialize in chipset-side slots.  
  → Always verify PCIe lane wiring in the motherboard manual.
  
- **RAM voltage must match across all modules**  
  Mixing 1.2V, 1.35V, and 1.5V modules causes unstable memory training and boot loops.  
  → Use identical sticks or disable XMP profiles.
  
- **A temporary GPU is essential for recovery**  
  Headless servers are easily lost due to misconfiguration.  
  → Keep a simple PCIe GPU available for emergency console access.
  
---
  
## 10.2 Proxmox Lessons
  
- **Always keep a dedicated management network outside the firewall**  
  Mixing Proxmox management IPs with internal OPNsense subnets will instantly lock you out.
  
- **Avoid configuring the management IP inside a routed VLAN**  
  Setting `vmbr0` to 10.10.10.1 isolated Proxmox behind the firewall.  
  → Never reuse internal firewall subnets for hypervisor management.
  
- **Backup `/etc/network/interfaces` before major changes**  
  A single wrong entry leads to a complete lockout.  
  → Keep a copy in `/root/interfaces.backup`.
  
- **IOMMU passthrough must not be used with DRAM-less NVMe**  
  This combination forces EXT4 into read-only protection.  
  → Disable IOMMU or move the system to a more suitable disk.
  
- **Always remove ISOs from VM CD/DVD drives after installation**  
  OPNsense will otherwise reboot into Live Mode every time instead of booting from the installed disk.
  
---
  
## 10.3 Firewall / OPNsense Lessons
  
- **Never configure a firewall from inside the network it is routing**  
  If WAN/LAN are misassigned, the admin machine becomes isolated.
  
- **Understand vtnet interface ordering**  
  VirtIO adapters are not always assigned in alphabetical order.  
  → Always check interface MACs when mapping WAN/LAN.
  
- **OPNsense will assign DHCP to any interface marked as LAN**  
  Misconfigured interfaces can affect the hypervisor network.  
  → Confirm interface roles before enabling DHCP.
  
- **Back up OPNsense configuration regularly**  
  The `config.xml` file should be exported after every major change.
  
---
  
## 10.4 Networking & VLAN Lessons
  
- **Tagged vs. untagged VLANs must be fully understood before implementation**  
  Incorrect PVID or tagging breaks routing instantly.  
  → Use diagrams to visualize port roles and flows.
  
- **Smart switches behave differently than enterprise switches**  
  TP-Link SG105E uses PVID logic instead of Cisco-style trunk/native VLAN logic.  
  → Understand vendor-specific VLAN behavior before deployment.
  
- **Use diagrams early to avoid mis-patching**  
  Visual planning reduces errors and improves documentation quality.
  
- **Do not rely on DLAN or mixed uplinks for core infrastructure**  
  Temporary uplinks can hide problems or introduce packet loss.  
  → Use dedicated Ethernet runs for long-term stability.
  
---
  
## 10.5 Storage Lessons
  
- **ZFS mirrors are more reliable than single consumer NVMe**  
  WD Red HDD mirrors with ZFS provide safer storage for TrueNAS and VM data.
  
- **NVMe should not carry both system and passthrough workload**  
  Separate boot and VM storage whenever possible.
  
- **EXT4 fsck success does not mean the NVMe is healthy**  
  Firmware issues can re-trigger read-only mode regardless of filesystem repair.
  
---
  
## 10.6 Operational Lessons
  
- **Document every IP, MAC address, and VLAN early**  
  Prevents conflicts and simplifies recovery.
  
- **Make incremental changes, not large batches**  
  Multiple changes at once make troubleshooting significantly harder.
  
- **Always validate routing before applying firewall rules**  
  Avoids creating unintended network islands.
  
- **Maintain a basic runbook for common tasks and recovery steps**  
  Short procedures for “Proxmox unreachable”, “OPNsense unreachable”, etc., reduce stress in outage situations.
  
---
  
## 10.7 Summary
  
The Homelab project provided valuable insights into:
  
- proper hypervisor installation  
- VLAN and network architecture  
- OPNsense routing and firewall concepts  
- hardware compatibility and limitations  
- storage and backup strategies  
- repeatable recovery procedures  
  
These lessons form the foundation for stable, maintainable future expansions of the Homelab environment.
  
<div class="pagebreak"> </div>
  
  
# 11 – Best Practices
  
This chapter consolidates all operational, architectural, and security best practices derived from the Homelab deployment.  
It serves as a reference for future upgrades, redesigns, and troubleshooting scenarios.
  
---
  
## 11.1 Hypervisor Best Practices
  
- Use NVMe with DRAM or enterprise SSDs for the Proxmox boot drive.  
- Keep a dedicated out-of-band management network for Proxmox.  
- Never expose management interfaces through routed VLANs.  
- Always maintain a physical GPU for emergency console access.  
- Back up `/etc/network/interfaces` before making network changes.  
- Use ZFS mirrors for reliable long-term storage.  
- Avoid DRAM-less NVMe for virtualization or passthrough workloads.
  
---
  
## 11.2 Networking Best Practices
  
- Keep management VLAN separate from production VLANs.  
- Create network diagrams before implementing changes.  
- Use descriptive port labeling on switches and patch panels.  
- Always verify PVID and VLAN tagging behavior on consumer smart switches.  
- Plan uplinks and downlinks ahead of time (avoid DLAN for core infrastructure).  
- Prefer wired Ethernet for critical links over powerline or WiFi bridges.
  
---
  
## 11.3 OPNsense / Firewall Best Practices
  
- Never configure a firewall from inside the network it is routing.  
- Verify correct WAN/LAN interface assignments before enabling DHCP.  
- Document all static mappings, VLANs, and upstream gateways.  
- Enable HTTPS access and generate a self-signed certificate at installation.  
- Maintain offline backups of the OPNsense configuration.  
- Restrict Web GUI exposure to management networks only.
  
---
  
## 11.4 Storage Best Practices
  
- Keep the hypervisor boot device separate from VM storage.  
- Use ZFS for disks that host VMs, containers, or NAS workloads.  
- Use S.M.A.R.T. monitoring for early disk failure detection.  
- Avoid placing the root filesystem and passthrough workloads on the same device.  
- Regularly test backup restores, not only backup creation.
  
---
  
## 11.5 Operational Best Practices
  
- Make incremental changes—never modify multiple systems at once without testing.  
- Keep a changelog of configuration edits and firmware updates.  
- Test connectivity after every modification (ping, ARP, DHCP, traceroute).  
- Validate routing before applying new firewall rules.  
- Document network topology updates immediately.  
- Perform regular off-site configuration backups.
  
---
  
## 11.6 Checklists
  
### 11.6.1 Pre-Change Checklist
  
- Recent backup of Proxmox, OPNsense, and important VMs.  
- Changelog entry prepared (what will be changed and why).  
- Rollback plan defined.  
- Maintenance window agreed (if services are impacted).  
  
### 11.6.2 Post-Change Verification Checklist
  
- Management access (Web UI + SSH) confirmed.  
- Internet access from VLAN10 confirmed.  
- Inter-VLAN isolation verified.  
- Monitoring and logging still operational.  
- Backups still running as scheduled.
  
---
  
## 11.7 Backup Strategy (High-Level)
  
- **Daily:**  
  - Proxmox configuration backup.  
  - OPNsense configuration export (automated or manual).  
  
- **Weekly:**  
  - Snapshot or backup of critical VMs / containers.  
  - TrueNAS configuration backup.  
  
- **Monthly:**  
  - Off-site copy to external disk or cloud storage.  
  
This strategy can be refined as additional services (e.g., monitoring, AD lab) are introduced.
  
<div class="pagebreak"> </div>
  
  
  
# Appendix – Diagrams
  
This appendix contains all architectural and troubleshooting diagrams used throughout the documentation.  
They are consolidated here for quick reference and print-friendly output.
  
---
  
## A.1 High-Level Network Architecture
  

```
Error: mermaid CLI is required to be installed.
Check https://github.com/mermaid-js/mermaid-cli for more information.

Error: Command failed: npx -p @mermaid-js/mermaid-cli mmdc --theme default --input C:\Users\MAXI~1.JAT\AppData\Local\Temp\crossnote-mermaid2025113-4968-10awb7n.14fhi.mmd --output c:\Users\maxi.jatzkowski\OneDrive - SecatGmbH\Desktop\Homelab_Documentation\1.2_Homelab_Documentation\assets\867ef0c6b7074bc935bdae2a9b6854e85.png
error: too many arguments. Expected 0 arguments but got 2.

```  

  
---
  
## A.2 Proxmox Virtual Network Layout
  

```
Error: mermaid CLI is required to be installed.
Check https://github.com/mermaid-js/mermaid-cli for more information.

Error: Command failed: npx -p @mermaid-js/mermaid-cli mmdc --theme default --input C:\Users\MAXI~1.JAT\AppData\Local\Temp\crossnote-mermaid2025113-4968-pp2ha1.8adjc.mmd --output c:\Users\maxi.jatzkowski\OneDrive - SecatGmbH\Desktop\Homelab_Documentation\1.2_Homelab_Documentation\assets\867ef0c6b7074bc935bdae2a9b6854e86.png
error: too many arguments. Expected 0 arguments but got 2.

```  

  
---
  
## A.3 Issue Overview Diagram
  

```
Error: mermaid CLI is required to be installed.
Check https://github.com/mermaid-js/mermaid-cli for more information.

Error: Command failed: npx -p @mermaid-js/mermaid-cli mmdc --theme default --input C:\Users\MAXI~1.JAT\AppData\Local\Temp\crossnote-mermaid2025113-4968-n9702k.7jmy.mmd --output c:\Users\maxi.jatzkowski\OneDrive - SecatGmbH\Desktop\Homelab_Documentation\1.2_Homelab_Documentation\assets\867ef0c6b7074bc935bdae2a9b6854e87.png
error: too many arguments. Expected 0 arguments but got 2.

```  

  
<div class="pagebreak"> </div>
  
  