<!--
Sync Impact Report
Version change: Uninitialized template → 1.0.0
Modified principles: None; this is the first project-specific constitution.
Added sections: Core Principles (five principles), Technology and Scope,
Development Workflow and Quality Gates, Governance.
Removed sections: None.
Follow-up TODOs: Confirm the original ratification date and replace the
RATIFICATION_DATE TODO.
-->

# RSS Feed Reader Constitution

## Core Principles

### I. Security at External Boundaries
MVP subscriptions MUST be treated as inert URLs: the MVP MUST NOT make network
requests, parse remote feeds, or render remote content. Any later feature that
fetches feeds MUST set bounded request timeouts, handle network and parsing
failures without exposing internals, and treat feed-provided text and links as
untrusted input. Remote HTML MUST NOT be rendered as trusted markup without
sanitization. Secrets MUST NOT be committed to source control, and backend CORS
MUST allow only the frontend origins required by the active local configuration.
These rules keep the initial scope safe and establish explicit safeguards before
the application processes remote content.

### II. MVP Scope and Incremental Delivery
The MVP MUST support adding a subscription by URL and displaying the current
subscription list. It MUST NOT add feed fetching, feed parsing, persistence,
subscription removal, or background polling unless those capabilities are
approved as a later phase. Features MUST be delivered in small increments with
acceptance criteria matching the active phase. This keeps the proof of concept
focused and prevents future-facing architecture from delaying its core behavior.

### III. Maintainable Separation and Simplicity
The application MUST preserve the documented separation between the ASP.NET Core
Web API, which owns subscription data and API behavior, and the Blazor WebAssembly
UI, which owns user interaction and presentation. Shared behavior MUST have one
clear owner; abstractions MUST be introduced only when they reduce duplication
or isolate a real change boundary. API addresses, ports, and allowed frontend
origins MUST be configured rather than scattered as hard-coded values. This
keeps the MVP easy to change without blocking the documented extension path.

### IV. Verifiable Code Quality
Every behavior change MUST be covered by tests at the narrowest meaningful
level; API contracts and frontend-to-backend behavior MUST receive integration
coverage when changed. Changes MUST build without new errors, and existing
automated tests MUST pass before completion. New dependencies MUST be justified
by an active requirement, not a deferred feature. These checks make correctness
observable while keeping the small project maintainable.

### V. Clear and Reliable User Behavior
The UI MUST reflect a successfully added subscription in the displayed list
without requiring a page reload. When a later feature introduces network or
other fallible operations, the UI MUST communicate failure in user-appropriate
terms and the implementation MUST retain actionable diagnostics without
exposing sensitive details. Local development MUST use consistent API and
frontend configuration, including matching API base URLs and CORS origins.
Predictable feedback and configuration make the app usable and straightforward
to verify across supported desktop platforms.

## Technology and Scope

The project uses an ASP.NET Core Web API backend and a Blazor WebAssembly
frontend written in C#. The MVP stores subscriptions in memory and is expected
to run locally on Windows, macOS, and Linux. Feed operations, durable storage,
and background processing are out of MVP scope; adopting them requires an
approved feature-phase change and the safeguards in this constitution.

## Development Workflow and Quality Gates

Before implementation, changes MUST identify the active product phase and its
acceptance criteria. Before completion, contributors MUST run the relevant
automated tests and build, and verify any changed API/UI integration locally.
Configuration changes MUST be checked against the actual launch ports, API base
URL, and CORS origins. A change that adds remote feed handling MUST include
tests for failure behavior and safe treatment of feed-provided content.

## Governance

This constitution governs project decisions and takes precedence over
conflicting development guidance. Amendments MUST be made explicitly in this
file, reviewed for impact on existing features and deferred phases, and reflected
in the version and amendment date. Versioning follows semantic versioning:
MAJOR for incompatible governance changes, MINOR for new or materially expanded
principles or requirements, and PATCH for clarifications that do not change
project obligations. Contributors and reviewers MUST check proposed changes
against the principles and quality gates; any exception MUST state its rationale
and scope in the change review.

**Version**: 1.0.0 | **Ratified**: TODO(RATIFICATION_DATE): confirm original adoption date | **Last Amended**: 2026-11-09
