# 🚀 EspoCRM & n8n Automation

**Platform Architecture | Docker | Automation | CRM | Status | License**

A fully self-hosted **lead generation and CRM platform**—built for automation, reliability, and scalability, without recurring infrastructure costs.

---

## ⚡ Tech Stack

| Layer          | Tool            |
| -------------- | --------------- |
| Infrastructure | Ubuntu / Linux  |
| Automation     | n8n             |
| CRM            | EspoCRM         |
| Database       | PostgreSQL 15   |
| Monitoring     | Uptime Kuma     |
| Management     | Portainer CE    |
| Reverse Proxy  | Traefik / Nginx |
| SSL            | Let’s Encrypt   |

---

## ✨ Features

### 🔄 Automation First

* End-to-end lead pipeline: **capture → enrich → qualify → CRM → outreach**
* API-efficient enrichment logic (minimizes credit usage)
* Built-in duplicate prevention

### 🌐 Production Ready

* Reverse proxy with automatic SSL (Let’s Encrypt)
* Clean subdomain routing:

  * `n8n.yourdomain.com`
  * `crm.yourdomain.com`
  * `status.yourdomain.com`
  * `portainer.yourdomain.com`

### 🛡️ Reliability

* Docker auto-restart (`restart: always`)
* External uptime monitoring endpoint

### 💾 Backup System

* Daily automated database backups
* Supports Google Drive / AWS S3
* Restore instructions included

### 🎯 Lead Quality Control

* Pre-outreach lead scoring

**Example filters:**

* LinkedIn URL required
* Company data required
* UK-based leads preferred

### 🔍 Manual Approval (Optional)

* Toggleable review step inside n8n
* Simple condition-based control

---

## ⚙️ Quick Start

### 1️⃣ Clone the Repository

```bash
git clone https://github.com/YOUR-USERNAME/espocrm-n8n-type.git
cd espocrm-n8n-type
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

### Flow Overview

1. **Lead Capture**

   * Google Sheets trigger or Webhook

2. **Deduplication Check**

   * Query EspoCRM before processing

3. **Smart Enrichment**

   * Apollo API (only if required fields are missing)

4. **Lead Scoring**

   * Apply filters before outreach

5. **CRM Sync**

   * Create/update lead in EspoCRM

6. **Outreach Trigger**

   * Send to Sendpilot via API

7. **Notifications**

   * Slack / Telegram / Discord alerts

---

## 📦 Deployment Deliverables

* ✅ Single Docker Compose setup
* ✅ HTTPS (Let’s Encrypt SSL)
* ✅ Persistent storage (Docker volumes)
* ✅ Fully functional n8n workflow
* ✅ Backup & restore system
* ✅ Handover documentation / Loom guide

---

## 🧪 Data Persistence Guarantee

All services use Docker volumes:

* PostgreSQL data persists across restarts
* n8n workflows remain محفوظ (safe)
* CRM data retained after server reboot

---

## 🧠 Use Cases

* Lead generation automation systems
* Outreach pipelines for agencies
* Zero-cost CRM setups for startups
* Self-hosted DevOps learning labs

---

## 🛠️ Requirements

* Docker & Docker Compose
* Domain name (for subdomains + SSL)
* Basic Linux knowledge

---

## 📩 Hiring / Contribution

When applying or contributing:

* Start your message with **"TYPE"**
* Share similar systems you’ve built
* Highlight relevant DevOps / automation experience

---

# 🧠 Apollo Credit Optimization (Triple-Gate Logic)

Since Apollo Free Tier is limited (~75 credits/month), the workflow enforces strict usage control:

---

### 1️⃣ Deduplication Gate (CRM First)

* Query EspoCRM before Apollo
* If lead/email exists → **STOP**
* Trigger outreach using existing data

---

### 2️⃣ Qualification Gate (Lead Scoring)

Only proceed if:

* Job Title contains:
  `Partner`, `Director`, `Angel`, `VC`, `Family Office`
* Location: **United Kingdom (preferred)**

If not:

* Mark as **Low Priority** in CRM
* ❌ Do NOT call Apollo

---

### 3️⃣ Daily Drip + Hard Limit

* Max **3 Apollo API calls per day**
* If limit reached:

  * Send Slack/Telegram alert
  * Queue remaining leads for next day

---

## 🔌 API Configuration

* Use: **Reveal / Enrichment API**
* Avoid: Export API

**Data to fetch:**

* LinkedIn URL
* Primary Email

**Do NOT fetch:**

* Mobile numbers (high credit cost)

---

If you want, I can next:

* Convert this into a **high-converting GitHub README (badges + visuals)**
* Or design a **system architecture diagram + n8n workflow diagram**
* Or optimize it for a **freelance proposal (Upwork/Fiverr)** 🚀
