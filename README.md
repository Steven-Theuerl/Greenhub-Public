# Greenhub — Statewide Cannabis Pricing & Product Discovery (PA)  

<img width="3520" height="1280" alt="Work-pc-04" src="https://github.com/user-attachments/assets/aaa01824-73de-437f-adbc-2e128ba44dbf" />

> Docs‑only public repository. Source code is private. This README shares the system design, performance results, and product outcomes for hiring managers and collaborators.

## TL;DR
- **Scale:** ETL **100k+ products/day** across multiple vendor platforms
- **Speed:** ~**4s** initial render for **25k** results; **sub‑1s** client‑side filter/sort at that scale
- **Users:** **1.5k+** unique users in first month; ~**150 DAU**; **5–7 min** avg session
- **Reliability:** 100% uptime since alpha
- **Security:** **JWT** auth, **bcrypt** password hashing, env‑scoped secrets, protected admin endpoints

## Role & Contributions

Built and scaled a statewide platform from scratch, delivering high performance and 100% uptime to thousands of medical patients in Pennsylvania

## Live
- Website: https://greenhubpa.com
- Case Study: https://www.tsundoku.blog/posts/greenhub-a-price-and-data-aggregator-for-medical-patients
- Overview Technical Documents available for discussion on calls or interviews!

## What It Does
Greenhub aggregates statewide dispensary menus, normalizes heterogeneous schemas (weights, categories, brands, terpene data), and delivers fast, patient‑friendly search, filtering, and price‑drop/restock notifications.

## Architecture Overview
- **Frontend:** Next.js (App Router), TypeScript, Tailwind, Zustand
- **Backend:** Python **FastAPI** (Uvicorn + PM2), REST endpoints
- **Data:** PostgreSQL (targeted indexes), historical table + **zero‑downtime temp→live promotion**
- **Infra:** Hardened Ubuntu VPS, **Nginx** reverse proxy, **TLS** via Certbot, gzip compression
- **Ops:** Structured logs, scraper diagnostics, per‑store health checks

```

[ Client ] ⇄ [ Nginx (TLS, gzip) ] ⇄ [ Next.js App ] ⇄ [ FastAPI (Uvicorn/PM2) ] ⇄ [ PostgreSQL ]  
│  
└── [ Scraper Engine (per‑platform modules, Playwright/Requests) ]

```

## Data Pipeline (ETL)
1. **Extract** — Platform‑specific modules (API/XHR/Playwright) pull product catalogs.
2. **Transform** — Normalize brands, weights, types, categories; enrich (e.g., terpene lookup) then de‑dupe.
3. **Load** — Insert into **temp** tables; on completion, archive **live → historical**, then **temp → live** (rename), ensuring consistency and zero downtime.

## Performance Engineering
- **gzip** JSON responses at the proxy to reduce payloads by ~80–90%.
- Client rendering strategy: fast first paint, infinite scroll of in‑memory dataset; memoized cards and images.
- **Sharp** image pipeline converts remote PNG/JPG to **WebP** on demand with caching.
- Flame‑chart guided refactors (moved regex name‑cleaning from client to backend normalizers) for consistent **<1s** filter/sort on 25k items.

## Security & Privacy
- **JWT** for sessions; **bcrypt** salted password hashes; env‑isolated secrets.
- UFW firewall + **Fail2Ban**; protected internal/administrative endpoints; rate‑limit sensitive routes.

## Features
- Location‑based search (ZIP + radius) using haversine distance
- Rich filtering (weights, categories, types, brands) and fast sorting
- User accounts with saved lists and **price‑drop/restock** alerts
- Responsive UI; desktop & mobile layouts

## Roadmap
- Historical analytics & trends (Postgres rollups)
- Web workers for heavy client computations if needed at scale
- Public API for verified partner apps

## Credits
- **Engineering:** Steven Theuerl (Founder/CTO)
- **Engineering:** Jack Weber (Founder/CFO)
- **Design:** Yuu Ito (UI/UX, layout, illustrations)

## Contact
- Email: mr.steven.theuerl@gmail.com · LinkedIn: https://www.linkedin.com/in/steven-theuerl-919175209/

---
