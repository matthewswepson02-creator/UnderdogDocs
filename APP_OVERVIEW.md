# The Underdog Method

## Mission

The Underdog Method exists to help athletes who lack access to elite resources receive high-level guidance, structure, feedback, and direction.

The goal is to suppress the "underdog feeling" by providing access to a complete performance staff through software.

The platform should help athletes understand:

* Where they are
* Why they are there
* What is limiting them
* What they should improve next
* How their plan should change
* How they compare to similar athletes

The score is only the beginning.

The real value is the decision-making system behind the score.

---

# Product Vision

The Underdog Method is not:

* A workout app
* A scoring app
* A dashboard

The Underdog Method is:

An AI-powered athlete development platform that combines athlete data, deterministic evaluation, specialist analysis, and coaching synthesis into a single athlete experience.

Core equation:

Athlete Data

* Performance Metrics
* Check-ins
* Research
* Similar Athlete Patterns
* Specialist Agents

= Individualized Coaching Direction

Product promise:

**A full performance staff in your pocket.**

---

# Core System Philosophy

The platform operates in three stages:

1. Evaluate
2. Interpret
3. Coach

These stages must remain separate.

Evaluation is deterministic.

Interpretation is intelligent.

Coaching is synthesized.

Do not merge these responsibilities.

---

# Long-Term Coaching Staff Architecture

Athlete Input

↓

Intake Agent

↓

Scoring Engine (Deterministic)

↓

Program Builder Agent

↓

Plan Safety Agent

↓

Research Agent
Nutritionist Agent
Weightlifting Coach Agent
Athletic Trainer Agent
Technique Agent

↓

Head Coach Agent

↓

Athlete

↓

Progress Analytics
Similarity Pattern Learning

---

# Athlete Communication Philosophy

Athletes should never receive disconnected recommendations from multiple systems.

Specialist agents provide analysis.

The Head Coach Agent provides the final recommendation.

The athlete hears one voice.

---

# Layer 1 — Intake + Evaluation

## Purpose

Transform raw athlete submissions into structured, reviewable, scoreable performance data.

This layer separates:

AI Interpretation
→ Deterministic Evaluation
→ Coaching Decisions

---

## Intake Agent

### Responsibilities

* Athlete intake structuring
* Metric normalization
* Unit conversion
* Missing field detection
* Data quality checks
* Intake triage metadata
* Review priority assignment

### Outputs

* AthleteProfileObject
* normalized_metrics
* missing_fields
* data_quality_flags
* ai_confidence
* review_priority
* review_status
* review_notes

### Restrictions

The Intake Agent does NOT:

* Assign scores
* Assign ATS
* Build plans
* Generate coaching recommendations

---

## Scoring Engine

### Type

Deterministic backend logic.

Not an AI system.

### Responsibilities

* Metric scoring
* ATS calculation (future)
* Classification bands
* Confidence scoring
* Limiter detection
* Development focus identification
* Tier assignment
* Evaluation creation

### Outputs

* EvaluationObject
* TierAssignment
* LimiterProfile

### Rule

All scoring must remain deterministic and reproducible.

---

# Layer 2 — Knowledge Base Growth

## Purpose

Convert coaching experience, athlete trends, and research into reusable knowledge.

---

## Research Agent

### Responsibilities

* Sport science research
* Event benchmarks
* Training principles
* Performance predictors
* Scoring improvements
* Coach-approved rules

---

## KB Intake Agent

### Responsibilities

* Intervention drafting
* Fault pattern drafting
* Program rule drafting
* Recommendation structuring
* Evidence tagging
* Confidence scoring
* Review metadata

### Outputs

* KB_Intervention Draft
* KB_FaultPattern Draft
* KB_ProgramRule Draft

### Rule

AI-generated knowledge enters draft workflows before production approval.

---

# Layer 3 — Program Construction

## Purpose

Convert evaluations into training decisions.

---

## Program Builder Agent

### Inputs

* Athlete tier
* Limiter profile
* Sport/event
* Season phase
* Equipment access
* KB interventions
* Program rules

