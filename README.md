# 📘 Unijobs ATS — Low Level Design (LLD)

> **Technology**
> Backend: Core PHP (procedural + service files)
> Database: MySQL
> Auth: PHP Sessions
> Mail: SMTP
> Storage: Local filesystem

This document explains **how the application actually works internally**, at **file, table, API, and flow level**, with **visual diagrams** rendered directly in GitHub.

---

## 1️⃣ SYSTEM MODULE BREAKDOWN (LLD)

```mermaid
flowchart LR
    UI[Browser UI] --> AUTH[Auth Module]
    UI --> VAC[Vacancy Module]
    UI --> ATS[ATS Module]
    UI --> INT[Interview Module]
    UI --> ADM[Admin Module]

    ATS --> PARSER[CV Parser]
    ATS --> FILTER[Auto Filter Engine]
    ATS --> EMAIL[Email Engine]

    AUTH --> DB[(MySQL)]
    VAC --> DB
    ATS --> DB
    INT --> DB
    ADM --> DB
```

### Modules

* **Auth**: login, roles, sessions
* **Vacancy**: job posting, ownership
* **ATS**: applications, status pipeline
* **Parser**: CV text extraction
* **Filter**: JD vs CV matching
* **Email**: rejection + reminders
* **Interview**: calendar & scheduling
* **Admin**: access control

---

## 2️⃣ DATABASE — LOW LEVEL ER DESIGN

```mermaid
erDiagram
    USERS ||--o{ COMPANIES : manages
    COMPANIES ||--o{ VACANCIES : posts
    VACANCIES ||--o{ APPLICATIONS : receives
    CANDIDATES ||--o{ APPLICATIONS : applies
    APPLICATIONS ||--o{ INTERVIEWS : schedules
    APPLICATIONS }o--o{ TAGS : labeled
    APPLICATIONS ||--o{ ACTIVITY_LOGS : tracked
```

### Key Tables (LLD View)

#### `applications` (ATS Core)

| Field        | Purpose                      |
| ------------ | ---------------------------- |
| id           | unique                       |
| vacancy_id   | **isolates ATS per vacancy** |
| candidate_id | applicant                    |
| source       | Unijobs / Other              |
| score        | match %                      |
| status       | pipeline state               |
| created_at   | timeline                     |

⚠️ **Rule enforced**:
No application exists **without a vacancy** 

---

## 3️⃣ CV INGESTION FLOW (DETAILED)

```mermaid
sequenceDiagram
    participant C as Candidate
    participant UI as Job Page
    participant PHP as apply-job.php
    participant FS as File System
    participant DB as Database

    C->>UI: Upload CV
    UI->>PHP: POST form
    PHP->>FS: Save CV
    PHP->>PHP: Parse CV
    PHP->>DB: Create candidate
    PHP->>DB: Create application
    PHP->>PHP: Run auto filter
    PHP->>DB: Save score + status
```

### Important

* Parser failure ❌ does NOT reject application
* HR can always edit parsed data manually

---

## 4️⃣ AUTO FILTER ENGINE (LLD LOGIC)

```mermaid
flowchart TD
    A[Job Description] --> B[Extract Keywords]
    C[CV Text] --> D[Extract Skills]
    B --> E[Match Engine]
    D --> E
    E --> F{Score >= Threshold?}
    F -->|Yes| G[Shortlisted]
    F -->|No| H[Rejected Auto]
```

### Filtering Rules

* Keyword match
* Skill overlap
* Experience count (basic)

📌 **Rejected ≠ Hidden**
Rejected CVs remain visible & reversible 

---

## 5️⃣ ATS STATUS PIPELINE (STATE MACHINE)

```mermaid
stateDiagram-v2
    New --> Shortlisted
    New --> Rejected_Auto
    Rejected_Auto --> Reviewed
    Reviewed --> Shortlisted
    Shortlisted --> Interview_Scheduled
    Interview_Scheduled --> Interviewed
    Interviewed --> Hired
    Interviewed --> Rejected_Manual
```

### Design Guarantee

* No terminal state blocks HR action
* Manual override always allowed

---

## 6️⃣ HR DASHBOARD → APPLICATION FLOW

```mermaid
flowchart LR
    HR[HR Dashboard] --> L[List Applications]
    L --> V[View CV]
    V --> S[Change Status]
    S --> DB[(Update Application)]
    S --> LOG[Activity Log]
```

---

## 7️⃣ BULK REJECTION EMAIL FLOW

```mermaid
sequenceDiagram
    participant HR
    participant UI
    participant PHP
    participant DB
    participant SMTP

    HR->>UI: Select rejected
    UI->>PHP: Send bulk email
    PHP->>DB: Fetch candidates
    PHP->>SMTP: Send emails
    PHP->>DB: Log activity
```

### Guarantees

* Same email template
* Failure logged, not blocking
* One-click operation 

---

## 8️⃣ INTERVIEW SCHEDULING (LLD)

```mermaid
flowchart TD
    A[Select Candidate] --> B[Pick Date & Time]
    B --> C[Save Interview]
    C --> D[Schedule Reminder]
    D --> E[Send Email 24h Before]
```

---

## 9️⃣ INTERNAL PHP FILE STRUCTURE (REALISTIC)

```
/public
  login.php
  apply-job.php
  dashboard.php

/app
  /auth
    login.service.php
    auth.middleware.php

  /vacancy
    vacancy.service.php

  /ats
    application.service.php
    filter.engine.php
    status.manager.php

  /cv
    parser.service.php

  /email
    mailer.service.php

  /interview
    interview.service.php

  /admin
    access.service.php

/core
  db.php
  session.php
  config.php
```

No frameworks.
Each file has **one responsibility**.

---

## 🔟 COMPLETE USER JOURNEY (END-TO-END)

```mermaid
flowchart LR
    Candidate --> Apply
    Apply --> ATS
    ATS --> Filter
    Filter --> HR
    HR --> Interview
    Interview --> Decision
```

---

## 1️⃣1️⃣ NON-FUNCTIONAL GUARANTEES

* Vacancy-isolated ATS pipelines 
* Manual override everywhere
* Simple UX (low-skill HR friendly)
* No hidden automation
* Fully auditable via logs

---

## 1️⃣2️⃣ WHAT THIS DESIGN AVOIDS (INTENTIONALLY)

❌ Microservices
❌ Message queues
❌ JWT
❌ AI hype
❌ Over-engineering

This is **production-correct**, not “LinkedIn-architecture”.

---

## ✅ FINAL STATEMENT

This README:

* Covers **entire application**
* Is **pure LLD**
* Is **GitHub-renderable**
* Is **implementation-ready**
* Matches **every ATS rule in spec** 

---

