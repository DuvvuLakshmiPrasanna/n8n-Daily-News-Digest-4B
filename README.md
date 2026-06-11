# 📬 Job Notifications — Daily Placement Digest

**Author:** Duvvu Lakshmi Prasanna
**Lab:** Day 4 — n8n Automation (Lab 4B)
**Tool:** Self-hosted n8n via Docker

---

## 🎯 Project Overview

An automated n8n workflow that runs on a schedule, fetches the latest job and internship postings from Hacker News Jobs RSS feed, summarises them using an AI model (Groq / LLaMA), and delivers a clean daily digest directly to your inbox — no manual searching required.

Targeted specifically at **final-year CSE students in India**, filtering for India-based, Remote, Fresher, and Internship opportunities.

---

## ⚙️ Workflow Architecture

```
Schedule Trigger → RSS Read → HTTP Request (Groq AI) → Send Email
```

| Step | Node | Purpose |
|------|------|---------|
| 1 | **Schedule Trigger** | Fires the workflow automatically at a set time |
| 2 | **RSS Read** | Fetches latest 10 job postings from `https://hnrss.org/jobs` |
| 3 | **HTTP Request** | Sends RSS data to Groq (LLaMA 3.1) for AI summarisation |
| 4 | **Send an Email** | Delivers the formatted digest via SMTP to your inbox |

---

## 🔧 Setup Instructions

### Prerequisites

- Docker Desktop installed and running
- n8n running locally at `http://localhost:5678`
- A Groq API key (free tier available at [console.groq.com](https://console.groq.com))
- Gmail account with an **App Password** enabled (for SMTP)

### 1. Start n8n via Docker

```bash
docker compose -f n8n_docker-compose.yml up -d
```

Open `http://localhost:5678` and complete the first-time setup.

### 2. Import the Workflow

- In n8n UI: click the ☰ menu → **Import from File**
- Select `Job_Notifications.json`
- The workflow loads with all 4 nodes pre-wired

### 3. Configure Credentials

**Groq API (HTTP Request node):**

- Open the HTTP Request node
- Under Headers, replace the `Authorization` value:
  ```
  Bearer <YOUR_GROQ_API_KEY>
  ```

**SMTP Email (Send an Email node):**

- Open the Send an Email node → Credentials → Edit `SMTP account`
- Host: `smtp.gmail.com`
- Port: `587`
- User: your Gmail address
- Password: your Gmail **App Password** (not your regular password)
- Update `fromEmail` and `toEmail` fields to your address

### 4. Set the Schedule

- Open the **Schedule Trigger** node
- Set Mode to **Cron**
- Cron expression: `0 7 * * *` (runs every day at 7:00 AM)
- Timezone: `Asia/Kolkata`

### 5. Activate

- Click **Save** (Cmd/Ctrl-S)
- Toggle the **Active** switch (top-right) — it turns green ✅
- Click **Execute Workflow** for an immediate test run

---

## 📧 Sample Email Output

**Subject:** `Daily Job & Internship Digest - 11 Jun 2026`

```
🚀 Daily Job & Internship Digest

Latest opportunities collected automatically.

1. Company: Zepto | Role: Backend Engineer (Fresher) | Location: Bangalore / Remote
   Apply: https://... | Source: HN Jobs | Posted: 10 Jun 2026

2. Company: Razorpay | Role: Software Intern | Location: Bangalore
   Apply: https://... | Source: HN Jobs | Posted: 10 Jun 2026

... (up to 20 opportunities)

Generated automatically by n8n.
```

---

## 🤖 AI Prompt (Groq / LLaMA 3.1)

The HTTP Request node sends this prompt to Groq:

> *"You are a placement assistant for a final year CSE student in India. Find only India, Remote, Freshers and Internship opportunities. For each opportunity provide Company Name, Role, Location, Apply Link, Source and Posted Date. Return the latest 20 opportunities."*

Model used: `llama-3.1-8b-instant` | Temperature: `0.2` (for consistent, factual output)

---

## 🗂️ Project Files

```
📁 ai-mentor-portfolio/
├── Job_Notifications.json      ← n8n workflow (importable)
└── README.md                   ← This file
```

---

## ✅ Acceptance Checklist

- [x] n8n container running at `localhost:5678`
- [x] Workflow with all 4 nodes wired and connected
- [x] Groq API key configured in HTTP Request node
- [x] SMTP credentials configured in Send an Email node
- [x] Schedule trigger set to 7:00 AM IST (`Asia/Kolkata`)
- [x] Workflow set to **Active**
- [x] Test run executed — email received in inbox
- [x] Workflow JSON exported and committed to repo

---

## 🐛 Common Issues & Fixes

| Issue | Fix |
|-------|-----|
| `401 Unauthorized` from Groq | Re-paste your API key. Ensure `Bearer ` prefix is present with a space |
| RSS node returns empty | Feed URL may be temporarily down — substitute `https://news.ycombinator.com/rss` |
| Email not received | Check App Password is correct; ensure "Less secure app access" or App Password is enabled |
| n8n UI not loading | Run `docker logs <container_id>` — check for port conflict, try port `15678` |
| Docker not starting | Open Docker Desktop and wait for the whale icon to stop animating |

---

## 🚀 Stretch Goals

- [ ] Add a **Slack node** to post the digest to a `#placements` channel
- [ ] Filter results by specific keywords (e.g., "Python", "React", "ML")
- [ ] Add a second RSS source (e.g., LinkedIn Jobs, Internshala RSS)
- [ ] Store results in **Google Sheets** for tracking over time

---

## 📸 Test Email Screenshot

> *(Add a screenshot of the received email here)*

![Test Email Screenshot](daily_digest_test_email.png)

---

*Built as part of the AI Mentor Bootcamp — Day 4: Workflow Automation with n8n*
