# Frontend Architecture

## Purpose

The Underdog frontend is responsible for:

* Athlete data collection
* Athlete onboarding
* Check-in submission
* Profile management
* Evaluation display
* Future coaching interaction

The frontend should remain lightweight.

Business logic belongs in the backend.

The frontend should collect, display, and submit data.

---

# Technology Stack

## Framework

* React
* TypeScript
* Vite

## Authentication

* Supabase Auth

## API Communication

* REST API
* FastAPI backend

---

# Repository Structure

```text
src/
├── api/
├── assets/
├── components/
├── contexts/
├── hooks/
├── lib/
├── pages/
└── test/
```

---

# API Layer

Location:

```text
src/api/
```

Purpose:

Frontend communication with backend services.

API files should:

* Build requests
* Handle responses
* Normalize frontend data

API files should NOT:

* Contain scoring logic
* Contain business rules
* Calculate evaluations

---

## client.ts

Purpose:

Central API client configuration.

Responsibilities:

* base URLs
* request configuration
* shared API behavior

---

## health.ts

Purpose:

Backend health checks.

---

## profile.ts

Purpose:

Profile API communication.

Examples:

* GET profile
* PATCH profile

---

## intake.ts

Purpose:

Submit intake payloads.

---

## score.ts

Purpose:

Retrieve evaluation results.

---

## onboard.ts

Purpose:

Handle onboarding workflows.

---

## admin.ts

Purpose:

Administrative API actions.

---

## research.ts

Future implementation.

Not currently active.

---

# Assets

Location:

```text
src/assets/
```

Purpose:

Static application resources.

Examples:

* logos
* icons
* branding

---

# Components

Location:

```text
src/components/
```

Purpose:

Reusable UI building blocks.

Components should:

* remain presentation-focused
* receive data through props
* avoid business logic

---

# UI Components

Location:

```text
src/components/ui/
```

---

## ATSGauge.tsx

Purpose:

Display athlete score visually.

Note:

Current ATS implementation is deferred.

Do not expand ATS functionality unless requested.

---

## BandBadge.tsx

Purpose:

Display classification bands.

Examples:

* U
* V
* W
* X
* Y

---

## BackendStatus.tsx

Purpose:

Display backend availability.

---

## FormCard.tsx

Purpose:

Reusable form container.

---

## HeightInput.tsx

Purpose:

Height data entry.

---

## Navbar.tsx

Purpose:

Application navigation.

---

## NavLink.tsx

Purpose:

Navigation helper component.

---

## ProtectedRoute.tsx

Purpose:

Authentication-protected routes.

---

## AdminRoute.tsx

Future implementation.

Administrative route protection.

---

## SportEventFields.tsx

Purpose:

Sport-specific form fields.

Examples:

* throws
* jumps
* sprints
* future sport support

---

# Context Layer

Location:

```text
src/contexts/
```

---

## AuthContext.tsx

Purpose:

Application authentication state.

Responsibilities:

* current user
* login status
* auth lifecycle

---

# Hooks Layer

Location:

```text
src/hooks/
```

Purpose:

Reusable React logic.

---

## use-mobile.tsx

Purpose:

Responsive behavior.

---

## use-toast.ts

Purpose:

Notification handling.

---

# Utility Layer

Location:

```text
src/lib/
```

Purpose:

Shared utilities.

---

## profileCompleteness.ts

Purpose:

Calculate profile completion state.

---

## sportEvents.ts

Purpose:

Sport and event configuration.

---

## supabase.ts

Purpose:

Supabase client configuration.

---

## utils.ts

Purpose:

General utility helpers.

---

# Page Layer

Location:

```text
src/pages/
```

Purpose:

Application screens.

Pages should:

* orchestrate UI
* call services
* manage state

Pages should NOT:

* perform scoring
* perform evaluation calculations
* contain business rules

---

## Home.tsx

Landing page.

---

## Demo.tsx

Demonstration page.

---

## Research.tsx

Future research interface.

---

## Onboard.tsx

Athlete onboarding experience.

---

## Login.tsx

Authentication page.

---

## Verify.tsx

Account verification.

---

## Dashboard.tsx

Primary athlete dashboard.

Current responsibilities:

* display latest evaluation
* display readiness information
* display score information

Future responsibilities:

* historical trends
* evaluation history
* progress analytics

---

## Profile.tsx

Athlete profile management.

---

## CheckIn.tsx

Athlete check-in workflow.

Current role:

* submit check-ins
* trigger evaluation pipeline

---

## AskCoach.tsx

Future coaching interaction page.

---

## NotFound.tsx

404 handling.

---

# Admin Pages

Location:

```text
src/pages/admin/
```

---

## AdminAthletes.tsx

Administrative athlete list.

---

## AdminAthleteDetail.tsx

Administrative athlete detail view.

---

# Testing Layer

Location:

```text
src/test/
```

Purpose:

Frontend testing.

---

## setup.ts

Shared test configuration.

---

## example.test.ts

Example testing structure.

Replace with production test coverage over time.

---

# Frontend Philosophy

The frontend is responsible for:

* collecting information
* displaying information
* submitting information

The frontend is NOT responsible for:

* scoring athletes
* assigning confidence
* assigning readiness
* calculating evaluations

All evaluation logic belongs in the backend.

---

# Current Athlete Flow

Athlete

↓

Onboard.tsx

↓

Profile.tsx

↓

CheckIn.tsx

↓

Backend Evaluation

↓

Dashboard.tsx

↓

Future Coaching Experience

---

# Current Phase

Phase 1 — Intake + Evaluation Layer

Implemented:

* Authentication
* Profile Management
* Onboarding
* Check-In Flow
* Dashboard Score Display
* Intake Submission

In Progress:

* Deterministic Evaluation Stabilization

Not Yet Implemented:

* Historical Trends
* Program Builder
* Specialist Agents
* Head Coach Experience
* Adaptive Intelligence

---

# Locked Frontend Rules

## Rule 1

Scoring remains backend logic.

---

## Rule 2

Evaluation logic remains backend logic.

---

## Rule 3

The frontend displays evaluations but does not calculate them.

---

## Rule 4

Business rules belong in backend services.

---

## Rule 5

Future coaching recommendations should originate from backend systems, not frontend calculations.
