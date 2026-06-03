# Backend Architecture

## Repository

```text
UnderdogMethod-Backend-main/
├── main.py
├── requirements.txt
├── api/
├── schemas/
├── services/
├── core/
└── prompts/
```

---

# Purpose

The backend is responsible for:

* Authentication-aware API handling
* Athlete onboarding
* Profile management
* Intake submission processing
* AI-assisted intake structuring
* Deterministic metric scoring
* EvaluationObject construction
* Evaluation persistence
* Admin review workflows

The backend must preserve clean service separation.

---

# Application Entry Point

## main.py

Responsibilities:

* FastAPI app initialization
* CORS configuration
* Router registration
* `GET /health`
* `GET /`

`main.py` should not contain scoring logic, database logic, or evaluation construction logic.

---

# API Layer

Location:

```text
api/
```

Purpose:

HTTP routes only.

API files should:

* validate requests
* resolve authenticated users
* call services
* return responses

API files should not:

* score athletes
* contain database logic
* contain AI prompts
* build deterministic evaluation logic

---

## api/deps.py

Purpose:

Bearer authentication dependency.

Responsibilities:

* read bearer token
* resolve Supabase user ID
* provide authenticated user context to routes

---

## api/intake.py

Endpoints:

* `POST /intake`
* `POST /approve-profile/{submission_id}`

Purpose:

Handles raw intake submissions and profile approval workflow.

Responsibilities:

* receive intake payloads
* delegate AI structuring to `claude_service.py`
* store reviewable intake/profile data through `supabase_service.py`
* support profile approval from inbox/review flow

---

## api/check_in.py

Endpoint:

* `POST /check-in`

Purpose:

Authenticated athlete check-in route.

Responsibilities:

* receive check-in payload
* store check-in submission
* insert mapped metrics
* trigger deterministic evaluation flow

---

## api/onboard.py

Endpoint:

* `POST /onboard`

Purpose:

Athlete onboarding route.

Responsibilities:

* receive onboarding payload
* validate onboarding data
* delegate onboarding logic to services

---

## api/profile.py

Endpoints:

* `/profile/me`
* `/profile/finalize`
* admin profile lookup by `athlete_id`

Purpose:

Athlete profile retrieval, update, and finalize workflows.

---

## api/scoring.py

Endpoints:

* `/score/*`
* `/athlete-context/*`

Purpose:

Evaluation and athlete-context access routes.

Responsibilities:

* retrieve current/latest score
* retrieve athlete evaluation context
* delegate evaluation logic to `evaluation_service.py`

---

## api/admin.py

Endpoints:

* `/admin/athletes`
* `/admin/intake-inbox`

Purpose:

Admin visibility and review workflows.

Responsibilities:

* list athletes
* retrieve intake inbox records
* support internal review dashboards

---

# Schema Layer

Location:

```text
schemas/
```

Purpose:

Pydantic request and response models.

Schemas define contracts between API routes, services, and persisted outputs.

---

## schemas/intake.py

Purpose:

Intake request and response models.

---

## schemas/onboard.py

Purpose:

Onboarding request and response models.

---

## schemas/profile.py

Purpose:

Athlete profile request and response models.

---

## schemas/evaluation.py

Purpose:

Evaluation output models.

Contains:

* `EvaluationObject`
* `EvaluationSummary`
* scored metric models
* confidence/readiness-related structures

`EvaluationObject` is the canonical deterministic evaluation output.

---

# Service Layer

Location:

```text
services/
```

Purpose:

Backend business logic and integrations.

---

# Mandatory Service Separation

## services/scoring_service.py

Type:

Pure deterministic math layer.

Responsibilities:

* `score_metric()`
* `score_athlete_metrics()`
* `calculate_average_score()`
* threshold comparison
* score normalization
* scoring helper logic

Allowed:

* deterministic calculations
* metric/rule comparison
* pure function behavior

Not allowed:

* Supabase access
* HTTP calls
* AI calls
* route handling
* evaluation persistence
* request/response construction

---

## services/evaluation_service.py

Type:

Evaluation orchestration and deterministic interpretation layer.

Responsibilities:

* collect athlete metrics
* collect threshold rules
* call `scoring_service.py`
* build scored metrics
* build evaluation summary
* build confidence assessment
* construct `EvaluationObject`
* coordinate evaluation persistence
* retrieve evaluation history

Allowed:

* orchestration
* deterministic interpretation
* EvaluationObject construction

Not allowed:

* raw Supabase implementation details
* API route handling
* AI model calls
* low-level scoring math that belongs in `scoring_service.py`

---

## services/supabase_service.py

Type:

Database access layer.

Implementation:

* `httpx` to Supabase REST

Responsibilities:

* athlete reads/writes
* profile reads/writes
* metric reads/writes
* threshold rule retrieval
* intake submission insertion
* profile inbox access
* check-in metric insertion
* evaluation run insertion
* latest evaluation retrieval
* evaluation history retrieval

Allowed:

