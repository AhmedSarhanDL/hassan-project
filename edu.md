# EgyptFocused LLM Tutoring Platform

## 0. Executive Snapshot

A phased roadmap and feature catalogue for launching a **bilingual (Arabic a0194 a0English) AI tutoring webapp** tailored to Egypt9s K12 & university preparatory market. The plan integrates compliance with Law51/2020, alignment to Ministry of Education (MoE) syllabi, and a **GraphRAG knowledgegraph memory** for deep textbook understanding.

---

## 1. Phase Roadmap (24Month Horizon)

| # | Phase                      | Window    | Core Objectives                                          | Key Outputs                                                | Gate / Done a0Signal                                  |
| - | -------------------------- | --------- | -------------------------------------------------------- | ---------------------------------------------------------- | --------------------------------------------------- |
| 0 | **PreCommit**             | Week 1  | Vision, domain, personal runway validated                | 2page vision note; Lean Canvas a0v0                         | Domain reserved & personal GO decision              |
| 1 | **Discovery a0& Validation** | W2 a0192 a0M2   | Demand evidence (2615 interviews), legal landscape mapped | Personas; MoE alignment gapanalysis; compliance checklist | 257002f% intent a0to a0use/pay; no legal blockers          |
| 2 | **Technical Prototype**    | M2 a0192 a0M3   | Endtoend QA flow; GraphRAG PoC on one textbook        | Clickable POC; usage logs; latency a0<40fs                    | 8002f% helpfulness score from alpha users             |
| 3 | **MVP a0+ Compliance**       | M3 a0192 a0M5   | Guardianconsent, Paymob/Stripe, PDPL controls           | v1 webapp; DPO contract; security test pass               | MoE syllabus validated by teacher adviser           |
| 4 | **Closed a0Beta**            | M5 a0192 a0M8   | 500 learners, 10 teachers; cost & retention tuning       | Beta metrics; cost curve; Arabic UX polish                 | 2530 11day retention a0>4002f%; token cost a0164 a0\$0.12 a0/ a0user |
| 5 | **GotoMarket a0v1**        | M8 a0192 a0M12  | Revenue on; 35 paying schools                           | Paid subs dashboard; 3 MoUs; investor deck                 | 265EGP02fk MRR **or** 3 school contracts            |
| 6 | **Scale a0& Seed Raise**     | M12 a0192 a0M18 | 10002fk a0MAU infra; seed 165 a0\$102fM                            | SLA 99.502f%; cost model; closed seed round                  | Runway 161802fm & infra cost a01642002f% revenue             |
| 7 | **PostPMF Expansion**     | M18 a0192 a0M24 | Profitability; Gulf localisation; livetutor marketplace | KSA MoU; marketplace module; EBITDA >0                     | 3 11profitable months; first nonEGY revenue          |

---

## 2. Technical Architecture Highlights

### 2.1 Knowledge Layer  **GraphRAG BookMemory**

* **Ingestion**: OCR/PDF 192 sentencelevel chunks 192 Namedentity recognition (Arabic & English) 192 triplet extraction.
* **Storage**: Neo4j or Amazon Neptune hosted in Cairo region; vector index (Weaviate) held sidebyside.
* **Retrieval Flow**:

  1. User query 192 semantic search (vector)
  2. Topk nodes 192 hopexpansion (1112 degrees) in knowledge graph
  3. Compose context 192 feed LLM (Mixtral8x22BAra finetune or GPT4o)
* **Benefits**: hallucination drop, syllabus crosslinking, explanatory paths for teachers.

### 2.2 Core Stack

| Layer          | Tech Choice                                                          | Egypt Relevance                               |
| -------------- | -------------------------------------------------------------------- | --------------------------------------------- |
| Frontend      | Next.js a0/ a0React a0+ a0i18n & RTL; PWA for lowbandwidth                  | Mobilefirst; Arabic RTL support              |
| Backend       | FastAPI or NestJS; Postgres; Redis cache                             | Python/Javascript talent abundant locally     |
| LLM Serving    | OpenAI API fallback + onprem GPU (NVIDIA L40) for opensource model | Control token cost; dataresidency compliance |
| Data a0Residency | AWS a0mesouth1 (Bahrain) or Egypt Telecom Nebula DC                  | PDPL a01714 requires regional hosting            |
| MLOps          | BentoML a0/ a0Kubeflow; Prometheus + Grafana                             | Local devs familiar; cost tracking            |
| Payments       | Paymob (EGP) + Stripe a0USD                                            | Accept local wallets, instalments             |
| Analytics      | Metabase over Postgres; Amplitude for events                         | Low setup cost; Arabic dashboards             |
| Identity       | Firebase Auth or Custom OAuth; guardian consent flow                 | Age gating for minors                         |
| DevOps         | Terraform IaC; Arqam Cloud MSP optional                              | Egyptian cloud expertise                      |

