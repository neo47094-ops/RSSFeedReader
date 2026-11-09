# Tasks: MVP RSS Reader

**Input**: Design documents from
`/specs/001-mvp-rss-reader/`

**Prerequisites**: `plan.md`, `spec.md`, `research.md`, `data-model.md`,
`contracts/subscriptions-api.md`, `quickstart.md`

**Organization**: Tasks are grouped by user story. Tests are included because
the project constitution requires unit and integration coverage.

## Phase 1: Setup

**Purpose**: Create the application and test projects defined by the plan.

- [ ] T001 Create `RSSFeedReader.sln`, the .NET 10 ASP.NET Core API project at `backend/RSSFeedReader.Api/RSSFeedReader.Api.csproj`, the Blazor WebAssembly project at `frontend/RSSFeedReader.UI/RSSFeedReader.UI.csproj`, and test projects at `backend/RSSFeedReader.Api.Tests/RSSFeedReader.Api.Tests.csproj` and `tests/RSSFeedReader.E2E/RSSFeedReader.E2E.csproj`.
- [ ] T002 Pin the installed .NET 10 SDK in `global.json` and add the project references and test dependencies in `backend/RSSFeedReader.Api.Tests/RSSFeedReader.Api.Tests.csproj` and `tests/RSSFeedReader.E2E/RSSFeedReader.E2E.csproj`: reference the API project, use xUnit and `Microsoft.AspNetCore.Mvc.Testing` for API tests, and use Playwright for the browser test project.

---

## Phase 2: Foundational

**Purpose**: Configure the local API/UI connection and remove template UI
conflicts before story implementation.

- [ ] T003 Configure backend/frontend launch ports, the frontend API base URL, and backend CORS for the exact frontend origin in `backend/RSSFeedReader.Api/Properties/launchSettings.json`, `backend/RSSFeedReader.Api/Program.cs`, `frontend/RSSFeedReader.UI/Properties/launchSettings.json`, and `frontend/RSSFeedReader.UI/wwwroot/appsettings.json`.
- [ ] T004 Remove the Blazor template demo pages `frontend/RSSFeedReader.UI/Pages/Home.razor`, `frontend/RSSFeedReader.UI/Pages/Counter.razor`, and `frontend/RSSFeedReader.UI/Pages/Weather.razor`, then remove their links and set the subscription landing-page navigation label in `frontend/RSSFeedReader.UI/Layout/NavMenu.razor`.

**Checkpoint**: Solution projects and local origins are configured; no
demonstration page competes with the subscription landing route.

---

## Phase 3: User Story 1 - Add and View Feed Subscriptions (Priority: P1) 🎯 MVP

**Goal**: Let one local user add feed URLs and see the ordered list, including
repeated URLs, for the current backend process.

**Independent Test**: Start with an empty backend, submit one URL, and verify
that it appears in the UI without a page reload. Add a second URL and a repeated
URL and verify that all entries appear in add order. Restart the backend and
verify that the list is empty.

### Tests for User Story 1

Write the tests first and verify they fail before implementing the store,
endpoints, or page.

- [ ] T005 [P] [US1] Add store unit tests for an empty initial list, append order, repeated URLs, and a fresh store starting empty in `backend/RSSFeedReader.Api.Tests/SubscriptionStoreTests.cs`.
- [ ] T006 [P] [US1] Add API integration tests for `GET /api/subscriptions` returning an empty list, `POST /api/subscriptions` returning 204, and subsequent GET preserving order and repeated URLs in `backend/RSSFeedReader.Api.Tests/SubscriptionEndpointsTests.cs`.
- [ ] T007 [P] [US1] Add a Playwright flow test that submits 10 distinct URLs and a repeated URL, then verifies each entry appears without a page reload using the “Feed URL” input, “Add subscription” button, and “Subscriptions” list in `tests/RSSFeedReader.E2E/SubscriptionFlowTests.cs`.

### Implementation for User Story 1

