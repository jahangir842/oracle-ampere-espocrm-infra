# 🚀 EspoCRM & n8n Infrastructure

![Platform](https://img.shields.io/badge/Platform-Oracle%20Cloud%20Always%20Free-blue)
![Architecture](https://img.shields.io/badge/Architecture-ARM64%20\(Ampere%20A1\)-orange)
![Docker](https://img.shields.io/badge/Container-Docker-blue)
![Automation](https://img.shields.io/badge/Automation-n8n-red)
![CRM](https://img.shields.io/badge/CRM-EspoCRM-green)
![Status](https://img.shields.io/badge/Status-Production--Ready-brightgreen)
![License](https://img.shields.io/badge/License-MIT-lightgrey)

A fully self-hosted, **Lead generation and CRM platform**.
Built for **automation, reliability, and scalability** without recurring infrastructure costs.

---

## 🧱 Architecture Diagram

```mermaid
flowchart TD

    A[Lead Source\n(Google Sheets / Webhook)] --> B[n8n Workflow Engine]

    B --> C{Duplicate Check}
    C -->|Exists| D[Skip / Update CRM]
    C -->|New Lead| E[Smart Enrichment\nApollo API]

    E --> F[Lead Scoring Logic]

    F -->|Qualified| G[EspoCRM]
    F -->|Not Qualified| H[Discard / Log]

    G --> I[Sendpilot API\n(Outreach)]
    I --> J[LinkedIn Campaign]

    B --> K[Notifications\nSlack / Telegram / Discord]

    subgraph Infra
        L[Traefik / Nginx\nReverse Proxy + SSL]
        M[PostgreSQL DB]
        N[Uptime Kuma]
        O[Portainer]
    end

    B --> M
    G --> M

    L --> B
    L --> G
    L --> N
    L --> O
```

---

## ⚡ Tech Stack

| Layer          | Tool                                    |
| -------------- | --------------------------------------- |
| Infrastructure | Oracle Cloud (Ubuntu ARM64 – Ampere A1) |
| Automation     | n8n                                     |
| CRM            | EspoCRM                                 |
| Database       | PostgreSQL 15                           |
| Monitoring     | Uptime Kuma                             |
| Management     | Portainer CE                            |
| Reverse Proxy  | Traefik / Nginx                         |
| SSL            | Let’s Encrypt                           |

---

## ✨ Features

### 🔄 Automation First

* End-to-end lead pipeline (capture → enrich → qualify → CRM → outreach)
* API-efficient enrichment logic (saves credits)
* Built-in duplicate prevention

### 🌐 Production Ready

* Reverse proxy with automatic SSL
* Clean subdomain routing:

  * `n8n.yourdomain.com`
  * `crm.yourdomain.com`
  * `status.yourdomain.com`
  * `portainer.yourdomain.com`

### 🛡️ Reliability

* Docker auto-restart (`restart: always`)
* Oracle idle prevention cron job
* External uptime monitoring endpoint

### 💾 Backup System

* Daily automated DB backups
* Supports Google Drive / AWS S3
* Restore instructions included

### 🎯 Lead Quality Control

* Scoring before outreach
* Example filters:

  * LinkedIn URL required
  * Company data required
  * UK-based leads preferred

### 🔍 Manual Approval (Optional)

* Toggleable review step inside n8n
* Simple condition-based control

---

## ⚙️ Quick Start

### 1️⃣ Clone the Repo

```bash
git clone https://github.com/YOUR-USERNAME/oracle-ampere-espocrm-infra.git
cd oracle-ampere-espocrm-infra
```

### 2️⃣ Setup Environment

```bash
cp .env.example .env
# Update credentials and secrets
```

### 3️⃣ Start Services

```bash
docker compose up -d
```

---

## 🌐 Local Access

| Service     | URL                                            |
| ----------- | ---------------------------------------------- |
| Portainer   | [http://localhost:9000](http://localhost:9000) |
| EspoCRM     | [http://localhost:8080](http://localhost:8080) |
| n8n         | [http://localhost:5678](http://localhost:5678) |
| Uptime Kuma | [http://localhost:3001](http://localhost:3001) |

---

## 🔁 Master Workflow (n8n)

### Flow Summary

1. **Lead Capture**

   * Google Sheets trigger or Webhook

2. **Duplicate Check**

   * Query EspoCRM before processing

3. **Smart Enrichment**

   * Apollo API (only if required fields missing)

4. **Lead Scoring**

   * Filters applied before outreach

5. **CRM Sync**

   * Create / update lead in EspoCRM

6. **Outreach Trigger**

   * Send to Sendpilot via API

7. **Notifications**

   * Slack / Telegram / Discord alerts

---

## 📦 Deployment Deliverables

* ✅ Single Docker Compose setup
* ✅ HTTPS (Let’s Encrypt SSL)
* ✅ Persistent storage (Docker volumes)
* ✅ Fully working n8n workflow
* ✅ Backup & restore system
* ✅ Handover documentation / Loom guide

---

## 🧪 Data Persistence Guarantee

All services use Docker volumes:

* PostgreSQL data persists across restarts
* n8n workflows محفوظ (safe)
* CRM data retained even after server reboot

---

## 🧠 Use Cases

* Lead generation automation systems
* Outreach pipelines for agencies
* Zero-cost CRM setups for startups
* Self-hosted DevOps learning labs

---

## 🛠️ Requirements

* Docker & Docker Compose
* Oracle Cloud Free Tier account
* Domain name (for subdomains + SSL)
* Basic Linux knowledge

---

## 📩 Hiring / Contribution

When applying or contributing:

* Start your message with **"AMPERE"**
* Share similar systems you’ve built
* Highlight relevant DevOps / automation experience

---

## Depoyment to Oracle

### **1. The Production `.env` File**
Because we are routing actual domains now, your `.env` file needs a few extra variables. Update your `.env.example` file in the repo to look like this:

```ini
# System Configuration
ACME_EMAIL=your-email@example.com # Required for Let's Encrypt SSL expiry notices

# Domain Names (Replace with your actual subdomains)
N8N_DOMAIN=n8n.yourdomain.com
CRM_DOMAIN=crm.yourdomain.com
PORTAINER_DOMAIN=portainer.yourdomain.com
STATUS_DOMAIN=status.yourdomain.com

# PostgreSQL Database Credentials
DB_USER=crm_admin
DB_PASSWORD=supersecret_db_password
DB_NAME=espocrm_db

# n8n Configuration
N8N_ENCRYPTION_KEY=generate_a_random_string_here
```

### **2. The Production `docker-compose.yml`**
Change the docker images to ARM64-compatible containers, links them to the database, and sets up all the Traefik routing rules for your subdomains.

```yaml
version: '3.8'

services:
  # Traefik Reverse Proxy (Handles SSL & Subdomains)
  traefik:
    image: traefik:v2.10
    container_name: traefik
    restart: always
    command:
      - "--api.insecure=false"
      - "--providers.docker=true"
      - "--providers.docker.exposedbydefault=false"
      - "--entrypoints.web.address=:80"
      - "--entrypoints.websecure.address=:443"
      # Global HTTP -> HTTPS Redirect
      - "--entrypoints.web.http.redirections.entryPoint.to=websecure"
      - "--entrypoints.web.http.redirections.entryPoint.scheme=https"
      # Let's Encrypt Configuration
      - "--certificatesresolvers.myresolver.acme.tlschallenge=true"
      - "--certificatesresolvers.myresolver.acme.email=${ACME_EMAIL}"
      - "--certificatesresolvers.myresolver.acme.storage=/letsencrypt/acme.json"
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock:ro"
      - "traefik_letsencrypt:/letsencrypt"
    networks:
      - proxy_net

  # Database
  postgres:
    image: postgres:15-alpine
    container_name: postgres_db
    restart: always
    environment:
      POSTGRES_USER: ${DB_USER}
      POSTGRES_PASSWORD: ${DB_PASSWORD}
      POSTGRES_DB: ${DB_NAME}
    volumes:
      - db_data:/var/lib/postgresql/data
    networks:
      - internal_net

  # CRM
  espocrm:
    image: espocrm/espocrm:latest
    container_name: espocrm
    restart: always
    environment:
      ESPOCRM_DATABASE_PLATFORM: Postgresql
      ESPOCRM_DATABASE_HOST: postgres
      ESPOCRM_DATABASE_USER: ${DB_USER}
      ESPOCRM_DATABASE_PASSWORD: ${DB_PASSWORD}
      ESPOCRM_DATABASE_NAME: ${DB_NAME}
      ESPOCRM_SITE_URL: "https://${CRM_DOMAIN}"
    volumes:
      - espocrm_data:/var/www/html
    networks:
      - internal_net
      - proxy_net
    depends_on:
      - postgres
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.espocrm.rule=Host(`${CRM_DOMAIN}`)"
      - "traefik.http.routers.espocrm.entrypoints=websecure"
      - "traefik.http.routers.espocrm.tls.certresolver=myresolver"
      - "traefik.http.services.espocrm.loadbalancer.server.port=80"

  # Automation Engine
  n8n:
    image: docker.n8n.io/n8nio/n8n:latest
    container_name: n8n
    restart: always
    environment:
      - N8N_ENCRYPTION_KEY=${N8N_ENCRYPTION_KEY}
      - WEBHOOK_URL=https://${N8N_DOMAIN}/
      - GENERIC_TIMEZONE=Europe/London
    volumes:
      - n8n_data:/home/node/.n8n
    networks:
      - internal_net
      - proxy_net
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.n8n.rule=Host(`${N8N_DOMAIN}`)"
      - "traefik.http.routers.n8n.entrypoints=websecure"
      - "traefik.http.routers.n8n.tls.certresolver=myresolver"
      - "traefik.http.services.n8n.loadbalancer.server.port=5678"

  # Monitoring
  uptime-kuma:
    image: louislam/uptime-kuma:1
    container_name: uptime_kuma
    restart: always
    volumes:
      - uptime_kuma_data:/app/data
    networks:
      - proxy_net
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.uptimekuma.rule=Host(`${STATUS_DOMAIN}`)"
      - "traefik.http.routers.uptimekuma.entrypoints=websecure"
      - "traefik.http.routers.uptimekuma.tls.certresolver=myresolver"
      - "traefik.http.services.uptimekuma.loadbalancer.server.port=3001"

  # Management Dashboard
  portainer:
    image: portainer/portainer-ce:latest
    container_name: portainer
    restart: always
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - portainer_data:/data
    networks:
      - proxy_net
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.portainer.rule=Host(`${PORTAINER_DOMAIN}`)"
      - "traefik.http.routers.portainer.entrypoints=websecure"
      - "traefik.http.routers.portainer.tls.certresolver=myresolver"
      - "traefik.http.services.portainer.loadbalancer.server.port=9000"

# Persistent Storage Volumes
volumes:
  traefik_letsencrypt:
  db_data:
  espocrm_data:
  n8n_data:
  uptime_kuma_data:
  portainer_data:

# Networks: One for internal DB chatter, one for public web proxy
networks:
  internal_net:
    driver: bridge
  proxy_net:
    driver: bridge
```

### **A Crucial DevOps Note for Oracle Deployment**

When you eventually deploy this on the Oracle instance, Traefik will handle all the reverse proxying and SSL generation automatically. However, **Let's Encrypt will fail to generate certificates if ports 80 and 443 are blocked**.

Before running `docker compose up -d` on the live server, you must ensure two things are done:

1.  **DNS Records:** Create `A Records` in your domain registrar for `n8n`, `crm`, `status`, and `portainer` pointing to the Oracle public IP.
2.  **Oracle Firewall:** Go into the Oracle Cloud Console -> Virtual Cloud Networks -> Default Security List -> Add Ingress Rules for TCP ports `80` and `443`.
3.  **Ubuntu Iptables:** Oracle's default Ubuntu images have local firewall rules that block web traffic. You will need to open them natively on the machine via your terminal:
    ```bash
    sudo iptables -I INPUT 6 -m state --state NEW -p tcp --dport 80 -j ACCEPT
    sudo iptables -I INPUT 6 -m state --state NEW -p tcp --dport 443 -j ACCEPT
    sudo netfilter-persistent save
    ```

---