---

## 3. Feature Catalogue

### 3.1 Core Learner Features

* **Conversational Tutor** (Arabic/English switch)
* **Stepbystep problem solver** (shows reasoning path)
* **Curriculumaligned practice sets** (MoE tags)
* **Adaptive spaced repetition** (Egyptian term schedules)
* **Voice input & TTS output** (Cairo Arabic)
* **Offline mode (PWA cache)** for lowconnectivity zones
* **Exam simulator** with grading rubric explanations

### 3.2 Teacher & School Features

* **Answer audit trail** (tokenlevel provenance, GraphRAG path)
* **Class dashboard** (mastery heatmap, risk alerts)
* **Custom worksheet generator** (PDF & Google Classroom push)
* **Admin analytics** (attendance vs. performance)
* **Bulk licence management** (Paymob instalment invoicing)

### 3.3 Parent Features

* **Weekly progress SMS / WhatsApp digest** (Arabic)
* **Guardian controls** (session timelimits, content filters)
* **Subscription family plans** (shared wallet)

### 3.4 AI & Data Features

* **Bias & hallucination logging** with oneclick teacher flagging
* **Tokencost tracker** (per user & per subject)
* **Lowlatency fallback model switch** (GPT3.5 vs Mixtral)
* **Graph view of knowledge** for advanced learners

### 3.5 Engagement & Gamification

* **Badge system** rooted in Egyptian cultural icons (Pharaoh, Pyramids)
* **Peer leaderboard** (schoolsafe pseudonyms)
* **Challenge of the Day** tied to MoE curriculum week

### 3.6 Trust, Safety, Compliance

* **Guardian consent modal** (Law51/2020 compliant)
* **Under 113 COPPAstyle flow** (extra safeguards)
* **Arabic toxicity filter & moderation queue** (Unitary.ai)

### 3.7 Monetisation Extensions

* **Marketplace for vetted human tutors** (takerate model)
* **Affiliate bookstore links** (student reference material)
* **Corporate CSR sponsorship slots** (subsidised rural access)

### 3.8 Technical Feature Roadmap a013 Detailed TaskLevel

> **Code Base Convention**  (applies to every sprint)
>
> * **Monorepo** a0`egypt-edututor/` (GitHub)
>
>   * `apps/` a013 a0`web`, `teacher-portal`, `admin`
>   * `services/` a013 a0`kg_ingest`, `chat_api`, `speech_api`, `billing`, `auth`
>   * `infra/` a013 a0Terraform, K8s manifests, GitHub Actions
>   * `docs/` a013 a0ADR (Architecture Decision Records), OpenAPI specs, onboarding guides
> * **Branching** a013 a0GitFlow (`main`, `develop`, `release/*`, `feature/*`)
> * **CI/CD** a013 a0GitHub Actions 192 Docker Hub 192 K8s (ArgoCD)
> * **Quality Gates** a013 a0precommit (`black`, `ruff`, `bandit`), 9002f% pytest coverage gate.

---

#### Phase a02 a0(Month a0202f19202f3) a014 a0Prototype Track

