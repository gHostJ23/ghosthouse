# 👻 ghosthouse-lab

Welcome to the official repository for **ghosthouse**, a dedicated, self-hosted home laboratory environment built for media automation, network administration, localized data security, and game servers. My own personal Linux and Network playground.

This repository tracks my infrastructure design, hypervisor configurations, network topologies, and containerized deployments as well as errors, hiccups, and troubleshooting. This also serves as a breadcrumb trail for diagnostic and re-deployability.

---

## Hardware Architecture

The backbone of the `ghosthouse` infrastructure currently relies on a e-waste recycled Dell Optiplex 7060, Cyberpower CP1500 UPS,  GL.iNet Flint 2 Open WRT router.

* **Host System:** Dell OptiPlex 7060 Mid-Tower (MT)
* **Network Infastructure:** Flint 2 OpenWRT router w/ unmanaged 2.5gb switch.
* **Hypervisor Platform:** Proxmox Virtual Environment (PVE) v9.2.3
* **Power Continuity & Telemetry:** CyberPower CP1500PFCLCDa Uninterruptible Power Supply (UPS). Provides localized battery backup, pure sine wave power conditioning, and automated shutdown signaling. Currently sustains ~48 minutes of runtime under baseline infrastructure load.
* **Storage Architecture:** Local ZFS File System configurations optimized for bit-rot protection and continuous pool health evaluation. 512gb NVMe gen3. 2tb SATA SEAGATE EXOS Refurbished.
* **Active Storage Pools:**
    * `Vault (ghosthouse)`: High-security data storage tier.
    * `ghost_Drive (ghosthouse)`: Primary high-capacity storage engine for network shares and media arrays.
    * `local / local-lvm (ghosthouse)`: High-speed local system block storage allocated for hypervisor root files and virtual disk backends.

---

## Network Configuration

* **Primary Subnet:** `192.168.8.0/24`
* **Proxmox VE Web UI Portal:** `https://192.168.8.10:8006`
* **Internal Gateway Architecture:** Network DNS & Ad-Blocking: Dual-AdGuard setup. A Proxmox container acts as the primary DNS sinkhole, and the GL.iNet Flint 2 router runs a native AdGuard instance as an automatic backup to prevent internet drops during server maintenance.

---

## Virtualization Matrix & Container Deployments

The environment is built using isolated Linux Containers (LXCs) to maximize efficiency, compute density, and security boundaries.

### LXC Deployment Catalog

| CTID | Node Alias | Core Network Services / Stack Ports | Architecture & Operational Role |
| :--- | :--- | :--- | :--- |
| **100** | `ghostDrive` | SMB/Samba Share (`192.168.8.129`) | **Central Storage Layer:** Configured via TurnKey Linux File Server protocols. Completely unlocked reading/writing permissions (`chmod 777`) mapped strictly across internal clients. |
| **101** | `uptimekuma` | Web GUI Port: `3001` | **High-Availability Monitoring Engine:** Deployed using Proxmox community shell scripts backed by SQLite database file engine. Tracks heartbeat frequencies of every single node over a 60-second polling cadence with localized retry buffers. |
| **102** | `jellyfin` | Web GUI Port: `8096` | **Private Media Distribution:** Jellyfin Open-Source media deployment managing local libraries securely stream-rendered to home client hardware. |
| **103** | `adguard` | Web GUI Port: `3000` | **Local DNS & Network Shielding:** Network-wide upstream request sinkhole. Manages internal recursive DNS rules, custom path routing, and blocklists. |
| **104** | `homarr` | Web GUI Port: `7575` | **Central Command Dashboard:** Visual UI hub aggregation utilizing native widget frameworks to cleanly visualize running containers alongside server metrics. |
| **105** | `peanut` | Web GUI Port: `8090` | **Network UPS Tools:** Interfaces directly with the physical CyberPower UPS via USB passthrough. Hosts the PeaNUT UI to visualize active load, voltage curves, and battery runtime in real-time. |
| **106** | `crafty-controller` | Web GUI Port: `8443` (HTTPS) | **Game Server Orchestration:** Minecraft server administration console handling localized instance configurations, backups, and live server resource monitoring. |
| **107** | `qbittorrent` | Web GUI Port: `8090` | **Peer-to-Peer Client:** qBittorrent instance deployed for secure, localized file distribution, automation, and ISO management. |
| **108** | `docker-engine` | Web GUI Port: `9443` | **Container Orchestration Hub:** Modular Docker environment hosting Shoko Server and future application stacks. |

---

## Project Timeline & Development Changelog

Documenting the configuration milestones, deployment hurdles, and infrastructure victories for the internal LXC stack.

