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

I am on the Apollo Free Tier, which only gives me 900 credits per year (~75/month).
Because credits are so limited, please build the "Master Workflow" with the following Triple-Gate Logic to protect my account:
1. The Deduplication Gate (CRM Check First):
Before calling the Apollo API, n8n must query EspoCRM.
If the Lead/Email already exists in our CRM, STOP. Do not call Apollo.
Use the existing data to trigger the Sendpilot outreach.
2. The Qualification Gate (Lead Scoring):
We are targeting Private Investors. Please add a "Filter" node that only proceeds to Apollo if the lead meets these exact criteria:
Job Title contains: "Partner", "Director", "Angel", "VC", or "Family Office".
Location: United Kingdom (preferred).
If they don't match, log them as "Low Priority" in the CRM and DO NOT use an Apollo credit.
3. The "Daily Drip" & Hard Ceiling:
To prevent accidental credit exhaustion, please add a Limit/Function node to the workflow:
Daily Cap: Maximum of 3 Apollo API calls per day.
Alert: If the daily limit is reached, send me a Slack/Telegram notification and queue the remaining leads for the next day.

4. API Node Configuration:
Please use the "Reveal" or "Enrichment" API call, not an "Export" call (to keep credit costs at 1 per lead).
Ensure we are only pulling the LinkedIn URL and Primary Email. We do not need mobile numbers (which cost 8x more credits).
