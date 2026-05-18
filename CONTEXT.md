
# 🧠 My Master Context — Paste This at the Start of Every New Chat

---

## WHO I AM

- **Role:** Apprentice Data Engineer at Fidelity (FIPSE program) — 1 year contract
- **Squad:** Tax & Treasury
- **BU Hierarchy:** CST → FMP → Tax & Treasury
- **Manager:** Praseed | **Buddy/DE:** Hari | **Principal DE:** Durgesh | **Squad Lead:** Amy Depolo
- **Time left in role:** ~8 months (started May 2026)
- **Goal after this:** Land a Data Engineer role (open to SWE too)

---

## MY TECH STACK AT WORK

SQL, Python, Pandas, Snowflake, Power BI, AWS (EC2, S3, Lambda),
Jenkins, Control-M, GitHub, Jira, ServiceNow, Confluence, IBM MQ, Axway, SWIFT

---

## MY TWO PROJECTS AT FIDELITY

### 1. BDA (Bulk Data Integration) — Treasury Side
- Handles payment file processing between Fidelity systems, Kyriba, SWIFT, and external banks
- Core: one Python app + Java MQ applications
- File formats: pipe/comma/tab delimited (CSV/TXT style)

### 2. Tax Apportionment — Tax Side
- Allocates Fidelity taxable income across legal entities, states, countries
- Batch ETL → Snowflake → Power BI reporting

---

## KEY SYSTEMS

| System | What It Does |
|--------|-------------|
| Axway | SFTP gateway — only entry/exit for files between Fidelity and external systems |
| BDI S3 | AWS S3 bucket — landing folder where all files are staged |
| IBM MQ | Message queue — guarantees no message lost between systems |
| Kyriba | External Treasury Management System — pre-payment validation |
| SWIFT SAA | SWIFT gateway — translates to/from SWIFT ISO 20022 format |
| Snowflake | Cloud data warehouse — analytics and reporting |
| Control-M | Enterprise job scheduler — orchestrates ETL pipelines |

---

## BDI OUTBOUND FLOW (Fidelity → Banks)

```
Oracle AP / FILI / SHARES → Axway (SFTP) → BDI S3
→ Python App reads raw file from S3
→ Python App transforms file (CSV/TXT → Kyriba format)
→ Python App SFTPs to Kyriba via Axway
→ Kyriba validates → generates PAIN001 + PAIN008 + Companion files
→ Axway → BDI S3
→ Treasury MQ Producer (Java) → Treasury IBM MQ
→ FFIO MQ Consumer (Java) → FFIO IBM MQ
→ SWIFT SAA → Banks (JP Morgan, Citi, Wells Fargo, HSBC, MUFG/SMBC)
```

## BDI INBOUND FLOW (Banks → Fidelity)

```
Bank → SWIFT SAA → FFIO MQ → FFIO MQ Producer
→ Treasury MQ → Treasury MQ Consumer → BDI S3

Then routes:
- PAIN002 → Axway → Kyriba (payment accepted/rejected)
- ack     → Archived in BDI S3 (audit trail)
- nack    → ServiceNow incident + email alert to tech team
```

---

## MY CURRENT SKILL LEVEL (Honest)

| Skill | Level |
|-------|-------|
| SQL | Learned but rusty — no recent hands-on |
| Python | Can write functions, basic loops — NO file I/O, NO Pandas yet |
| Java | Basics only |
| Git | Basic clone/pull — no branching practice |
| AWS | Watching real infra — no hands-on yet |
| Snowflake | Watching — no hands-on yet |
| Docker | Zero |
| Airflow | Zero |
| Kafka | Zero |
| Spring Boot | Want to learn — Java basics are base |

---

## MY DAILY TIME AVAILABLE

| Time | Hours | What to use it for |
|------|-------|-------------------|
| Office idle time | 3–4 hrs | Read BDI/MQ repos in VS Code, SQL practice (browser), observe team |
| Home after office | 2–3 hrs | Python hands-on, projects, videos |
| Saturday | 2–3 hrs | Build mini projects only |
| Sunday | Rest | Full rest |

---

## MY DEVICE SITUATION

| Device | What I Can Do |
|--------|--------------|
| Work laptop | VS Code, read repos, browser-based practice, write `.md` notes, Copilot chat |
| Personal laptop | Install anything freely — Python, Java, Docker, AWS CLI, Git push to GitHub |
| GitHub on work laptop | SSO locked — cannot create personal repos from work machine |
| GitHub on personal laptop | Full access — push code here every evening |

---

## MY 8-MONTH LEARNING PLAN

