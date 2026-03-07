# Standalone Cloudflare Tunnel with Auto-Update 🛡️

This repository contains a standalone Docker Compose configuration for running a Cloudflare Tunnel securely. It is designed to act as a secure gateway for web applications (like our SaaS POS system) without exposing any server ports to the public internet.

This architecture ensures high security and stability by isolating the tunnel infrastructure from the main application stack.

## ✨ Features

* **Zero Trust Architecture:** No inbound ports need to be opened on the server firewall.
* **Standalone Deployment:** Runs independently of the main application containers, allowing for isolated maintenance.
* **Auto-Updating:** Integrated with `Watchtower` to automatically check for and apply updates to the Cloudflare image every 24 hours (86400 seconds), ensuring you always have the latest security patches.
* **Self-Healing:** Containers are configured to restart automatically on failure.

## 📋 Prerequisites

* Docker and Docker Compose installed on your server.
* An active [Cloudflare Zero Trust](https://dash.teams.cloudflare.com/) account.
* A pre-created external Docker network named `pos_network` (or modify the `docker-compose.yml` to match your network).

## 🚀 Getting Started

1. **Clone the repository:**
   ```bash
   git clone <your-repository-url>
   cd <repository-directory>

   
