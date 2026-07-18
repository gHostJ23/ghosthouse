
# Future Infrastructure Roadmap

This document outlines the strategic integration of new services into the `ghosthouse` laboratory, categorized by priority and operational dependency.

### Phase 1: Immediate Integration (Planned)
*   **Shoko Server + Shokofin:** 
    *   *Role:* Specialized anime metadata management.
    *   *Interaction:* Acts as the primary metadata provider for the `jellyfin` (LXC 102) media library. Requires read/write access to `ghostDrive` (LXC 100).
*   **Tailscale:** 
    *   *Role:* Secure remote access layer.
    *   *Interaction:* Will join the `ghosthouse` lab to your existing Tailscale mesh VPN. This enables secure, cross-device access to all lab services (Webmin, Portainer, etc.) without requiring port forwarding or public firewall exposure.

### Phase 2: Services & Automation (Backlog)
*   **Immich:** 
    *   *Role:* High-performance self-hosted photo/video backup.
    *   *Interaction:* Will utilize `ghostDrive` for long-term storage of mobile device ingestion.
*   **Nextcloud:** 
    *   *Role:* Private cloud suite for file sync and productivity.
    *   *Interaction:* Will replace external cloud storage for sensitive document management and automated backups from local workstations.
*   **Sonarr / Radarr:** 
    *   *Role:* Automated media acquisition workflows.
    *   *Interaction:* Dependent on `qbittorrent` (LXC 107) for file transfers and `ghostDrive` for final media delivery to `jellyfin`.
*   **Komga:** 
    *   *Role:* Manga and comic book server.
    *   *Interaction:* Dedicated library reader that draws from `ghostDrive` collections.
*   **Mealie:** 
    *   *Role:* Recipe management and kitchen planning.
    *   *Interaction:* Web-based service to track household nutrition data; will eventually integrate with localized backup scripts for database protection.
