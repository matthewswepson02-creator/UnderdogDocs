# Backend Architecture

## Purpose

The Underdog backend is responsible for:

* Athlete data management
* Intake processing
* Deterministic evaluation
* Evaluation persistence
* Future specialist-agent orchestration

The backend is built on:

* FastAPI
* Supabase
* Deterministic evaluation services

---

# Repository Structure

```text
backend/
├── api/
├── core/
├── prompts/
├── schemas/
├── services/
├── main.py
└── requirements.txt
```

---

# API Layer

Location:

```text
backend/api/
```

## Purpose

API routes should:

* Receive requests
* Validate requests
* Call services
* Return responses

API routes should NOT:

* Perform scoring
* Perform database operations
* Contain business logic

---

## admin.py

Purpose:

Administrative functionality.

Examples:

* athlete lookup
* intake review
* future moderation tools

---

## check_in.py

Purpose:

Handle athlete check-ins.

Responsibilities:

* receive check-in payload
* validate payload
* delegate to services

---

## deps.py

Purpose:

Shared FastAPI dependencies.

Examples:

* authentication
* user resolution
* request context

---

## intake.py

Purpose:

Handle intake submissions.

Responsibilities:

* receive intake payload
* trigger Intake Agent
* store submission
* route review workflow

---

## onboard.py

Purpose:

Athlete onboarding workflows.

---

## profile.py

Purpose:

Athlete profile retrieval and updates.

---

## scoring.py

Purpose:

Evaluation endpoints.

Examples:

* GET /score/me
* GET /score/{athlete_id}

---

# Core Layer

Location:

```text
backend/core/
```

---

## config.py

Purpose:

Application configuration.

Examples:

* Supabase configuration
* environment variables
* API settings
* runtime configuration

---

# Prompt Layer

Location:

```text
backend/prompts/
```

---

## intake_agent.py

Purpose:

Prompt templates used by the Intake Agent.

Responsibilities:

* normalization instructions
* extraction instructions
* review metadata instructions

Rule:

Prompts contain instructions only.

Prompts do not execute business logic.

---

# Schema Layer

Location:

```text
backend/schemas/
```

Purpose:

System contracts and validation.

Schemas define:

* request payloads
* response payloads
* service contracts
* database transfer objects

---

## intake.py

Contains:

* intake request models
* intake response models

---

## onboard.py

Contains:

* onboarding models

---

## profile.py

Contains:

* athlete profile models

---

# Service Layer

Location:

```text
backend/services/
```

This is the most important layer in the backend.

---

## Service Separation Rules

These rules are mandatory.

---

### supabase_service.py

Type:

Database layer

Responsibilities:

* get athlete
* get metrics
* get threshold rules
* insert check-in
* insert evaluation
* retrieve evaluation history

Allowed:

* database access
* Supabase queries

Not Allowed:

* scoring
* confidence logic
* readiness logic
* business decisions

---

### scoring_service.py

Type:

Deterministic scoring engine

Responsibilities:

* score_metric()
* score_athlete_metrics()
* calculate_average_score()
* band assignment
* scoring calculations

Allowed:

* deterministic math
* threshold evaluation

Not Allowed:

* database access
* AI calls
* orchestration
* API logic

---

### evaluation_service.py

Type:

Evaluation orchestration layer

Responsibilities:

* retrieve athlete context
* retrieve metrics
* retrieve threshold rules
* call scoring_service
* build EvaluationObject
* build evaluation summary
* build confidence assessment
* persist evaluation results

Allowed:

* orchestration
* interpretation
* evaluation assembly

Not Allowed:

* raw database implementation
* route handling
* AI scoring

---

### claude_service.py

Type:

AI integration layer

Responsibilities:

* Claude API calls
* structured AI outputs
* Intake Agent execution

Important:

AI may structure data.

AI may not assign deterministic scores.

---

### onboard.py

Type:

Onboarding business logic

Responsibilities:

* onboarding workflows
* onboarding transformations

---

# Application Entry Point

## main.py

Purpose:

FastAPI application startup.

Responsibilities:

* route registration
* middleware registration
* app configuration

---

# Current Evaluation Flow

Current implementation:

Athlete Check-In

↓

intake_submissions

↓

metrics

↓

threshold_rules

↓

scoring_service.py

↓

evaluation_service.py

↓

EvaluationObject

↓

evaluation_runs.evaluation

---

# Current Phase

Phase 1 — Intake + Evaluation Layer

Completed:

* Intake Agent
* Metric Registry
* Threshold Rules
* EvaluationObject
* Confidence Scoring MVP
* Evaluation Orchestration
* Evaluation History Retrieval
* Evaluation Persistence

---

# Locked Architecture Decisions

## ADR-001

Deterministic scoring remains backend code.

AI does not assign scores.

---

## ADR-002

Service separation is mandatory.

* scoring_service.py = math only
* evaluation_service.py = orchestration
* supabase_service.py = database access

---

## ADR-003

EvaluationObject is the source of truth.

---

## ADR-004

evaluation_runs.evaluation stores the complete evaluation output.

---

## ADR-005

Raw intake payloads must always be stored.

---

# Future Services

These are planned but not yet implemented.

* research_service.py
* nutrition_service.py
* weightlifting_service.py
* athletic_trainer_service.py
* technique_service.py
* similarity_service.py
* head_coach_service.py

Do not implement these unless explicitly requested.
