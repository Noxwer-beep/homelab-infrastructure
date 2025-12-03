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
<!-- pagebreak -->