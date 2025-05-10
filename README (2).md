# 🧩 Jellyfin Media Server Setup (Ubuntu Server 24.04 + Docker)

This guide documents how I built a **self-hosted media server** using [Jellyfin](https://jellyfin.org/) in a **VirtualBox VM** running **Ubuntu Server 24.04**, with **Docker Compose**. This setup is intended for **local network access only**.

---

## 🚀 Stack Overview

| Component      | Purpose                               |
|----------------|----------------------------------------|
| VirtualBox     | VM host on local machine               |
| Ubuntu Server 24.04 | Lightweight OS inside VM         |
| Docker + Compose | Containerized Jellyfin deployment    |
| Jellyfin       | Media server for streaming content     |

---

## 🛠️ Installation Steps

### 1. Install Ubuntu Server in VirtualBox

- Create a VM in VirtualBox
- Allocate resources (e.g., 2 CPUs, 2GB+ RAM, bridged networking)
- Install Ubuntu Server 24.04 (minimal install is fine)

---

### 2. Install Docker & Docker Compose

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install docker.io docker-compose -y
sudo usermod -aG docker $USER
```

> ⚠️ Reboot or log out/log in to apply Docker group permissions.

---

### 3. Create Docker Compose Setup

```bash
mkdir ~/jellyfin
cd ~/jellyfin
nano docker-compose.yml
```

Paste the following:

```yaml
version: "3.8"

services:
  jellyfin:
    image: jellyfin/jellyfin
    container_name: jellyfin
    ports:
      - "8096:8096"
    volumes:
      - ./config:/config
      - ./cache:/cache
      - /path/to/your/media:/media
    restart: unless-stopped
```

> Replace `/path/to/your/media` with your actual media directory path (e.g. `/home/youruser/media`)

---

### 4. Start Jellyfin

```bash
cd ~/jellyfin
docker-compose up -d
```

---

## 📺 Access Jellyfin (Local Network Only)

- Find the IP address of your VM:  
  ```bash
  ip a
  ```
- Visit Jellyfin in a web browser from your host or other devices on the same LAN:
  ```
  http://<VM-IP>:8096
  ```

---

## 🔒 Security Tips

- Use strong passwords
- Keep Jellyfin and Docker updated
- Require login for all libraries in Jellyfin settings

---

## 🧼 Useful Commands

| Command | Description |
|--------|-------------|
| `docker-compose up -d` | Start services |
| `docker-compose down`  | Stop services |
| `docker-compose pull`  | Update images |
| `docker logs jellyfin` | View Jellyfin logs |

---

## 📜 License

MIT

---

## 🤝 Acknowledgments

- [Jellyfin](https://jellyfin.org/)
- [Docker](https://docker.com/)
