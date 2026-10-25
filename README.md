# 📋 Scrutiny Board Management System

A database-driven web application that manages the **examination paper scrutiny process** of an academic department — from exam timetable planning, through moderator review and revision cycles, to the final Scrutiny Board decision on each paper.

Built for **ICT 1407 – Database Systems**, Department of Computing, Rajarata University of Sri Lanka.

🔗 **Live demo:** [scrutiny-board-system.vercel.app](https://scrutiny-board-system.vercel.app/)

> ⚠️ Replace the GitHub repo link, screenshots, and author details below (marked with `<!-- TODO -->`) before publishing — they're placeholders since I'm generating this from outside your repo.

---

## 🧭 Overview

Before an examination paper can be printed, it has to pass through a formal moderation and scrutiny workflow: a lecturer submits a paper and marking scheme, an appointed moderator reviews it (sometimes over several revision cycles), the Head of Department verifies everything required is in place, and finally a Scrutiny Board reviews and approves, rejects, or sends the paper back for correction.

Doing this on spreadsheets makes it hard to track deadlines, moderation progress, missing documents, and past decisions. This system centralizes the whole process in a single relational database with enforced business rules, and gives each stakeholder (HOD, lecturer, moderator) a simple interface to manage their part of it.

## ✨ Features

- **Dashboard** — at-a-glance overview of papers, boards, and pending actions
- **Examinations & Exam Timetable** — record exam sessions, dates, times, and linked course codes
- **Courses** — maintain the course catalogue referenced by the timetable
- **Exam Papers** — create papers linked to a timetable entry, assign a lecturer in charge and a moderator, track status end-to-end
- **Moderation workflow** — multi-cycle moderation with comments, recommendations, and required corrections; revisions are tracked separately so a paper's full review history is never lost
- **Document tracking** — tracks submission of the three required documents (examination paper, marking scheme, moderator's report) before a paper can proceed
- **HOD Verification** — enforces that moderation is complete *and* all required documents are available before a paper is marked ready for scrutiny
- **Scrutiny Boards** — schedule boards with a date and submission deadline; assign eligible papers to a board
- **Scrutiny Review & Decision** — record board observations and the final decision (Approved / Corrections Required / Rejected)
- **Reports** — generated from SQL views: paper overview, moderation progress, board summary, missing documents, and status breakdown
- **Users** — accounts for HODs, lecturers, and moderators, linked to their respective profile records

## 🏗️ Tech Stack

```
┌─────────────┐
│    USER     │
└──────┬──────┘
       │
       ▼
┌──────────────────────┐
│      FRONTEND         │
│ HTML / CSS / JavaScript│
│      → Vercel          │
└──────────┬─────────────┘
           │ Supabase client (REST / supabase-js)
           ▼
┌──────────────────────┐
│      SUPABASE          │
│  PostgreSQL + Auth +   │
│  Auto-generated API    │
└──────────────────────┘
```

| Layer | Technology | Purpose |
|---|---|---|
| Version control | **GitHub** | Source code hosting, history, and CI trigger for Vercel |
| Frontend | **HTML, CSS, JavaScript** | User interface for HODs, lecturers, and moderators |
| Hosting | **Vercel** | Builds and deploys the frontend on every push to `main`; free tier, no card required |
| Backend / Database | **Supabase (PostgreSQL)** | Relational database, auto-generated REST API, authentication |

All three services run on free tiers — no hosting cost for this project.

## 🗄️ Database Design

The schema (`database.txt` / `database/schema.sql`) models the full scrutiny workflow as a normalized relational structure:

**Core entities**
- `hods`, `lecturers`, `moderators` — people, each optionally linked to a `users` account
- `examinations`, `courses`, `exam_timetable` — exam sessions and their scheduled courses
- `scrutiny_boards` — scheduled board sessions with a submission deadline
- `exam_papers` — the central entity; tracks `status` through the full lifecycle (`Pending Moderation → Under Moderation → Revision Required → Moderation Completed → Ready for Scrutiny → Under Scrutiny → Approved/Rejected`)
- `marking_schemes`, `documents` — tracks submission of the required supporting documents
- `moderation_cycles`, `revisions` — supports multiple rounds of moderation per paper, each with its own comments and required corrections
- `hod_verifications` — records the HOD's eligibility check before a paper can proceed to scrutiny
- `scrutiny_reviews`, `scrutiny_decisions` — the board's observations and final decision
- `paper_status_history` — a full audit trail of every status change

**Business rules enforced at the database layer** (via triggers, not just application code):
- A paper can't start moderation without a moderator assigned
- A paper can't be assigned to a board unless its status is `Ready for Scrutiny`
- A paper can only be HOD-verified once its latest moderation cycle is complete *and* all three required documents are available — enforced by a `BEFORE INSERT` trigger that raises an exception otherwise
- Every status change is automatically logged to `paper_status_history`
- A Scrutiny Board decision automatically updates the paper's status and writes an audit log entry

**Reporting views** — `v_paper_overview`, `v_moderation_progress`, `v_board_summary`, `v_missing_documents`, `v_status_summary` — pre-built joins that power the Reports section without duplicating query logic across the app.

## 📸 Screenshots

<!-- TODO: add screenshots, e.g.
![Dashboard](docs/screenshots/dashboard.png)
![Exam Papers](docs/screenshots/exam-papers.png)
-->

## 🚀 Getting Started

### Prerequisites
- A free [Supabase](https://supabase.com) account
- A free [Vercel](https://vercel.com) account (sign in with GitHub, no card required)
- Node.js, if you use any local tooling <!-- TODO: remove if not applicable -->

### 1. Clone the repository
```bash
git clone https://github.com/<!-- TODO: your-username -->/scrutiny-board-system.git
cd scrutiny-board-system
```

### 2. Set up the database
1. Create a new Supabase project.
2. Open **SQL Editor** and run the contents of `database.txt` (or `database/schema.sql`) to create all tables, triggers, views, and sample data.
3. Go to **Project Settings → API** and copy your **Project URL** and **anon public key**.

### 3. Configure the frontend
<!-- TODO: update this to match where you actually put the Supabase URL/key, e.g. a config.js or .env file -->
```js
// e.g. js/supabaseClient.js
const SUPABASE_URL = "https://YOUR_PROJECT_REF.supabase.co";
const SUPABASE_ANON_KEY = "YOUR_ANON_KEY";
```

### 4. Run locally
<!-- TODO: adjust to how you actually serve the frontend locally, e.g. Live Server, `npx serve`, etc. -->
Open `index.html` with a local server (e.g. the VS Code "Live Server" extension) and log in.

### 5. Deploy
Push to GitHub, then import the repository on [vercel.com](https://vercel.com) — no build step is required for a static frontend. Every push to `main` auto-deploys.

## 📁 Project Structure

<!-- TODO: replace with your actual folder layout -->
```
scrutiny-board-system/
├── database.txt / database/schema.sql   # full PostgreSQL schema
├── index.html                            # entry point
├── css/
├── js/
└── README.md
```

## 🎓 Academic Context

| | |
|---|---|
| **Course** | ICT 1407 – Database Systems |
| **Institution** | Rajarata University of Sri Lanka, Department of Computing |
| **Assignment** | Problem-based Assignment — Scrutiny Board Management System |

This project was developed as part of the course's database development lifecycle: requirements analysis, conceptual (EER) design, logical design, normalization, physical implementation, security design, and front-end integration.

## 👥 Author

<!-- TODO: add your name(s) and student ID(s) -->
- Your Name — Student ID

## 📄 License

<!-- TODO: pick one, e.g. -->
This project is submitted as academic coursework and is not licensed for external reuse without permission.