| Sprint                          | Detailed Tasks                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         | Acceptance Gate                                                                             |
| ------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| **2.1 a0GraphRAG Pipeline a0MVP**   | **Git & Env**: <br>202 Create `kg_ingest` service folder; initialise Poetry.<br>202 Add ADR 11001: Choose Neo4j + Weaviate.<br>**OCR a0Pipeline**: <br>202 Dockerfile with a0`tesseract-ocr` & `arabic` a0lang pkg.<br>202 Write `ocr_runner.py` batching 300 dpi PNGs.<br>**NER Training**: <br>202 Label 200 sentences in Prodigy; store a0`.spacy` model in a0`models/`.<br>202 Add `training.yml` & GitHub Action to train on push.<br>**Triplet Loader**: <br>202 Cypher a0`MERGE` script idempotent.<br>202 Unit tests with `pytest-neo4j` fixture.<br>**Vector Embedding**: <br>202 Wrapper `embed.py` (bgebasear via a0`sentence-transformers`).<br>**CI**: <br>202 Workflow `kg_ci.yml` 13 lint, tests, build image.<br>**Infra**: <br>202 Terraform module a0`kg_db` creates Neo4j a0& Weaviate on Hetzner Cloud.<br> | Endtoend ingestion of Chapter a01 produces 26502fk nodes; CLI a0query returns path 164202fs P95. |
| **2.2 a0API a0Gateway a0& a0Text Chat** | **Git**: new `chat_api` FastAPI service.<br>**Auth**: implement JWT, OAuth2 password flow.<br>**LLM Router**: wrapper with fallthrough to Mixtral via vLLM Docker image (local a0GPU).<br>**RateLimiter**: Redis a0bucket middleware (100 a0r/h).<br>**Tests**: Pytest httpx a0client; 50 a0cases; snapshot expected JSON.<br>**CI/CD**: GitHub Action builds multiarch image & deploys helm a0chart `chat-api/`.<br>                                                                                                                                                                                                                                                                                                                                                                           | 100 a0concurrent k6 users 192 a0P95 latency a0164202fs; test suite pass a010002f%.                         |
| **2.3 a0Observability Stack**     | **Git**: `infra/monitoring/` helmfile.<br>**Prometheus** scrape jobs for FastAPI, GPU02fDCGM exporter, Neo4j.<br>**Grafana**: JSON dashboards committed under `dashboards/`.<br>**Alertmanager** route to Slack webhook; severity mapping ADR 11002.<br>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   | Alert fires when latency a0>02fs for 502fm; manual Grafana check OK.                            |

#### Phase a03 a0(Month a0302f19202f5) a014 a0MVP a0& a0Compliance

| Sprint                                                  | Detailed Tasks                                                                                                                                                                                                                                                                                                         | Acceptance Gate                                             |
| ------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------- |
| **3.1 a0Speech Layer02f 3b**                                  | **Git**: `speech_api` service.<br>**STT**: integrate GCP Speech (REST) with streaming websockets (`fastapi_websocket_pubsub`).<br>**TTS**: Mawdoo3 REST; add Redis cache key `tts:{hash}`.<br>**FrontEnd**: React hook `useSpeech()`; microphone permission UX.<br>**Load Test**: JMeter 50 a0simultaneous streams.<br> | Realtime lag a0<0280002fms 9502fth; frontend records <202f% error.  |
| **3.2 a0Guardian Consent Flow**                           | **DB**: `guardian_consents` table (user\_id, timestamp, hash).<br>**Email OTP**: Resend.com API; 6digit TTL a010 a0min.<br>**Copy**: PDPL text drafted (`docs/legal/consent_ar.md`).<br>**Audit**: DPO reviews and signs ADR 11003.                                                                                         |                                                             |
| External DPO signs; functional e2e Cypress test passes. |                                                                                                                                                                                                                                                                                                                        |                                                             |
| **3.3 a0Usage a0Meter02f0**                                  | **Instrumentation**: FastAPI middleware collects token counts, speech seconds.<br>**DB a0schema**: partitioned by day.<br>**ETL**: Airflow DAG summarises hourly into `usage_daily`.<br>**Metabase**: dashboard `usage_costs` with formulas (USD/token rate).                                                            | ETL runs <302fmin; dashboard shows correct sample day totals. |
| **3.4 a0KGViz Teacher Audit**                            | **Neo4j Bloom**: configure SSO via Keycloak.<br>**D3 Minimap**: `/teacher/api/path/<answer_id>` returns JSON nodes/edges.<br>**Frontend**: React component renders InteractiveGraph.<br>**Security**: role `ROLE_TEACHER` only.<br>                                                                                   | Click any answer 192 graph renders <102fs.                      |

#### Phase a04 a0(Month a0502f19202f8) a014 a0Closed a0Beta

