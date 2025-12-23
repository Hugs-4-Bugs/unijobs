
# 📅 Unijobs ATS — Development Timeline (Visual)

> **Note**
> This document contains **only visual execution timelines**.
> No architecture. No LLD. No explanations.

---

## 🗓️ Master Development Timeline (Gantt)

```mermaid
gantt
    title Unijobs ATS — Core PHP Development Timeline
    dateFormat  YYYY-MM-DD
    axisFormat  %d %b

    section Phase 0 — Setup
    Project setup & infra        :p0, 2025-01-01, 3d

    section Phase 1 — Auth & Roles
    Authentication & RBAC        :p1, after p0, 6d

    section Phase 2 — Vacancies
    Vacancy management           :p2, after p1, 5d

    section Phase 3 — Applications
    Job apply & CV upload        :p3, after p2, 5d

    section Phase 4 — CV Parsing
    Parsing & candidate creation :p4, after p3, 5d

    section Phase 5 — ATS Engine
    Filtering & status pipeline  :p5, after p4, 7d

    section Phase 6 — HR Dashboard
    Application management UI    :p6, after p5, 6d

    section Phase 7 — Emails
    Email automation             :p7, after p6, 4d

    section Phase 8 — Interviews
    Interview scheduling         :p8, after p7, 4d

    section Phase 9 — Search & Pool
    Search, tags, talent pool    :p9, after p8, 4d

    section Phase 10 — Analytics
    Reports & admin controls     :p10, after p9, 4d

    section Phase 11 — Stabilization
    QA & bug fixing              :p11, after p10, 5d
```

---

## 🔍 Phase Dependency Flow (Visual)

This shows **why no phase can be skipped**.

```mermaid
flowchart LR
    P0[Setup] --> P1[Auth]
    P1 --> P2[Vacancies]
    P2 --> P3[Applications]
    P3 --> P4[CV Parsing]
    P4 --> P5[ATS Engine]
    P5 --> P6[HR Dashboard]
    P6 --> P7[Emails]
    P7 --> P8[Interviews]
    P8 --> P9[Search & Pool]
    P9 --> P10[Analytics]
    P10 --> P11[Stabilization]
```

---

## ⏱️ Time Distribution Overview (Visual)

```mermaid
flowchart TD
    A[Total ~6–7 Weeks] --> B[Core Build ~5 Weeks]
    A --> C[QA & Stabilization ~2 Weeks]

    B --> B1[Data + ATS Logic]
    B --> B2[Dashboards + Automation]

    C --> C1[Bug Fixes]
    C --> C2[Edge Cases]
```

---

## 🚦 Bottleneck Awareness (Visual)

This diagram highlights **where most projects fail** if rushed.

```mermaid
flowchart TD
    A[Phase 5 ATS Engine] -->|If rushed| B[Broken Hiring Flow]
    A -->|If done right| C[Trustworthy ATS]

    C --> D[HR Adoption]
    B --> E[HR Abandonment]
```

---

## ✅ What This Visual Timeline Guarantees

* Clear phase sequencing
* Zero parallel confusion
* No hidden dependencies
* GitHub-native rendering
* Matches your written timeline **exactly**
* No bottlenecks introduced by design

---

### Hard truth (final push)

If you **follow this timeline visually**, you won’t:

* Jump phases
* Half-build features
* Rewrite core logic later

---
