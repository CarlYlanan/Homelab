# Carl Ylanan's Homelab Server

This repository documents my personal homelab setup, running multiple containerized services on a repurposed ASUS TUF A15 laptop. The system is built for media hosting, file management, monitoring, and light experimentation with AI tooling — all using Docker on Ubuntu Desktop.

---

## Overview

The homelab is powered by Docker Compose and focuses on three key principles:

- Simplicity and maintainability  
- Secure local network access  
- Minimal external exposure  

Services are split across categories such as media streaming, downloads, monitoring, productivity, and self-hosted tools.

---

## System Information

**Host OS:** Ubuntu Desktop (LTS)  
**Hardware:** ASUS TUF A15  
**CPU:** Ryzen 7 4800H  
**GPU:** GTX 1660 Ti Mobile  
**Storage:** External USB drives (2TB+ total)  
**Networking:** Custom Docker bridge network + Samba file sharing  
**Access Control:** Only Plex is exposed to the internet through strict firewall rules

---

## Installed Applications

| Category         | Application   | Description                                    |
|------------------|---------------|------------------------------------------------|
| Media Servers    | Plex          | Personal media streaming server               |
|                  | Jellyfin      | Open-source alternative to Plex               |
| Download Tools   | Deluge        | BitTorrent client                             |
|                  | Radarr        | Movie management and downloader               |
|                  | Sonarr        | TV series management and downloader           |
|                  | Prowlarr      | Indexer manager for Sonarr/Radarr             |
| Request System   | Ombi          | Media request system for Plex/Jellyfin        |
| Analytics        | Tautulli      | Plex usage tracking and analytics             |
| Monitoring       | Glances       | System resource monitoring                    |
|                  | Scrutiny      | Drive health and S.M.A.R.T. monitoring        |
|                  | Prometheus    | Metrics collection and monitoring             |
|                  | Grafana       | Prometheus dashboard visualization            |
| Productivity     | Joplin Server | Self-hosted note-taking and to-do system      |
|                  | Docmost       | Lightweight document management               |
| Photo Management | Immich        | Self-hosted photo and video management        |
| AI Utilities     | OpenWebUI     | Web interface for local AI models             |
| Network Tools    | AdGuard Home  | DNS-level ad and tracker blocking             |

---

## Installation Steps

### 1. Update and upgrade the system

```bash
sudo apt update && sudo apt upgrade -y
```

### 2. Install essential tools

```bash
sudo apt install -y curl wget git htop net-tools gparted gnome-disk-utility apt-transport-https ca-certificates gnupg2 software-properties-common
```

### 3. Install Docker

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker $USER
```

**Note:** Log out and back in (or reboot) to apply Docker group changes.

### 4. Install Docker Compose

```bash
sudo apt install -y docker-compose
```

### 5. Create base directory structure and Docker network

```bash
mkdir -p ~/homelab/{configs,data}
cd ~/homelab
docker network create homelab
```

---

## Running Services

Each app is defined in its own `docker-compose.yml` file within `~/homelab/configs/`. Services are connected to a shared `homelab` Docker network to enable internal communication. Data is persisted in the `~/homelab/data/` directory.

To start a service:

```bash
cd ~/homelab/configs/<app-name>
docker-compose up -d
```

---

## Notes

- All services are self-hosted and manually maintained.  
- The setup prioritizes local use with minimal external ports exposed.  
- File sharing is done using Samba for compatibility with Windows and macOS devices.  
- Drives are monitored regularly with Scrutiny for early failure detection.

---

## License

This repository is open for educational and personal use. Feel free to reference or build on this setup for your own homelab.
