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

<!-- pagebreak -->