| Sprint                                                       | Detailed Tasks                                            | Acceptance Gate |
| ------------------------------------------------------------ | --------------------------------------------------------- | --------------- |
| **4.1 a0Adaptive Practice02f 3b**                                  | **Alg**: ELO skill per concept; update after each Q.      |                 |
| **Scheduler**: daily cron selects weakest 5 concepts / user. |                                                           |                 |
| **Push**: FCM topic per user; Expo PWA notifications.        |                                                           |                 |
| **Analytics**: BigQuery panel retention/CTR.                 |                                                           |                 |
| 3002f%+ CTR on daily practice push.                            |                                                           |                 |
| **4.2 a0Cost Optimiser**                                       | **Heuristic**: difficulty classifier (SVM) 192 cheap model. |                 |
| **Prompt a0trimmer**: remove solved steps.                     |                                                           |                 |
| **Router** a0in `chat_api`. **AB test** LaunchDarkly flag.     |                                                           |                 |
| Avg cost/user02f16402f0.1202fUSD, quality drop a0<02302f%.                |                                                           |                 |
| **4.3 a0Arabic RTL UI Polish**                                 | **i18n**: reacti18next JSON keys.                        |                 |
| **RTL audit**: Storybook visual tests.                       |                                                           |                 |
| **Accessibility**: axecore CI script.                       |                                                           |                 |
| Lighthouse i18n02f>0295; zero LTR glitches.                     |                                                           |                 |

#### Phase a05 a0(Month a0802f19202f12) a014 a0Public a0Launch

| Sprint                                                 | Detailed Tasks                                       | Acceptance Gate |
| ------------------------------------------------------ | ---------------------------------------------------- | --------------- |
| **5.1 a0Exam Simulator a0v1**                              | **Question DB**: `exam_session`, `exam_item` tables. |                 |
| **Timer**: Web Worker; autosave localStorage.         |                                                      |                 |
| **Scoring**: rubric prompt chain; store JSON rubric.   |                                                      |                 |
| **PDF**: Puppeteer render.                             |                                                      |                 |
| Score correlation vs teacher 16502f0.85 on 50 11exam sample. |                                                      |                 |
| **5.2 a0Billing & Subscriptions**                        | **Webhook**: Paymob 192 billing service.               |                 |
| **Licence seats**: `school_licences` table.            |                                                      |                 |
| **Stripe**: USD plans; FX conversion note.             |                                                      |                 |
| **Integration test**: mock webhooks.                   |                                                      |                 |
| Sandbox flow complete; invoice PDF emails.             |                                                      |                 |
| **5.3 a0Load & Failover Test**                           | **k6** script committed.                             |                 |
| **ChaosMesh**: podkill Neo4j & Weaviate.              |                                                      |                 |
| **Observability** verifies zero data loss.             |                                                      |                 |
| 9502fth latency a0<02502fs during failover.                   |                                                      |                 |

#### Phase a06 a0(Month a01202f19202f18) a014 a0Scale a0& a0Seed

| Sprint                                               | Detailed Tasks                                     | Acceptance Gate |
| ---------------------------------------------------- | -------------------------------------------------- | --------------- |
| **6.1 a0GPU AutoScaler**                              | **Karpenter**: provision `nvidiaa100spot` pools. |                 |
| **HPA**: tokenrps metric.                           |                                                    |                 |
| **Load test** peak50 a0rps.                          |                                                    |                 |
| Cost/token 19302f1502f% vs baseline.                       |                                                    |                 |
| **6.2 a0Subject Expansion**                            | **Pipeline**: parallel ingestion DAG per textbook. |                 |
| **Validation**: random 102f% manual review in Prodigy. |                                                    |                 |
| <202f% ingest error; >50002fk nodes graph.               |                                                    |                 |
| **6.3 a0Edge Cache Gateway**                           | **Cloudflare Worker**: KV cache 2402fh.              |                 |
| **Stalewhilerevalidate** logic.                    |                                                    |                 |
| **Perf test**: rps capacity.                         |                                                    |                 |
| Cache HIT >026002f%, rps 19102f2502f%.                        |                                                    |                 |

#### Phase a07 a0(Month a01802f19202f24) a014 a0Regional a0& a0Marketplace

| Sprint                               | Detailed Tasks                                  | Acceptance Gate |
| ------------------------------------ | ----------------------------------------------- | --------------- |
| **7.1 a0KSA Localisation**             | **Dialect STT** finetune (50002fh Riyadh audio). |                 |
| **Hijri date util** in frontend.     |                                                 |                 |
| **Legal**: CITC dataresidency memo. |                                                 |                 |
| WER02f<121202f% in test, CITC approval.   |                                                 |                 |
| **7.2 a0LiveTutor Marketplace02f 3b**     | **Microservice**: `tutor_sched` (Go + gRPC).   |                 |
| **WebRTC**: mediasoup SFU.           |                                                 |                 |
```