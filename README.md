# 🛡️ Standalone Cloudflare Tunnel with Auto-Update
### نفق Cloudflare مستقل مع تحديث تلقائي

> A standalone, self-healing, and auto-updating Cloudflare Tunnel — Zero ports exposed.
> نفق Cloudflare مستقل، يُعالج نفسه، ويُحدّث نفسه — بدون فتح أي بورت.

---

## 📖 Overview | نظرة عامة

**English:**
This repository provides a minimal, production-ready Docker Compose setup for running a **Cloudflare Tunnel** as an isolated service. It acts as a secure gateway for any backend application (e.g., Nginx, Django, APIs) without ever exposing a port to the public internet.

It is intentionally **decoupled** from the main application stack, so you can restart, update, or debug the tunnel independently — without touching your app.

**عربي:**
هذا الريبو بيوفر إعداد Docker Compose جاهز للإنتاج لتشغيل **Cloudflare Tunnel** كسيرفس معزول. بيعمل كبوابة أمنية لأي تطبيق backend (مثل Nginx أو Django أو APIs) بدون ما يفتح أي بورت للإنترنت العام.

مصمم بشكل **مستقل** عن stack التطبيق الأساسي، فتقدر تعيد تشغيل أو تحديث أو تـdebug النفق بشكل منفصل — من غير ما تأثر على التطبيق.

---

## ✨ Features | المميزات

| Feature | الميزة |
|---|---|
| 🔒 **Zero Trust Architecture** — No inbound ports needed | لا داعي لفتح أي بورت على الـ firewall |
| 🔄 **Auto-Update via Watchtower** — Checks every 24h | تحديث تلقائي كل 24 ساعة |
| 💊 **Self-Healing** — Containers restart on failure | إعادة تشغيل تلقائية عند أي فشل |
| 📦 **Standalone Deployment** — Decoupled from your app | مستقل تماماً عن stack التطبيق |
| 🧹 **Auto Cleanup** — Old images removed after update | حذف الـ images القديمة بعد التحديث |

---

## 🏗️ Architecture | البنية

```
Internet
   │
   ▼
Cloudflare Edge  ◄──── Cloudflare Tunnel (cloudflared) ◄──── Your App (Nginx/Django)
   │                         │
   │                    pos_network (shared Docker network)
   │
   └── No public ports exposed on your server ✅
```

---

## 📋 Prerequisites | المتطلبات

**English:**
- Docker & Docker Compose installed
- Active [Cloudflare Zero Trust](https://dash.teams.cloudflare.com/) account
- A tunnel created and a **Tunnel Token** generated
- An external Docker network named `pos_network` (or rename it to match your setup)

**عربي:**
- Docker وDocker Compose مثبتين
- حساب فعّال على [Cloudflare Zero Trust](https://dash.teams.cloudflare.com/)
- نفق منشأ مسبقاً وتوكن الـ **Tunnel Token** محفوظ
- شبكة Docker خارجية باسم `pos_network` (أو غيّر الاسم ليناسب إعدادك)

---

## ⚙️ Environment Variables | متغيرات البيئة

Create a `.env` file in the same directory as `docker-compose.yml`:
أنشئ ملف `.env` في نفس مجلد `docker-compose.yml`:

```env
# .env

# Your Cloudflare Tunnel Token
# توكن النفق من لوحة تحكم Cloudflare Zero Trust
# Dashboard → Zero Trust → Networks → Tunnels → your tunnel → Configure → Token
CLOUDFLARE_TUNNEL_TOKEN=eyJhIjoiXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX...
```

> ⚠️ **Never commit `.env` to Git.** Add it to `.gitignore`.
> ⚠️ **لا ترفع ملف `.env` على Git أبداً.** أضفه إلى `.gitignore`.

---

## 🚀 Getting Started | البدء

### 1. Clone the repository | استنسخ الريبو

```bash
git clone <your-repository-url>
cd <repository-directory>
```

### 2. Create the `.env` file | أنشئ ملف البيئة

```bash
cp .env.example .env
# Then edit .env and paste your token
# ثم عدّل الملف وضع توكنك
```

### 3. Create the shared network (if not exists) | أنشئ الشبكة المشتركة إن لم تكن موجودة

```bash
docker network create pos_network
```

### 4. Start the services | شغّل الخدمات

```bash
docker compose up -d
```

### 5. Verify it's running | تحقق من التشغيل

```bash
docker compose ps
docker logs cloudflared_standalone
```

---

## 🔄 How Auto-Update Works | كيف يعمل التحديث التلقائي

```
Watchtower (every 24h)
   │
   ├── Checks Docker Hub for new cloudflared image
   │
   ├── If new image found:
   │     ├── Pull new image
   │     ├── Stop old container
   │     ├── Start new container (same config)
   │     └── Delete old image (--cleanup)
   │
   └── If no new image: do nothing ✅
```

**Downtime during update:** A few seconds only — Cloudflare reconnects automatically.
**وقت التوقف أثناء التحديث:** ثوانٍ معدودة فقط — Cloudflare يعيد الاتصال تلقائياً.

> 📌 **Note:** Watchtower does **not** update itself automatically.
> To update Watchtower manually:
> ```bash
> docker pull containrrr/watchtower
> docker compose up -d watchtower-cf
> ```

---

## 📁 Project Structure | هيكل المشروع

```
.
├── docker-compose.yml   # Main config | الإعداد الرئيسي
├── .env                 # Secret tokens (not committed) | التوكنات السرية
├── .env.example         # Template for .env | قالب للـ .env
└── README.md            # This file | هذا الملف
```

---

## 🛑 Stopping the Services | إيقاف الخدمات

```bash
# Stop and remove containers
docker compose down

# Stop without removing
docker compose stop
```

---

## 🔗 Related Resources | مصادر مفيدة

- [Cloudflare Zero Trust Dashboard](https://dash.teams.cloudflare.com/)
- [cloudflared Docker Image](https://hub.docker.com/r/cloudflare/cloudflared)
- [Watchtower Docs](https://containrrr.dev/watchtower/)
- [Docker Compose Docs](https://docs.docker.com/compose/)

---

## 📜 License | الترخيص

MIT — Feel free to use and modify.
MIT — استخدم وعدّل بحرية.
