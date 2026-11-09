# Quickstart: MVP RSS Reader

This guide validates the add-and-list subscription flow locally. The planned
application projects are `backend/RSSFeedReader.Api` and
`frontend/RSSFeedReader.UI`; create them as implementation begins if they do not
exist yet.

## Prerequisites

- .NET 10 SDK installed.
- A modern browser.
- Playwright browser installed for the end-to-end test.
- Two local processes/terminals available for the API and UI.

## Configure and run

1. Ensure the API launch settings use `http://localhost:5151` and the UI uses
   `http://localhost:5213`, or update both to the chosen local ports.
2. Set the UI API base URL to the API's actual base address and configure API
   CORS for the exact UI origin.
3. From the repository root, start the API:

   ```sh
   dotnet run --project backend/RSSFeedReader.Api
   ```

4. In a second terminal, start the UI:

   ```sh
   dotnet run --project frontend/RSSFeedReader.UI
   ```

5. Open the UI URL printed by the frontend process.

## Validate the API contract

With the API running on port 5151, verify the empty list:

```sh
curl --fail http://localhost:5151/api/subscriptions
```

Expected response: HTTP 200 and an empty JSON array (`[]`).

Add a URL without contacting the feed:

```sh
curl --fail \
  -H 'Content-Type: application/json' \
  -d '{"url":"https://example.com/feed.xml"}' \
  http://localhost:5151/api/subscriptions
```

Expected response: HTTP 204 with no response body.

Read the list again:

```sh
curl --fail http://localhost:5151/api/subscriptions
```

Expected response: HTTP 200 with the submitted entry. Repeat the POST and verify
that the same URL appears twice.

## Validate the user flow

1. Open a fresh UI session and confirm that the list is empty.
2. Submit a valid feed URL and confirm it appears without a page reload.
3. Submit the same URL again and confirm that a second entry appears.
4. Add enough URLs to reach 10 entries and confirm all 10 are visible.
5. Refresh the browser while the API remains running and confirm the list is
   loaded from the API.
6. Stop and restart the API, then reload the UI. Confirm that the list is empty.
7. With the API stopped, attempt to load or add a subscription and verify the UI
   shows a clear, non-sensitive failure message.
8. Confirm no request to a feed URL is made when adding a subscription and that
   no feed items are displayed.

## Automated checks

With the backend and frontend running, execute the API unit/integration tests
and browser integration test:

```sh
dotnet test backend/RSSFeedReader.Api.Tests
dotnet test tests/RSSFeedReader.E2E
```

Then build both application projects:

```sh
dotnet build
```

Expected outcome: store and API tests cover empty results, adding a URL,
ordered results, repeated URLs, and clearing state with a fresh store instance.
The browser integration test confirms the form sends a URL to the API and shows
the updated list. Both application projects build without errors.

See [`data-model.md`](./data-model.md) and
[`contracts/subscriptions-api.md`](./contracts/subscriptions-api.md) for the
data and HTTP behavior being validated.
