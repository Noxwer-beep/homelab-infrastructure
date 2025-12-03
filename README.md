# Homelab Infrastructure Documentation

This repository contains the full **architecture, configuration, troubleshooting history, and roadmap** of my personal Homelab.

The lab is built around **Proxmox VE**, **OPNsense**, VLAN segmentation and planned ZFS-based storage.  
It is designed for **virtualization, networking labs, security experiments, and long-term learning**.
    ![Status](https://img.shields.io/badge/status-active-success)
    ![Docs](https://img.shields.io/badge/docs-markdown-blue)
    ![Platform](https://img.shields.io/badge/platform-Proxmox%20%7C%20OPNsense-lightgrey)

   ---
   ## Repository Structure

```text
docs/        → Main documentation (Markdown)
images/      → Network diagrams, screenshots 
configs/     → Example configuration files 
CHANGELOG.md → Version history of the documentation
README.md    → You are here
```

   ### Documentation Files (docs/)
   The following Markdown files make up the core of the documentation:

- `00_Master.md`
- `01_Project_Overview.md`
- `02_Hardware_Overview.md`
- `03_Future_Roadmap.md`
- `04_Network_Architecture.md`
- `05_Switch_and_VLAN_Configuration.md`
- `06_Proxmox_Configuration.md`
- `07_OPNsense_Configuration.md`
- `08_Installation_History.md`
- `09_Issues_and_Solutions.md`
- `10_Lessons_Learned.md`
- `11_Best_Practices.md`
- `999_Appendix_Diagrams.md`
- `Titlepage.md`

Each file focuses on a specific aspect of the homelab, such as hardware overview, network architecture, Proxmox configuration, OPNsense setup, issues & solutions, lessons learned and best practices.

   ---

   ## Architecture Overview

   Core building blocks:

   - **Hypervisor:** Proxmox VE
    - **Firewall / Router:** OPNsense (virtualized)
    - **Switching:** TP-Link SG105E with VLAN segmentation
    - **Homelab VLAN:** Isolated lab network (e.g. 10.10.10.0/24)
    - **Storage:** NVMe boot disk + planned ZFS mirror on HDDs
    - **Planned Services:** DNS filtering (Pi-hole / AdGuard), monitoring (Prometheus/Grafana), NAS (TrueNAS SCALE), AD pentest lab

   The documentation covers both the *final design* and the *real-world problems* encountered along the way.

   ---

   ## Goals

   - Provide a **reproducible** homelab design for learning and experimentation
    - Simulate **enterprise-like network segmentation** and firewalling
    - Document **every major issue and fix** for future recovery
    - Build a long-term base for **security labs, automation and experiments**
    - Practice clean, structured **technical documentation**

   ---

   ## Versioning & Changelog

   The documentation follows a simple semantic versioning scheme:

   - **MAJOR** – breaking structural changes to the documentation
    - **MINOR** – new chapters, sections or major improvements
    - **PATCH** – small corrections, typo fixes, minor clarifications

   All changes are tracked in **[`CHANGELOG.md`](./CHANGELOG.md)**.

   Current documented versions:

   - **1.1.0** – Restructured documentation, added roadmap, issues and lessons.
    - **1.0.0** – Initial release of the Homelab documentation.

   ---

   ## Usage

   This repository is intended as:

   - A **personal knowledge base** for my homelab
    - A **reference** for future rebuilds and redesigns
    - A **learning resource** for others interested in Proxmox + OPNsense homelabs
    - A record of real configuration mistakes and their solutions

   You can simply browse the `docs/` directory on GitHub or clone the repository and open the files locally in your editor.

   ---

   ## Roadmap (High-Level)

   Planned next steps include:

   - Setting up **TrueNAS SCALE** or another ZFS-based NAS
    - Adding a **monitoring stack** (Prometheus + Grafana)
    - Creating an isolated **Active Directory lab** for pentesting
    - Introducing additional VLANs (IoT, Guests) with proper firewalling
    - Automating backups for Proxmox, OPNsense and key VMs

   Details can be found in the dedicated roadmap chapter in `docs/`.

   ---

   ## License

   This documentation is primarily for educational and personal use.  
    If you want to reuse parts of it, please create an issue or contact me first.