* database queries
* database inserts
* database updates
* database response normalization

Not allowed:

* scoring calculations
* readiness decisions
* confidence assessment
* summary generation
* AI logic

---

## services/claude_service.py

Type:

AI integration layer.

Purpose:

Runs the Intake Agent.

Responsibilities:

* call Claude
* extract structured profile data
* normalize messy intake input
* return reviewable structured output

Allowed:

* AI-assisted intake interpretation
* profile extraction
* missing field detection
* data quality flagging

Not allowed:

* assigning deterministic scores
* assigning readiness
* calculating evaluation results
* writing directly to production scoring outputs

---

## services/onboard.py

Type:

Onboarding validation and review logic.

Responsibilities:

* onboarding validation
* onboarding review flags
* onboarding transformations

Restriction:

No direct database access.

---

# Core Layer

Location:

```text
core/
```

---

## core/config.py

Purpose:

Environment and runtime configuration.

Contains:

* `SUPABASE_*`
* `ANTHROPIC_*`
* `CLAUDE_MODEL`

---

## core/metric_registry.py

Purpose:

Canonical metric definitions for scoring.

Responsibilities:

* define supported metrics
* standardize metric names
* support scoring consistency
* prevent metric naming drift

The metric registry is part of the deterministic evaluation foundation.

---

# Prompt Layer

Location:

```text
prompts/
```

---

## prompts/intake_agent.py

Purpose:

System prompt for the Intake Agent.

Responsibilities:

* guide intake extraction
* guide metric normalization
* guide missing field detection
* guide review metadata generation

Prompts should not contain executable business logic.

---

# Current Evaluation Pipeline

```text
Authenticated Athlete
→ POST /check-in
→ intake_submissions
→ metrics
→ evaluation_service.py
→ scoring_service.py
→ EvaluationObject
→ evaluation_runs.evaluation JSONB
→ GET /score/me
→ Dashboard
```

---

# Current Intake Pipeline

```text
Athlete Submission
→ POST /intake
→ claude_service.py
→ Intake Agent structured output
→ profile/intake inbox
→ admin review
→ POST /approve-profile/{submission_id}
→ athlete profile update
```

---

# Current Database Concepts

* `athletes`
* `metrics`
* `threshold_rules`
* `evaluation_runs`
* `evaluation_runs.evaluation` JSONB
* `intake_submissions`
* `profile_inbox`

---

# Phase 1 Status

Phase 1 — Intake + Evaluation Layer

Completed:

* Authentication dependency
* Profile management
* Onboarding route
* Intake submission flow
* AI intake structuring
* Admin intake inbox
* Profile approval workflow
* Check-in route
* Metric insertion from check-in
* Metric registry
* Threshold rules
* Deterministic scoring functions
* EvaluationObject schema
* Evaluation summary construction
* Confidence scoring MVP
* Automatic evaluation orchestration
* Evaluation history retrieval
* Evaluation JSONB persistence
* Latest score retrieval

Current focus:

* Deterministic scoring tests

Next planned:

* Dashboard historical trends
* Evaluation history visualization

---

# Locked Architecture Decisions

## ADR-001 — Deterministic Scoring

Deterministic scoring remains backend code only.

AI does not assign scores.

---

## ADR-002 — Service Separation

Service boundaries are mandatory.

* `scoring_service.py` = math only
* `evaluation_service.py` = orchestration + deterministic interpretation
* `supabase_service.py` = database only
* API routes = request/response only

---

## ADR-003 — EvaluationObject Source of Truth

`EvaluationObject` is the canonical evaluation output shape.

---

## ADR-004 — JSONB Evaluation Persistence

`evaluation_runs.evaluation` stores the complete evaluation output.

Reason:

This preserves the full deterministic evaluation result for history, dashboard trends, and future coaching interpretation.

---

## ADR-005 — Raw Intake Preservation

Raw intake payloads must always be stored before interpretation.

---

## ADR-006 — Confidence Location

Confidence scoring belongs in `evaluation_service.py`.

Reason:

Confidence is deterministic interpretation of evaluation completeness, not scoring math.

---

## ADR-007 — Metric Registry

Metric naming must flow through `core/metric_registry.py`.

Reason:

Prevents scoring drift caused by inconsistent metric names.

---

# Explicitly Out of Scope

Do not add these unless specifically requested:

* ATS
* archetypes
* similarity engine
* research agent
* program builder
* plan safety agent
* nutritionist agent
* weightlifting coach agent
* athletic trainer agent
* technique agent
* head coach agent

---

# Testing Priorities

Current testing focus:

* `score_metric()`
* `score_athlete_metrics()`
* `calculate_average_score()`
* `build_evaluation_summary()`
* `build_confidence_assessment()`
* `EvaluationObject` output shape

Testing rules:

* Use pytest
* Do not hit real Supabase
* Do not require real auth
* Do not call Claude
* Mock database access in evaluation service tests
* Use fixtures for metrics, rules, athletes, and evaluation outputs