### Outputs

* Program_v1
* Training block
* Throwing sessions
* Lifting sessions
* Recovery structure
* Progression logic

---

## Plan Safety Agent

### Responsibilities

* Overtraining detection
* Injury conflicts
* Unsafe workloads
* Missing recovery
* Exercise conflicts
* Fatigue concerns

### Outputs

* Safety status
* Issue list
* Suggested corrections

### Rule

No training plan bypasses the safety layer.

---

# Layer 4 — Specialist Agents

## Nutritionist Agent

Focus:

* Weight trends
* Recovery
* Fueling
* Body composition
* Nutrition habits

---

## Weightlifting Coach Agent

Focus:

* Strength
* Power
* Fatigue
* Readiness
* Programming

---

## Athletic Trainer Agent

Focus:

* Pain
* Injury risk
* Rehab
* Prehab
* Return-to-play

May recommend professional medical evaluation.

---

## Technique Agent

Focus:

* Biomechanics
* Sequencing
* Timing
* Release mechanics
* Video analysis

Primary objective:

Identify the most limiting technical issue.

---

# Layer 5 — Head Coach

## Purpose

Final decision-maker.

Receives input from all specialist agents.

Resolves conflicts.

Produces one clear recommendation.

The athlete hears from a coach, not a committee.

---

# Layer 6 — Adaptive Intelligence

## Progress Analytics Agent

Tracks:

* Performance trends
* Evaluation history
* Fatigue response
* Adherence
* Readiness changes

Purpose:

Recommend adjustments based on progression.

---

## Similarity Agent

Compares athletes using:

* Sport
* Event
* Sex
* Height
* Weight
* Training age
* Strength profile
* Power profile
* Technical profile
* Performance level
* Progress trends

Primary question:

"What do athletes like this usually need next?"

---

# Current Technical Foundation

## Frontend

* Vite
* React
* TypeScript

## Backend

* FastAPI

## Database

* Supabase

## Authentication

* Supabase Auth

---

# Current Database Concepts

* athletes
* metrics
* threshold_rules
* evaluation_runs
* intake_submissions
* athlete_profiles_inbox

---

# Current Service Architecture

## supabase_service.py

Database access only.

Responsibilities:

* Retrieve athletes
* Retrieve metrics
* Retrieve threshold rules
* Insert check-ins
* Insert evaluations
* Retrieve evaluation history

---

## scoring_service.py

Deterministic math only.

Responsibilities:

* Score metrics
* Assign bands
* Calculate averages

No database access.

---

## evaluation_service.py

Evaluation orchestration.

Responsibilities:

* Retrieve context
* Retrieve metrics
* Retrieve rules
* Call scoring_service
* Build EvaluationObject
* Persist evaluations

---

# Locked Architecture Rules

* Deterministic scoring remains backend code.
* AI does not assign scores.
* AI does not assign confidence.
* AI does not assign readiness.
* Service separation is mandatory.
* EvaluationObject is the source of truth.
* evaluation_runs.evaluation stores complete evaluation output.
* Raw intake submissions must always be stored.

---

# Current Development Status

## Completed

* Intake Layer
* Intake Agent
* Metric Registry
* Threshold Rules
* EvaluationObject
* Confidence Scoring MVP
* Evaluation Orchestration
* Evaluation History Retrieval
* Evaluation Persistence

## Not Yet Implemented

* ATS
* Archetypes
* Similarity Engine
* Program Builder
* Plan Safety
* Research Agent
* Nutritionist Agent
* Weightlifting Coach Agent
* Athletic Trainer Agent
* Technique Agent
* Head Coach Agent

---

# Final Product Goal

The Underdog Method should evolve into an AI-powered coaching staff for athletes who would not otherwise have access to one.

The final experience should explain:

* What is happening
* Why it is happening
* What should change
* What should happen next

Data

↓

Evaluation

↓

Specialist Analysis

↓

Head Coach Synthesis

↓

Individualized Athlete Guidance
