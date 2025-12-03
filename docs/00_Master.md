@import "Titlepage.md"
# Table of Content

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [**Homelab Infrastructure Documentation**](#homelab-infrastructure-documentation)
    - [**Version 1.1.0**](#version-110)
      - [**Technical Architecture & Operational Overview**](#technical-architecture--operational-overview)
  - [**Author**](#author)
  - [**Date**](#date)
  - [**Document Control**](#document-control)
  - [**Copyright Notice**](#copyright-notice)
- [Table of Content](#table-of-content)
- [01 - Project Homelab Overview](#01---project-homelab-overview)
  - [1.1 Executive Summary](#11-executive-summary)
- [02 - Hardware Overview](#02---hardware-overview)
  - [2.1 Server Hardware (Proxmox Host)](#21-server-hardware-proxmox-host)
  - [2.2 Networking Hardware](#22-networking-hardware)
  - [2.3 Switch Configuration Summary](#23-switch-configuration-summary)
- [03 - Future Roadmap](#03---future-roadmap)
      - [Planned additions:](#planned-additions)
- [04 — Network Architecture](#04--network-architecture)
  - [4.1 High-Level Design Diagram](#41-high-level-design-diagram)
- [05 - Switch and VLAN Configuration](#05---switch-and-vlan-configuration)
  - [5.1 VLAN Plan](#51-vlan-plan)
  - [5.2 SG105E VLAN Implementation](#52-sg105e-vlan-implementation)
  - [5.3 Port Plan](#53-port-plan)
- [06 - Proxmox Configuration](#06---proxmox-configuration)
  - [6.1 Network Interfaces Configuration](#61-network-interfaces-configuration)
    - [6.2 Proxmox Network Diagram](#62-proxmox-network-diagram)
  - [6.3 Key Rules](#63-key-rules)
- [07 - OPNsense Configuration](#07---opnsense-configuration)
  - [7.1 Interface Assignment](#71-interface-assignment)
  - [7.2 Initial Console Setup (Critical Steps)](#72-initial-console-setup-critical-steps)
        - [WAN (vtnet0)](#wan-vtnet0)
        - [LAN (vtnet1)](#lan-vtnet1)
  - [7.3 Overview](#73-overview)
- [08 – Installation History](#08--installation-history)
  - [8.1 Hardware Bring-Up and Initial System Issues](#81-hardware-bring-up-and-initial-system-issues)
    - [8.1.1 Motherboard Cleaning and Preparation](#811-motherboard-cleaning-and-preparation)
    - [8.1.2 RAM Voltage Mismatch Causing Boot Loop](#812-ram-voltage-mismatch-causing-boot-loop)
    - [8.1.3 Incorrect Drive Selection During Initial Proxmox Installation](#813-incorrect-drive-selection-during-initial-proxmox-installation)
    - [8.1.4 NVMe Reinstallation and Boot Drive Verification](#814-nvme-reinstallation-and-boot-drive-verification)
    - [8.1.5 NIC Not Detected → PCIe Slot Reassignment](#815-nic-not-detected--pcie-slot-reassignment)
  - [8.2 Network Configuration Challenges](#82-network-configuration-challenges)
    - [8.2.1 VLAN and Proxmox Misconfigurations](#821-vlan-and-proxmox-misconfigurations)
    - [Network Bring-Up Overview Diagram](#network-bring-up-overview-diagram)
- [09 – Issues and Solutions](#09--issues-and-solutions)
  - [9.1 Issue: RAM Voltage Mismatch Causing Boot Loop](#91-issue-ram-voltage-mismatch-causing-boot-loop)
  - [9.2 Issue: Proxmox Unreachable (Wrong Static IP Assigned)](#92-issue-proxmox-unreachable-wrong-static-ip-assigned)
  - [9.3 Issue: OPNsense Assigning IP to Proxmox `vmbr0`](#93-issue-opnsense-assigning-ip-to-proxmox-vmbr0)
  - [9.4 Issue: ISO Not Booting Inside Proxmox VM](#94-issue-iso-not-booting-inside-proxmox-vm)
  - [9.5 Issue: 10G NIC Not Detected](#95-issue-10g-nic-not-detected)
  - [9.6 Issue: OPNsense Boot Loop Into Live Mode](#96-issue-opnsense-boot-loop-into-live-mode)
  - [9.7 Issue: Proxmox Root Filesystem Becomes Read-Only After Enabling IOMMU](#97-issue-proxmox-root-filesystem-becomes-read-only-after-enabling-iommu)
  - [9.8 Issue Flow Summary Diagram](#98-issue-flow-summary-diagram)
- [10 – Lessons Learned](#10--lessons-learned)
  - [10.1 General Hardware Lessons](#101-general-hardware-lessons)
  - [10.2 Proxmox Lessons](#102-proxmox-lessons)
  - [10.3 Firewall / OPNsense Lessons](#103-firewall--opnsense-lessons)
  - [10.4 Networking & VLAN Lessons](#104-networking--vlan-lessons)
  - [10.5 Storage Lessons](#105-storage-lessons)
  - [10.6 Operational Lessons](#106-operational-lessons)
  - [10.7 Summary](#107-summary)
- [11 – Best Practices](#11--best-practices)
  - [11.1 Hypervisor Best Practices](#111-hypervisor-best-practices)
  - [11.2 Networking Best Practices](#112-networking-best-practices)
  - [11.3 OPNsense / Firewall Best Practices](#113-opnsense--firewall-best-practices)
  - [11.4 Storage Best Practices](#114-storage-best-practices)
  - [11.5 Operational Best Practices](#115-operational-best-practices)

<!-- /code_chunk_output -->


@import "01_Project_Overview.md"
@import "02_Hardware_Overview.md"
@import "03_Future_Roadmap.md"
@import "04_Network_Architecture.md"
@import "05_Switch_and_VLAN_Configuration.md"
@import "06_Proxmox_Configuration.md"
@import "07_OPNsense_Configuration.md"
@import "08_Installation_History.md"
@import "09_Issues_and_Solutions.md"
@import "10_Lessons_Learned.md"
@import "11_Best_Practices.md"

@import "999_Appendix_Diagrams.md"
