
# Future Infrastructure Roadmap

This document outlines the strategic integration of new services into the `ghosthouse` laboratory, categorized by priority and operational dependency.

### Phase 1: Immediate Integration (Planned)
*   **Shoko Server + Shokofin:** 
    *   *Role:* Specialized anime metadata management.
    *   *Interaction:* Acts as the primary metadata provider for the `jellyfin` (LXC 102) media library. Requires read/write access to `ghostDrive` (LXC 100).
*   ~~**Tailscale:**~~ **(COMPLETED)**
    *   *Role:* Secure remote access layer.
    *   *Interaction:* Will join the `ghosthouse` lab to your existing Tailscale mesh VPN. This enables secure, cross-device access to all lab services (Webmin, Portainer, etc.) without requiring port forwarding or public firewall exposure.

### Phase 2: Services & Automation (Backlog)
*   **Immich:** 
    *   *Role:* High-performance self-hosted photo/video backup.
    *   *Interaction:* Will utilize `ghostDrive` for long-term storage of mobile device ingestion.
*   **Nextcloud:** 
    *   *Role:* Private cloud suite for file sync and productivity.
    *   *Interaction:* Will replace external cloud storage for sensitive document management and automated backups from local workstations.
*   ~~**Sonarr / Radarr:**~~ **(COMPLETED)**
    *   *Role:* Automated media acquisition workflows.
    *   *Interaction:* Dependent on `qbittorrent` (LXC 107) for file transfers and `ghostDrive` for final media delivery to `jellyfin`.
*   ~~**Komga:**~~ **(COMPLETED)**
    *   *Role:* Manga and comic book server.
    *   *Interaction:* Dedicated library reader that draws from `ghostDrive` collections.
*   **Nginx Proxy Manager:**
    *    *Role:* Network reverse proxy and SSL certificate management.
    *    *Interaction:* Consolidates internal routing by directing local traffic or external requests to the correct LXC ports, providing a streamlined access layer without memorizing individual IP addresses.
*   **Mealie:** 
    *   *Role:* Recipe management and kitchen planning.
    *   *Interaction:* Web-based service to track household nutrition data; will eventually integrate with localized backup scripts for database protection.

### Phase 3: Hardware Expansion & Local AI Engine (Future)
*   **Local AI Smart Home Assistant:**
    *   *Role:* Offline, privacy-first voice assistant and natural language automation engine.
    *   *Interaction:* Interfaces directly with Home Assistant via the Wyoming Protocol. Requires a dedicated low-profile PCIe GPU installed in the host server to execute localized Ollama LLM queries, Faster-Whisper STT transcription, and custom TTS audio synthesis. Captures audio via ESP32-S3 hardware room nodes.

### Checklist: Local AI Smart Home Assistant

This project introduces a fully localized, privacy-first voice assistant integrated seamlessly with Home Assistant. The architecture relies on an offline Large Language Model (LLM) for processing natural language commands and smart home function calling, bridging the gap between standard home automation and conversational AI without relying on cloud infrastructure.

**Architecture & Pipeline Breakdown**

*   **Core Orchestration:** Home Assistant handling localized device integration, internal network automations, and Wyoming Protocol routing.
*   **Compute Engine:** Hardware GPU upgrade for the Dell OptiPlex (e.g., Low-Profile NVIDIA RTX 3050 / A2000) to natively power localized AI processing.
*   **Local Language Model (LLM):** Ollama deployment to process contextual requests, determine intent, and trigger home automation scripts via function calling.
*   **Speech-to-Text (STT):** Faster-Whisper engine for rapid, multilingual transcription, seamlessly detecting and parsing English and Japanese audio input.
*   **Text-to-Speech (TTS):** Dedicated voice synthesis engine configured with a custom vocal profile for dynamic, localized audio generation and personality emulation.
*   **Hardware Satellites:** ESP32-S3 open-source microcontrollers (via ESPHome) deployed as physical room nodes, utilizing localized wake-word detection to trigger the assistant pipeline.

**Deployment Trajectory**

- [ ] Procure and install low-profile PCIe GPU into the OptiPlex.
- [ ] Deploy Home Assistant & establish integration layer.
- [ ] Configure Ollama instance & configure Home Assistant function calling.
- [ ] Configure Faster-Whisper for offline, multilingual STT processing.
- [ ] Implement custom-profile TTS engine for synthesized audio feedback.
- [ ] Flash and deploy ESP32-S3 room satellites for localized wake-word detection.