* **LXC 100 (`ghostDrive`):** Deployed local SMB/Samba share (`port:12321`). 
    * *Progress:* Mapped with fully unlocked permissions for frictionless internal network reading/writing. Successfully integrated into Uptime Kuma for ongoing ICMP Ping monitoring.
* **LXC 101 (`uptimekuma`):** Deployed core monitoring engine (`port: 3001`).
    * *Hiccup:* Encountered a Telegram API `400 (chat not found)` error during initial alert setup due to Telegram's anti-spam architecture blocking bots from initiating conversations.
    * *Resolution:* Manually opened a chat with the `gHost status 1` bot from a mobile client and sent a `/start` handshake, unlocking the API to successfully route `[UP]` and `[DOWN]` push notifications.
* **LXC 102 (`jellyfin`):** Deployed media streaming backend (`port: 8096`).
    * *Hiccup:* Initial Uptime Kuma monitoring threw false domain expiry warnings because the tracker was pinging a local IP instead of a public ICANN-registered domain.
    * *Resolution:* Disabled domain expiry tracking and established a 1-retry buffer threshold to completely eliminate false-positive dashboard alarms.
* **LXC 103 (`adguard`):** Established primary network-wide ad-blocking engine (`192.168.8.168`).
    * *Progress:* Upgraded the network topology into a High-Availability (HA) dual-DNS setup. Integrated the GL.iNet Flint 2 router's native AdGuard software as a seamless secondary fallback to prevent network collapse if the Proxmox host reboots. Deployed the "Big Three" blocklists (HaGeZi Multi Normal, OISD Big, StevenBlack Unified) across both instances for mirrored filtering.
* **LXC 104 (`homarr`):** Deployed central dashboard UI (`Port: 7575`).
    * *Hiccup:* Triggered an API `404 (Not found)` error when attempting to embed Uptime Kuma widgets. Homarr's integration secrets defaulted to searching for a `default` page slug, while the Kuma page was generated under the `dashboard` slug.
    * *Resolution:* Corrected the API linkage by aligning the Status Page slug nomenclature and publishing a unified monitor group within Uptime Kuma.
* **LXC 105 (`peanut`):** Deployed dedicated Uninterruptible Power Supply (UPS) telemetry tracker (`port 8080`).
    * *Progress:* Transitioned from a staging sandbox into a production Network UPS Tools (NUT) dashboard utilizing the PeaNUT UI. Successfully mapped the CyberPower CP1500PFCLCDa UPS USB passthrough to track real-time load, voltage, and runtime data.
* **LXC 106 (`crafty-controller`):** Deployed Java/Minecraft orchestration interface (`port 8443`).
    * *Hiccup:* Uptime Kuma failed to verify the service status due to Crafty Controller generating its own self-signed TLS/SSL security certificate out of the box. 
    * *Resolution:* Overrode strict TLS validation in Uptime Kuma's advanced settings, allowing the monitor to safely read the localized HTTPS traffic. (Also applied this exact resolution for the Proxmox VE root environment on port `8006`).
* **LXC 107 (`qbittorrent`):** Deployed dedicated peer-to-peer torrent client (`port: 8090`).
    * *Hiccup:* The client initially lacked the proper write permissions and completely failed to save any downloaded files to the central `ghostDrive` Samba storage.
    * *Resolution:* Deleted and re-created the instance as a **Privileged** container. This allowed qBittorrent to bypass the strict user-mapping restrictions of unprivileged containers and successfully write data directly to the network storage pool. A crucial and valuable lesson in how Proxmox handles UID/GID permission management across network shares.
* **LXC 108 (`docker-engine`):** Deployed Docker + Portainer UI (`Portaner config port: 9443`).
    * *Hiccup:* The installation process hung repeatedly at the initial menu load, failing to initialize the advanced configuration wizard.
    * *Resolution:* Performed a full browser cache refresh and shell restart, and transitioned to a default installation path to ensure environment stability and a successful container build.

---

> **Security Warning:** This repository tracks configuration framework logic and structural maps only. Production security credentials, environment tokens, private keys, and hardware MAC definitions are strictly filtered out of this public repository to safeguard the internal integrity of the `ghosthouse` network infrastructure.
>
> ---

## 🛠️ Development & Documentation Methodology
This repository and its infrastructure layout were engineered using an AI-assisted development workflow. LOCAL Large Language Models (Codestral (22B) + Qwen 2.5 (32B) Models) were utilized as strategic troubleshooting partners for optimizing container routing, parsing complex log errors, and accelerating initial markdown documentation frameworks.
