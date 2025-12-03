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
   
<!-- pagebreak -->