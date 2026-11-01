<div align="center">
  <img src="assets/cat.png" width="72" alt="">
  <h1>Homelab Infrastructure</h1>
  <p><em>How the lab is put together, and why each decision was made.</em></p>
  <p>
    <a href="https://noxwer-beep.github.io/portfolio/homelab.html">Case study</a> ·
    <a href="docs/01_Project_Overview.md">Start reading</a> ·
    <a href="CHANGELOG.md">Changelog</a>
  </p>
</div>

---

How the lab is put together, and why: Proxmox VE, OPNsense, VLAN segmentation and
planned ZFS storage. I write it down for two reasons — so I can rebuild it, and so I can
explain a decision instead of guessing at it later. The mistakes are in here too.

> **Addressing is redacted.** Exact IPs, the VLAN port map and the master PDF live in a
> private vault. What is published here is the design reasoning and the configuration
> approach — placeholders such as `<lab-subnet>`, `<home-lan>` and `<lab-gateway>` stand
> in for real addresses. A network map is reconnaissance material; the reasoning is the
> part worth sharing.

The lab is watched and audited by two tools of mine:

- **[Warden](https://github.com/Noxwer-beep/warden)** — inventory, health and drift.
  Declares what should be running, checks it, and reports what changed.
- **[Vantage](https://github.com/Noxwer-beep/vantage)** — authorized recon and
  vulnerability triage, used against the deliberately vulnerable lab subnet.

## Repository structure

```text
docs/        design, configuration approach and history (Markdown)
CHANGELOG.md version history of the documentation
assets/      the mark
README.md    you are here
```

Private, not published: exact addressing, per-port VLAN assignments, the master PDF
compilation, and the engineering vault (decisions, runbooks, build logs).

### Documentation files

| File | Covers |
|---|---|
| `01_Project_Overview.md` | what the lab is for, and its scope |
| `02_Hardware_Overview.md` | the physical machine and why these parts |
| `03_Future_Roadmap.md` | what comes next |
| `04_Network_Architecture.md` | subnets, routing, where the boundaries are |
| `05_Switch_and_VLAN_Configuration.md` | the managed switch and port map (redacted) |
| `06_Proxmox_Configuration.md` | hypervisor layout, storage, VM roles |
| `07_OPNsense_Configuration.md` | firewall rules and their reasoning |
| `08_Installation_History.md` | how it was built, in order |
| `09_Issues_and_Solutions.md` | what broke and what fixed it |
| `10_Lessons_Learned.md` | what I would do differently |
| `11_Best_Practices.md` | the rules that survived contact |
| `999_Appendix_Diagrams.md` | diagrams and reference material |

## Architecture overview

- **Hypervisor** — Proxmox VE
- **Firewall / router** — OPNsense, virtualized
- **Switching** — TP-Link SG105E with VLAN segmentation
- **Lab VLAN** — isolated network, `<lab-subnet>/24`
- **Storage** — NVMe boot disk, planned ZFS mirror on HDDs
- **Planned services** — DNS filtering (Pi-hole/AdGuard), monitoring
  (Prometheus/Grafana), NAS (TrueNAS SCALE), an AD pentest range

The documentation covers both the *final design* and the *real-world problems*
encountered getting there, because the second part is what a rebuild actually needs.

## Goals

- A **reproducible** design, not a one-off build
- **Enterprise-like segmentation**, because that is where the learning is
- **Every major issue documented** with its fix, for future recovery
- A durable base for **security labs, automation and experiments**

## Versioning

- **MAJOR** — breaking structural changes to the documentation
- **MINOR** — new chapters, sections or major improvements
- **PATCH** — corrections, typo fixes, clarifications

Current: **1.1.0** (restructured documentation, roadmap, issues, lessons). All changes
are tracked in [`CHANGELOG.md`](CHANGELOG.md).

## Roadmap

- TrueNAS SCALE or another ZFS-based NAS
- A monitoring stack (Prometheus + Grafana)
- An isolated Active Directory range for pentesting
- Additional VLANs (IoT, guests) with proper firewalling
- Automated backups for Proxmox, OPNsense and key VMs

## Licence

Educational and personal use. If you want to reuse parts of it, open an issue first.
