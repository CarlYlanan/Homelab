# 🏠 Carl Ylanan's Homelab Server

This repository documents my personal homelab running on a repurposed ASUS TUF A15 laptop. It hosts containerized services for media, monitoring, storage, and AI tooling — all managed via **Portainer** on Ubuntu Desktop.

---

## 📊 System Overview

| **Component**     | **Details**                                     |
|------------------|-------------------------------------------------|
| **Host OS**       | Ubuntu 24.04.2 LTS x86_64                           |
| **Hardware**       | ASUS TUF A15 — Ryzen 7 4800H, GTX 1660 Ti Mobile |
| **Storage**       | 2TB + 4TB external USB HDDs                     |
| **Docker**        | Managed via Portainer (Docker CE)               |
| **Networking**    | Samba + Xiaomi AX3000 + NetGear NightHawk Rax50v2  |
| **Exposure**      | Only Plex is externally accessible via firewall |

---

## 📁 Applications List

| **Category**       | **App**             | **Description**                            |
|--------------------|---------------------|--------------------------------------------|
| Media              | Plex, Jellyfin       | Media streaming                            |
| Download Tools     | Deluge, Radarr, Sonarr, Prowlarr | Full *Arr stack for media automation |
| Requests           | Ombi                | Media request frontend                     |
| Monitoring         | Glances, Prometheus, Grafana, Scrutiny | System metrics + drive health       |
| Analytics          | Tautulli            | Plex usage analytics                       |
| Documents          | Docmost, Joplin      | Document and notes management              |
| Photos             | Immich              | Photo/video backups                        |
| AI Tools           | Ollama, OpenWebUI   | Local LLM access via web UI                |
| DNS Filtering      | AdGuard Home        | Network-wide ad/tracker blocking           |

---

## 🔌 Step 1: Mount Drives & Samba Setup

### 1. Create mount points
```bash
sudo mkdir /mnt/2TB /mnt/4TB
```

### 2. Get UUIDs
```bash
sudo blkid
```

### 3. Edit fstab for persistent mounts
```bash
sudo nano /etc/fstab
```
Example entries:
```
UUID=XXXX-XXXX /mnt/2TB exfat defaults,uid=65534,gid=65534,fmask=0000,dmask=0000 0 0
UUID=YYYY-YYYY /mnt/4TB exfat defaults,uid=65534,gid=65534,fmask=0000,dmask=0000 0 0
```

### 4. Set permissions
```bash
sudo chown -R nobody:nogroup /mnt/2TB /mnt/4TB
sudo chmod -R 777 /mnt/2TB /mnt/4TB
```

### 5. Install packages and Samba
```bash
sudo apt install -y exfat-fuse samba
```

### 6. Configure Samba shares
```bash
sudo nano /etc/samba/smb.conf
```
Append:
```
[2TB]
  path = /mnt/2TB
  guest ok = yes
  writable = yes
  browseable = yes
  force user = nobody
  create mask = 0777
  directory mask = 0777

[4TB]
  path = /mnt/4TB
  guest ok = yes
  writable = yes
  browseable = yes
  force user = nobody
  create mask = 0777
  directory mask = 0777
```

### 7. Finalize setup
```bash
sudo systemctl restart smbd
sudo ufw allow Samba
```

---

## Step 2: Install Docker & Portainer

### 1. Update and install essentials
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y curl wget git htop net-tools gparted gnome-disk-utility \
  apt-transport-https ca-certificates gnupg2 software-properties-common
```

### 2. Install Docker
```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker $USER
```

> Log out and back in (or reboot) to apply Docker group permissions.

### 3. Deploy Portainer
```bash
docker volume create portainer_data
docker run -d -p 8000:8000 -p 9443:9443 \
  --name portainer \
  --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v portainer_data:/data \
  portainer/portainer-ce:lts
```

Access Portainer at: [https://localhost:9443](https://localhost:9443)

---

## Step 3: Deploy Apps with Portainer

1. Open [https://localhost:9443](https://localhost:9443)  
2. Go to **Stacks → Add Stack**  
3. Paste the `docker-compose.yml` from this repo  
4. Adjust volume paths to use `/mnt/2TB` or `/mnt/4TB`  
5. Click **Deploy the Stack**

## AdGuard Alterations

sudo nano /etc/systemd/resolved.conf
#DNSStubListener=yes - uncomment and change to no
-> DNSStubListener=no

sudo rm /etc/resolv.conf

echo "nameserver 1.1.1.1" | sudo tee /etc/resolv.conf

docker compose up -d

## Excluded Filters
Dandelion Sprout's Anti Push
No Google
HaGeZi's The World's Most Abused TLDs
Anti-Piracy
Shadowwhisperer List
1Hosts (Pro)
HaGeZi's Ultimate BlockList

---
