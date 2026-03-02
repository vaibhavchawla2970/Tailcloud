
# 🔐 Build Your Own Zero-Trust NAS

## Nextcloud + Tailscale (No Port Forwarding. No Public Exposure.)

> A private cloud storage server accessible **only through authenticated devices** over a zero-trust mesh VPN.

No open ports.
No reverse proxy servers exposed to the internet.
No router configuration.

Just secure device-based access.

---

# 🎯 What This Project Does

This project transforms a small Ubuntu machine (stick PC, mini PC, old laptop, etc.) into a secure self-hosted cloud using:

* ☁️ **Nextcloud** — File sync, calendar, contacts, collaboration
* 🔐 **Tailscale** — Zero-trust mesh VPN with automatic HTTPS
* 🚫 **Zero public ports exposed** — Not accessible from the public internet

---

# 🏗 Architecture

```
Client Devices
       │
       ▼
Tailscale Mesh VPN (Zero-Trust Identity)
       │
       ▼
Ubuntu Server
       │
       ▼
Nextcloud (Snap)
```

```
❌ Public Internet  → NOT accessible  
✅ Authenticated Device → Full access
```

---

# 🚨 CRITICAL: Tailscale Authentication Required

Before accessing Nextcloud, **every client device must join your Tailscale network**.

### On Every Device:

1. Install Tailscale
2. Run:

```bash
tailscale up
```

3. Authenticate in browser
4. Access:

```
https://example.tail0344eb3.ts.net/
```

No Tailscale login = No access.

---

# 🛠 Tech Stack

* Ubuntu Server (20.04 / 22.04 / 24.04)
* Nextcloud Snap
* Tailscale Mesh VPN
* Tailscale Serve (automatic HTTPS)

---

# 🚀 Quick Start (≈15 Minutes)

## ✅ Prerequisites

* Fresh Ubuntu Server
* Internet connection
* Free Tailscale account

---

# 🧩 Installation Guide

Run as root or with `sudo`.

---

## 1️⃣ Update System

```bash
apt update && apt upgrade -y
apt install openssh-server snapd curl apt-transport-https -y
```

---

## 2️⃣ Install Nextcloud

```bash
snap install nextcloud
snap services nextcloud
```

All services should show **Active**.

---

## 3️⃣ Install Tailscale

### Option A — Official Repository (Recommended)

```bash
curl -fsSL https://pkgs.tailscale.com/stable/ubuntu/jammy.noarmor.gpg | \
  tee /usr/share/keyrings/tailscale-archive-keyring.gpg >/dev/null

curl -fsSL https://pkgs.tailscale.com/stable/ubuntu/jammy.tailscale-keyring.list | \
  tee /etc/apt/sources.list.d/tailscale.list

apt update
apt install tailscale -y
```

### Option B — Direct Download (Latest Version)

Official Linux download page:

👉 [https://tailscale.com/download/linux](https://tailscale.com/download/linux)

---

## 4️⃣ Authenticate Server

```bash
tailscale up
tailscale ip
tailscale status
```

Note your:

* Tailscale IP → `100.x.x.x`
* MagicDNS hostname → `example.tail0344eb3.ts.net`

---

## 5️⃣ Initial Nextcloud Setup

Find your LAN IP:

```bash
ip a
```

Open in browser:

```
http://YOUR_LAN_IP
```

Create your admin account.

---

## 6️⃣ Configure Nextcloud for Reverse Proxy

```bash
snap connect nextcloud:network-control

snap run nextcloud.occ config:system:set trusted_domains 0 --value="localhost"
snap run nextcloud.occ config:system:set trusted_domains 1 --value="YOUR_TAILSCALE_IP"
snap run nextcloud.occ config:system:set trusted_domains 2 --value="example.tail0344eb3.ts.net"

snap run nextcloud.occ config:system:set trusted_proxies 0 --value=127.0.0.1
snap run nextcloud.occ config:system:set overwriteprotocol --value=https

snap restart nextcloud
```
![Tailscale Dashboard](https://i.postimg.cc/HLBFkQ93/Screenshot_2026_03_01_181158.png)
Replace:

```
YOUR_TAILSCALE_IP → 100.x.x.x
```

---

## 7️⃣ Enable HTTPS with Tailscale Serve

```bash
tailscale serve --reset
tailscale serve --bg --https=443 http://localhost:80
```

This maps:

```
https://example.tail0344eb3.ts.net/
        ↓
http://localhost:80
```

Automatic TLS certificates included.

---

# ✅ Access Your Private Cloud

## Step 1 — Install Tailscale on Client Device

### Desktop (Windows / macOS / Linux)

```bash
tailscale up
```

### Android / iOS

* Install Tailscale app
* Login with same account
* Connect

![TailScale Dashboard](https://i.postimg.cc/VNY3w7nD/Screenshot_2026_03_01_1811ssf58.png)

---

## Step 2 — Access Nextcloud

```
https://example.tail0344eb3.ts.net/
```
![NextCloud Dashboard](https://i.postimg.cc/63qg2yHJ/Screenshot_2026_03_01_182029.png)

Use this same URL for:

* Nextcloud Desktop Client
* Nextcloud Mobile App

---

# 🔒 Security Features

| Feature                 | Status | Description                      |
| ----------------------- | ------ | -------------------------------- |
| Zero Public Ports       | ✅      | No router configuration required |
| Device Authentication   | ✅      | Identity-based access control    |
| HTTPS Encryption        | ✅      | Automatic TLS certificates       |
| Zero-Trust Networking   | ✅      | Only authenticated devices       |
| Trusted Proxy Hardening | ✅      | Secure reverse proxy headers     |

---

# 🧪 Troubleshooting

| Problem                  | Solution                           |
| ------------------------ | ---------------------------------- |
| Connection refused       | Install & login to Tailscale first |
| Not secure warning       | Use exact `https://` URL           |
| Untrusted domain error   | Check `trusted_domains`            |
| Tailscale not connecting | Run `tailscale status`             |
| HTTPS issues             | Verify time with `timedatectl`     |

---

# 🔄 Maintenance

## Update Everything

```bash
apt update && apt upgrade -y && snap refresh
```

## Check Services

```bash
snap services nextcloud
tailscale status
```

## View Logs

```bash
snap run nextcloud.occ log:tail
```

## Backup Config

```bash
snap run nextcloud.export
```

---

# 🎓 What I Learned

### 🔐 Zero-Trust > Port Forwarding

Tailscale eliminates router configuration and exposed firewall holes.

### ⚡ Snap + Tailscale Serve = Instant HTTPS

One command → Secure reverse proxy → Automatic TLS.

### 🛡 Device Identity > IP Rules

Authentication is stronger than relying on exposed ports.

### 🔁 Reverse Proxy Hardening Matters

Correct `trusted_proxies` and `overwriteprotocol` configuration is essential.

---

# 🎥 Inspiration

This project was inspired by:

* 📺 **How to Self-Host Nextcloud with Tailscale**

The tutorial introduced the idea of combining Nextcloud with Tailscale for secure remote access without port forwarding.

This repository expands the concept with:

* Production-ready configuration
* Hardened reverse proxy setup
* Clean 15-minute deployment workflow
* Security-first documentation

Huge credit to the creator for the original concept 🙌

---

# 📈 Future Improvements

* [ ] External SSD storage mount
* [ ] Automated offsite backups
* [ ] Fail2ban brute-force protection
* [ ] Monitoring with Prometheus + Grafana
* [ ] Automated update notifications

---

# 👨‍💻 Author

**Vaibhav Chawla**

🔗 LinkedIn: [Me](https://www.linkedin.com/in/vaibhavchawla297)

---

# 🎥 Inspired by

📺 YouTube Tutorial: [Access Your Files ANYWHERE You Go — The Ultimate Pi 5 Setup](https://www.youtube.com/watch?v=jOYG10CvZZA)

---

# ⭐ Support

If this helped you, consider starring the repository.

---

# 🚀 Deploy Your Own

Copy → Paste → Done in 15 minutes.

Build your own secure cloud.
No subscriptions.
No exposed ports.
No compromises.