- [ ] T008 [P] [US1] Define the subscription JSON model with a required `url` string and no URL validation in `backend/RSSFeedReader.Api/Models/Subscription.cs`.
- [ ] T009 [US1] Implement a thread-safe, ordered, process-local subscription store that appends repeated entries and returns a snapshot in `backend/RSSFeedReader.Api/Services/SubscriptionStore.cs`.
- [ ] T010 [US1] Register the singleton store and implement `GET /api/subscriptions` and `POST /api/subscriptions` with the contract's JSON shape and 204 success response in `backend/RSSFeedReader.Api/Program.cs`.
- [ ] T011 [P] [US1] Implement a frontend API client that loads the subscription collection and posts a URL, surfacing connection or API failures for the UI to report in `frontend/RSSFeedReader.UI/Services/SubscriptionsClient.cs`.
- [ ] T012 [US1] Implement the root subscription page with an accessible “Feed URL” input, an “Add subscription” button, an ordered list named “Subscriptions”, immediate refresh after a successful add, and a clear non-sensitive API failure message in `frontend/RSSFeedReader.UI/Pages/Subscriptions.razor`.

**Checkpoint**: User Story 1 is independently demonstrable. The API accepts
inert URL strings only; it does not fetch or parse feeds. The list resets when
the backend process restarts.

---

## Phase 4: Polish & Cross-Cutting Concerns

**Purpose**: Verify the finished MVP against the plan, contract, and local
development guide.

- [ ] T013 Run the API tests, Playwright flow test, solution build, and all applicable scenarios in `specs/001-mvp-rss-reader/quickstart.md`; resolve any failures without adding feed fetching, URL validation, persistence, removal, or background polling.

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No prerequisite; T001 precedes T002.
- **Foundational (Phase 2)**: Depends on project setup. T003 and T004 work on
  separate configuration/UI files and can proceed in parallel after T001.
- **User Story 1 (Phase 3)**: Depends on setup and foundation. T005, T006, and
  T007 are written before implementation and can proceed in parallel. T008 and
  T011 can proceed in parallel after the tests are authored. T009 depends on
  T008; T010 depends on T009; T012 depends on T011 and T010.
- **Polish (Phase 4)**: Depends on all User Story 1 implementation and test
  tasks.

### User Story Dependencies

- **User Story 1 (P1)**: Depends only on Setup and Foundational phases. No other
  user stories are defined in this MVP.

### Parallel Opportunities

- After T001, T003 and T004 can run in parallel because they modify distinct
  configuration and UI-template files.
- After T002 and the foundation, T005, T006, and T007 can run in parallel; each
  test task owns a separate file.
- After the story tests are authored, T008 and T011 can run in parallel; they
  define the backend model and frontend client in separate projects.

## Parallel Example: User Story 1

```text
After setup and foundation:
Task: T005 store unit tests in backend/RSSFeedReader.Api.Tests/SubscriptionStoreTests.cs
Task: T006 API integration tests in backend/RSSFeedReader.Api.Tests/SubscriptionEndpointsTests.cs
Task: T007 browser flow test in tests/RSSFeedReader.E2E/SubscriptionFlowTests.cs

After story tests are authored:
Task: T008 subscription model in backend/RSSFeedReader.Api/Models/Subscription.cs
Task: T011 frontend API client in frontend/RSSFeedReader.UI/Services/SubscriptionsClient.cs
```

## Implementation Strategy

### MVP First (User Story 1 Only)

1. Complete Setup and Foundational phases.
2. Write the store, API, and browser tests; confirm they fail before feature
   implementation.
3. Implement the subscription model, store, API endpoints, client, and page.
4. Run all User Story 1 tests and validate the quickstart scenarios.
5. Stop at this scope: feed fetching, parsing, URL validation, persistence,
   removal, and background polling are deferred.

### Incremental Delivery

This specification defines one user story. Complete and validate User Story 1
as the MVP; additional stories require a later specification.

## Notes

- `[P]` marks tasks that can proceed in parallel without editing the same file.
- `[US1]` maps tasks to the sole story in `spec.md`.
- Every task uses the required checkbox, sequential ID, optional parallel/story
  labels, and explicit file path(s).
