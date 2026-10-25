# Scrutiny Board Management System

A web application and SQL database that manages the full life cycle of university examination papers: preparation, moderation, Head of Department verification, scrutiny board review and final decision.

**Team NextDB** | Department of Computing, Rajarata University of Sri Lanka | Database Management Systems | 2026

---

## Quick Links

| What | Link |
|---|---|
| Live system | https://scrutiny-board-system.vercel.app/ |
| GitHub repository | https://github.com/lwijayamal/nextDB.git |

---

## Login Details

Open the live system and sign in with one of the accounts below.

| Role | Username | Password |
|---|---|---|
| Admin | `admin` | `admin1234` |
| Lecturer | `Lecturer` | `admin1234` |
| Head of Department | `hod` | `admin1234` |
| Moderator | `modarator` | `admin1234` |
| Staff | `staff` | `admin1234` |

> Usernames are entered exactly as shown, including the spelling `modarator` and the capital `L` in `Lecturer`.
> These are demo accounts for evaluation only. Change the passwords before any real use.

---

## Technology Stack

| Layer | Technology |
|---|---|
| Database | PostgreSQL (hosted on Supabase) |
| Database logic | SQL and PL/pgSQL: constraints, functions, triggers, views, indexes |
| Front end | Web application (source in this repository) |
| Hosting | Vercel (deployed from GitHub) |

---

## What the System Does

- Manages examinations, courses, the exam timetable, lecturers, moderators and HODs.
- Creates one exam paper per timetable entry and tracks it through eight statuses.
- Runs multiple moderation cycles with comments, recommendations, corrections and revisions.
- Tracks three required documents per paper: Examination Paper, Marking Scheme, Moderator Report.
- Lets the HOD verify a paper only when moderation is complete and all documents are available.
- Assigns verified papers to a scrutiny board, records the review and the final decision.
- Logs every status change and shows live status counters on the dashboard.

### Paper life cycle

```
Pending Moderation -> Under Moderation -> Revision Required (back to moderation)
        -> Moderation Completed -> Ready for Scrutiny -> Under Scrutiny
        -> Approved | Rejected | Revision Required
```

### Roles

| Role | Responsibility |
|---|---|
| admin | Manages users, courses, examinations, timetable and scrutiny boards |
| lecturer | Prepares the paper and marking scheme, submits revisions |
| moderator | Reviews papers in moderation cycles, records corrections |
| hod | Verifies completed, fully documented papers |
| staff | Supports administration |

---

## Database Overview

- **16 tables** in the SQL script: `hods`, `lecturers`, `moderators`, `examinations`, `courses`, `exam_timetable`, `scrutiny_boards`, `exam_papers`, `marking_schemes`, `moderation_cycles`, `revisions`, `documents`, `hod_verifications`, `scrutiny_reviews`, `scrutiny_decisions`, `paper_status_history`
- Supporting tables referenced by the script: `users`, `audit_log`
- **8 indexes**, **2 helper functions**, **7 triggers**, **5 report views**
- The workflow rules are enforced inside the database, so no client can skip a step. For example, a paper cannot be assigned to a board until the HOD has verified it.

### Triggers

| Trigger | Purpose |
|---|---|
| `paper_status_log` | Records every status change in `paper_status_history` |
| `cycle_rules` | Requires a moderator; updates paper status with each moderation cycle |
| `hod_verify_rule` | Blocks verification unless moderation is complete and all 3 documents are available |
| `hod_verified_status` | Sets the paper to Ready for Scrutiny |
| `paper_board_rule` | Allows board assignment only for Ready for Scrutiny papers |
| `review_rule` | Allows a review only for a paper assigned to that board |
| `decision_status` | Completes the review, sets the final status, writes an audit record |

### Report views

`v_paper_overview`, `v_moderation_progress`, `v_board_summary`, `v_missing_documents`, `v_status_summary`

---

## Setting Up the Database

1. Create a Supabase project and open the SQL editor.
2. Run the SQL script in this order: `users` and `audit_log`, reference and staff tables, boards and timetable, `exam_papers`, the paper-dependent tables, indexes, functions, triggers, views.
3. Optionally load the sample data (2 courses, 1 examination, 2 papers, 1 board) for testing.
4. Put the Supabase connection settings in the Vercel project's environment variables. Never commit them to the repository.
5. Connect this GitHub repository to Vercel and deploy.

---

## Team Members

| No. | Name | Reg. No. | Index No. |
|---|---|---|---|
| 1 | W.M. Lahiru Sathsara | ICT/2024/134 | 6734 |
| 2 | K.S. Heshan | ICT/2024/051 | 6664 |
| 3 | W.A.S.K. Wickramasinghe | ICT/2024/133 | 6733 |
| 4 | M.K. Thisari Thathsara | ICT/2024/120 | 6721 |
| 5 | U.R.G.N.H. Bandara | ICT/2024/016 | 6630 |
