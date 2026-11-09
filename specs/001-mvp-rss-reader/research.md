# Research: MVP RSS Reader

## Decisions

### Separate ASP.NET Core API and Blazor WebAssembly UI

- **Decision**: Keep the API and browser UI as separate local applications.
- **Rationale**: This is the architecture specified in the stakeholder
  technology document. The backend owns the subscription list; the UI handles
  user input and display. Keep the implementation focused on these
  responsibilities rather than adopting a combined hosting model.
- **Alternatives considered**: A single server-rendered app could reduce
  configuration, but would diverge from the selected stack and require
  reworking the UI approach.

### In-memory process-local subscription store

- **Decision**: Store an ordered collection of URL strings in the API process,
  preserve repeated entries, and clear the collection when the API process
  stops.
- **Rationale**: The feature specification explicitly excludes persistence and
  duplicate filtering. A small thread-safe collection is sufficient for one
  local user and safely handles overlapping HTTP requests without adding
  external infrastructure.
- **Alternatives considered**: A database or browser storage would retain data
  across sessions, violating MVP scope. A plain unsynchronized list is less
  safe under concurrent requests.

### Minimal collection API

- **Decision**: Expose `GET /api/subscriptions` for the ordered list and
  `POST /api/subscriptions` to append one URL. Use JSON and return `204 No
  Content` for a successful add; the client then reloads the collection. See
  [`contracts/subscriptions-api.md`](./contracts/subscriptions-api.md).
- **Rationale**: These operations directly support the required add-and-view
  workflow without feed-specific endpoints or additional entity operations.
- **Alternatives considered**: Client-only storage would avoid API calls but
  contradict the selected backend ownership and API/UI separation. Per-item
  routes and unique URL constraints add complexity and conflict with duplicate
  entries.

### No URL validation or feed retrieval in this phase

- **Decision**: Treat submitted values as user-provided feed URLs and store
  them without contacting or parsing remote feeds.
- **Rationale**: The MVP specification assumes valid input and explicitly
  excludes URL validation, fetching, and parsing. This also keeps external
  content outside the application's trust boundary for this feature.
- **Alternatives considered**: URL validation and test fetches might catch
  errors earlier, but expand scope and introduce network behavior the MVP
  explicitly defers.

### .NET 10 LTS and API/UI integration verification

- **Decision**: Target .NET 10 LTS, use xUnit for backend tests, test HTTP
  behavior through ASP.NET Core's test host, and use a focused Playwright test
  to verify the browser-to-API workflow. Pin the SDK when the solution is
  scaffolded.
- **Rationale**: .NET 10 is the active LTS release as of the plan date. The
  test-host approach validates endpoint behavior without external feed
  services; one browser integration test verifies the connected UI-to-API
  workflow required by the project's quality gates.
- **Alternatives considered**: Targeting a prior .NET version provides no
  benefit for a new project and may be outside its support lifecycle. Manual
  browser checks alone would not provide repeatable integration coverage.

## Sources

- Microsoft, [.NET and .NET Core support policy](https://dotnet.microsoft.com/en-us/platform/support/policy/dotnet-core):
  .NET 10 is an active LTS release with support through November 14, 2028.
- Microsoft, [Minimal APIs quick reference](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/minimal-apis?view=aspnetcore-10.0):
  ASP.NET Core's built-in endpoint model supports a minimal API surface.
- Microsoft, [Dependency injection in ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-10.0):
  use built-in dependency injection to share application services.
- Microsoft, [ASP.NET Core Blazor forms overview](https://learn.microsoft.com/en-us/aspnet/core/blazor/forms/?view=aspnetcore-10.0):
  Blazor provides built-in form and input components for the subscription form.
- Microsoft, [Integration tests in ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/test/integration-tests?view=aspnetcore-10.0):
  ASP.NET Core supports HTTP integration testing with an in-memory test server
  and recommends tools such as Playwright for SPA browser automation.

## Resolved Unknowns

- **Runtime baseline**: .NET 10 LTS selected; pin SDK and target frameworks at
  project creation.
- **API surface**: One collection GET and one add POST, specified in the API
  contract.
- **Storage lifetime**: API process lifetime, consistent with the no-persistence
  requirement.
- **Testing approach**: Backend unit and API integration tests plus automated
  browser integration coverage for the UI-to-API subscription flow.