### Phase 1 — Month 1 & 2: Foundations Revival
- **Month 1:** SQL revival (JOINs, CTEs, Window functions) + Python File I/O + error handling
- **Month 2:** Pandas + Git properly + Reading BDI codebase with structure

### Phase 2 — Month 3 & 4: DE Core Stack
- **Month 3:** AWS hands-on with boto3 (S3, Lambda) + Snowflake free trial
- **Month 4:** Docker basics + Apache Airflow + Build Mini BDI Pipeline project

### Phase 3 — Month 5 & 6: Messaging + Spring Boot
- **Month 5:** Apache Kafka — producers, consumers, Python integration
- **Month 6:** Spring Boot — REST API, dependency injection, Spring Data JPA

### Phase 4 — Month 7 & 8: Portfolio + Interview Prep
- **Month 7:** Clean up 3 GitHub projects, write READMEs, architecture diagrams
- **Month 8:** LeetCode SQL/Python, system design prep, mock interviews

order and curriculum may change so dont worry but remind which order is changed, that's all

---

## MY 3 PORTFOLIO PROJECTS (TO BUILD)

| # | Project | Skills It Shows |
|---|---------|----------------|
| 1 | Mini BDI Pipeline — Python reads CSV from S3 → transforms → loads to Snowflake → Airflow orchestrates | ETL, cloud, orchestration |
| 2 | Kafka Payment Stream Pipeline — producer sends mock payment events → consumer writes to S3 | Streaming, event-driven |
| 3 | Spring Boot Payment Status API — accepts payment file name → returns status | REST API, Java |

---

## MY DOCUMENTATION SYSTEM

### Folder Structure (my-learning repo on GitHub)
```
my-learning/
├── daily-logs/
│   └── YYYY-MM-DD.md        ← one file per day
├── concepts/
│   ├── sql-notes.md
│   ├── python-notes.md
│   ├── bdi-flow.md
│   └── aws-notes.md
├── projects/
│   └── mini-bdi-pipeline.md
└── README.md
```

### Daily Log Template
```markdown
# 📅 YYYY-MM-DD

## 🏢 What I Observed at Work Today
-

## 📚 What I Studied Today
- Topic:
- Resource:
- What I understood:
- What confused me:

## 💻 What I Built / Practiced
-

## ❓ One Question I Want to Ask Tomorrow
-

## 🔗 Connections I Made
-

## ⭐ One Thing I'm Proud of Today
-
```

### How I Use AI Chat for Documentation
- I paste my raw messy notes from the day
- AI formats them into clean markdown
- I copy the output into the correct `.md` file
- Evening: `git add . → git commit → git push` from personal laptop

---

## RESOURCES I AM USING

| Topic | Resource | Where |
|-------|----------|-------|
| SQL | SQLZoo, Mode SQL Tutorial, LeetCode SQL | Browser — office |
| Python | Python official docs, real practice | Personal laptop |
| Pandas | Kaggle Pandas Course (free, browser) | Office or home |
| AWS | AWS Skill Builder (free), boto3 docs | Personal laptop |
| Snowflake | Snowflake Quickstart Tutorials | Browser |
| Airflow | Official Docker Quickstart | Personal laptop |
| Kafka | Confluent Kafka Python Tutorial | Personal laptop |
| Spring Boot | Spring official guides | Personal laptop |

---

## CERTIFICATIONS PLAN (OPTIONAL)

| Cert | Target Month | Value |
|------|-------------|-------|
| AWS Cloud Practitioner | Month 3 | Entry cloud credential |
| AWS Solutions Architect Associate | Month 5 | Strong DE credential |
| Snowflake SnowPro Core | Month 4 | Fintech DE jobs love this |

---

## 3 RULES I FOLLOW

1. **One topic at a time** — finish the week's topic before touching next
2. **Build something every week** — even 20 lines, push to GitHub
3. **Use real job as case studies** — every system at Fidelity maps to an open-source tool

---

## WHAT TO TELL THE AI AT THE START OF EACH CHAT

Paste this entire file. Then say one of these:

- *"I'm on my personal laptop. Help me set up [topic] from scratch."*
- *"Here are today's rough notes — format them into my daily log."*
- *"I'm on week [X] of my plan. What exactly do I do today?"*
- *"I finished [topic]. What's next and how do I start it?"*

---

## CURRENT STATUS (Update this block every week)

```
Last updated     : 2026-05-15
Current phase    : Phase 1 — Month 1
Current week     : Week 1
Current topic    : Setting up folder structure + SQL revival starts
Last thing done  : Created CONTEXT.md and my-learning folder structure
Next thing to do : Start SQLZoo Intermediate section + create first daily log
Projects started : None yet
GitHub repo      : (add your link here once created)
```
