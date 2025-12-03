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

<!-- pagebreak -->
