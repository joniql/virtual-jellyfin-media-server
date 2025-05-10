# 🧩 Jellyfin Media Server Setup (Ubuntu Server 24.04 + Docker + Caddy)

This guide documents how I built a **self-hosted media server** using [Jellyfin](https://jellyfin.org/) in a **VirtualBox VM** running **Ubuntu Server 24.04**, with **Docker Compose** and secure **HTTPS access via Caddy + Let's Encrypt**.

---

## 🚀 Stack Overview

| Component      | Purpose                               |
|----------------|----------------------------------------|
| VirtualBox     | VM host on local machine               |
| Ubuntu Server 24.04 | Lightweight OS inside VM         |
| Docker + Compose | Containerized Jellyfin deployment    |
| Jellyfin       | Media server for streaming content     |
| Caddy          | Reverse proxy with automatic HTTPS     |
| Let's Encrypt  | Free SSL certificates                  |
| DuckDNS (or domain) | Remote access to server           |

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
    network_mode: "internal"
    volumes:
      - ./config:/config
      - ./cache:/cache
      - /path/to/your/media:/media
    restart: unless-stopped

  caddy:
    image: caddy:latest
    container_name: caddy
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile
      - caddy_data:/data
      - caddy_config:/config
    depends_on:
      - jellyfin
    network_mode: "internal"
    restart: unless-stopped

volumes:
  caddy_data:
  caddy_config:

networks:
  default:
    name: internal
    driver: bridge
```

---

### 4. Create the Caddyfile (Reverse Proxy Config)

```bash
nano Caddyfile
```

Paste:

```
media.yourdomain.com {
    reverse_proxy jellyfin:8096
}
```

> Replace `media.yourdomain.com` with your actual domain or DDNS hostname.

---

### 5. Start Jellyfin + Caddy

```bash
cd ~/jellyfin
docker-compose up -d
```

---

## 🌍 Remote Access

1. **Configure port forwarding** on your router:
   - Port `80` → VM IP
   - Port `443` → VM IP

2. **Set up Dynamic DNS (e.g., DuckDNS)**:
   - Point your domain to your public IP
   - Ensure DNS is up-to-date

3. **Visit Jellyfin** securely:
   ```
   https://media.yourdomain.com
   ```

---

## 🔒 Security Tips

- Use strong passwords
- Update Jellyfin and Docker images regularly
- Consider a VPN if exposing other services
- Monitor logs: `docker logs caddy` or `docker logs jellyfin`

---

## 🧼 Useful Commands

| Command | Description |
|--------|-------------|
| `docker-compose up -d` | Start services |
| `docker-compose down`  | Stop services |
| `docker-compose pull`  | Update images |
| `docker logs jellyfin` | View Jellyfin logs |
| `docker logs caddy`    | View Caddy logs |

---

## 📸 Screenshots *(optional)*

_Add screenshots of your Jellyfin dashboard, Caddy log, or directory structure here._

---

## 📜 License

MIT

---

## 🤝 Acknowledgments

- [Jellyfin](https://jellyfin.org/)
- [Docker](https://docker.com/)
- [Caddy](https://caddyserver.com/)
- [Let's Encrypt](https://letsencrypt.org/)
