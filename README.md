# 🛡️ Standalone Cloudflare Tunnel with Auto-Update

> A standalone, self-healing, and auto-updating Cloudflare Tunnel — Zero ports exposed.

---

## 📖 Overview

This repository provides a minimal, production-ready Docker Compose setup for running a **Cloudflare Tunnel** as an isolated service. It acts as a secure gateway for any backend application (e.g., Nginx, Django, APIs) without ever exposing a port to the public internet.

It is intentionally **decoupled** from the main application stack, so you can restart, update, or debug the tunnel independently — without touching your app.

---

## ✨ Features

| Feature | Description |
|---|---|
| 🔒 **Zero Trust Architecture** | No inbound ports need to be opened on the server firewall |
| 🔄 **Auto-Update via Watchtower** | Automatically checks for and applies updates every 24 hours |
| 💊 **Self-Healing** | Containers restart automatically on failure |
| 📦 **Standalone Deployment** | Fully decoupled from your main application stack |
| 🧹 **Auto Cleanup** | Old images are removed automatically after each update |

---

## 🏗️ Architecture

```
Internet
   │
   ▼
Cloudflare Edge  ◄──── Cloudflare Tunnel (cloudflared) ◄──── Your App (Nginx/Django)
                              │
                         pos_network (shared Docker network)

└── No public ports exposed on your server ✅
```

---

## 📋 Prerequisites

- Docker & Docker Compose installed on your server
- An active [Cloudflare Zero Trust](https://dash.teams.cloudflare.com/) account
- A tunnel created and a **Tunnel Token** generated
- A pre-created external Docker network named `pos_network` (or modify `docker-compose.yml` to match your network)

---

## ⚙️ Environment Variables

Create a `.env` file in the same directory as `docker-compose.yml`:

```env
# .env

# Your Cloudflare Tunnel Token
# Dashboard → Zero Trust → Networks → Tunnels → your tunnel → Configure → Token
CLOUDFLARE_TUNNEL_TOKEN=eyJhIjoiXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX...
```

> ⚠️ **Never commit `.env` to Git.** Add it to `.gitignore`.

---

## 🚀 Getting Started

### 1. Clone the repository

```bash
git clone <your-repository-url>
cd <repository-directory>
```

### 2. Create the `.env` file

```bash
cp .env.example .env
# Edit .env and paste your Tunnel Token
```

### 3. Create the shared Docker network (if it doesn't exist)

```bash
docker network create pos_network
```

### 4. Start the services

```bash
docker compose up -d
```

### 5. Verify everything is running

```bash
docker compose ps
docker logs cloudflared_standalone
```

---

## 🔄 How Auto-Update Works

```
Watchtower (every 24h)
   │
   ├── Checks Docker Hub for a new cloudflared image
   │
   ├── If a new image is found:
   │     ├── Pull new image
   │     ├── Stop old container
   │     ├── Start new container (same config)
   │     └── Delete old image (--cleanup)
   │
   └── If no new image: do nothing ✅
```

**Downtime during update:** A few seconds only — Cloudflare reconnects automatically.

> 📌 **Note:** Watchtower does **not** update itself automatically.
> To update Watchtower manually:
> ```bash
> docker pull containrrr/watchtower
> docker compose up -d watchtower-cf
> ```

---

## 📁 Project Structure

```
.
├── docker-compose.yml   # Main configuration
├── .env                 # Secret tokens (not committed to Git)
├── .env.example         # Template for .env
└── README.md            # This file
```

---

## 🛑 Stopping the Services

```bash
# Stop and remove containers
docker compose down

# Stop without removing
docker compose stop
```

---

## 🔗 Related Resources

- [Cloudflare Zero Trust Dashboard](https://dash.teams.cloudflare.com/)
- [cloudflared Docker Image](https://hub.docker.com/r/cloudflare/cloudflared)
- [Watchtower Docs](https://containrrr.dev/watchtower/)
- [Docker Compose Docs](https://docs.docker.com/compose/)

---

## 📜 License

MIT — Feel free to use and modify.
