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

<!-- pagebreak -->
