# Implementation Plan: MVP RSS Reader

**Branch**: `001-mvp-rss-reader` | **Date**: 2026-11-09 | **Spec**:
[`spec.md`](./spec.md)

**Input**: Feature specification from
`/specs/001-mvp-rss-reader/spec.md`

## Summary

Deliver the single-user MVP subscription workflow: accept feed URLs, keep each
entry in memory for the lifetime of the local backend process, and show the
current list in the UI. Use the documented ASP.NET Core Web API and Blazor
WebAssembly split, with one list endpoint and one add endpoint. The MVP does not
fetch or parse feeds, validate URLs, persist subscriptions, or display feed
items.

## Technical Context

**Language/Version**: C# on .NET 10 LTS; pin the SDK when creating the solution.

**Primary Dependencies**: ASP.NET Core Web API, Blazor WebAssembly, and the .NET
shared framework. No feed parsing, database, or UI component package is needed.

**Storage**: Process-local in-memory subscription list; all entries are lost
when the backend process stops.

**Testing**: xUnit for backend unit/integration tests using ASP.NET Core's test
host, plus a focused Playwright browser integration test for the UI-to-API
subscription flow.

**Target Platform**: Local development on Windows, macOS, and Linux; backend and
frontend run as separate local processes.

**Project Type**: Web application with an ASP.NET Core API backend and a Blazor
WebAssembly frontend.

**Performance Goals**: For one local user and at least 10 added entries, the
list reflects each successful add on the next response and UI update; no
throughput or latency target beyond responsive local interaction is required.

**Constraints**: Do not fetch, parse, or render remote feed content. Do not
validate subscription URLs, remove entries, persist data across backend
restarts, or add background work. Keep API base URL and CORS origins aligned
with local launch settings.

**Scale/Scope**: One local user, one subscription-list screen, and add/list
operations only.

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

- **Security at External Boundaries — PASS**: Subscription URLs remain inert
  strings; no outbound feed requests or remote content processing are in scope.
  Configure CORS for the actual local UI origin only.
- **MVP Scope and Incremental Delivery — PASS**: Only add and list subscriptions
  are planned. Fetching, parsing, persistence, removal, and background polling
  are explicitly excluded.
- **Maintainable Separation and Simplicity — PASS**: The API owns the
  process-local list and API behavior; the Blazor UI owns input and display.
  The design adds no database, feed library, or unnecessary abstraction.
- **Verifiable Code Quality — PASS**: Cover store behavior and API contracts
  with automated tests; cover the UI-to-API flow with a browser integration
  test; build both application projects.
- **Clear and Reliable User Behavior — PASS**: The UI reloads the list on
  startup and after a successful add. It reports a simple actionable message
  if the local API cannot be reached without exposing internal details.

**Pre-design gate**: PASS. No constitution violations or unresolved product
decisions block design.

## Project Structure

The repository currently contains stakeholder and Spec Kit documentation but
no application projects. The following is the planned layout, based on the
project's technology and port configuration documentation.

### Documentation (this feature)

```text
specs/001-mvp-rss-reader/
├── plan.md
├── research.md
├── data-model.md
├── quickstart.md
├── contracts/
│   └── subscriptions-api.md
└── tasks.md
```

### Source Code (repository root)

```text
RSSFeedReader.sln
backend/
├── RSSFeedReader.Api/
│   ├── Program.cs
│   ├── Services/
│   │   └── SubscriptionStore.cs
│   └── Properties/
│       └── launchSettings.json
└── RSSFeedReader.Api.Tests/
    └── SubscriptionEndpointsTests.cs
frontend/
└── RSSFeedReader.UI/
    ├── Pages/
    │   └── Subscriptions.razor
    ├── Services/
    │   └── SubscriptionsClient.cs
    ├── Properties/
    │   └── launchSettings.json
    └── wwwroot/
        └── appsettings.json
tests/
└── RSSFeedReader.E2E/
    └── SubscriptionFlowTests.cs
```

**Structure Decision**: Use two application projects, as specified by the
stakeholder technology documents: a separately hosted ASP.NET Core API and
Blazor WebAssembly UI. Keep the in-memory store in the API and add focused API
integration tests. Remove any template demo pages that conflict with the
subscription landing route when scaffolding the UI.

## Complexity Tracking

No constitution violations. No additional complexity is introduced.

## Phase 0: Research

Research decisions and sources are recorded in
[`research.md`](./research.md). The principal design decisions are to use the
documented separate API/UI architecture, a small in-memory list, and a minimal
JSON contract. The target is .NET 10 LTS, the supported LTS release as of the
plan date.

## Phase 1: Design and Contracts

- [`data-model.md`](./data-model.md) defines the process-local subscription
  entry and list behavior.
- [`contracts/subscriptions-api.md`](./contracts/subscriptions-api.md) defines
  the list and add HTTP operations.
- [`quickstart.md`](./quickstart.md) describes build, run, and acceptance
  validation for local development.

**Post-design gate**: PASS. The design retains the in-memory-only scope, adds no
remote feed processing, defines tests for the API/UI boundary, and keeps local
API and CORS configuration explicit.
