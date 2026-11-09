# Subscriptions API Contract

Base path: `/api/subscriptions`

The API accepts and returns JSON. It stores URLs only; it does not fetch or
parse feed content.

## List subscriptions

`GET /api/subscriptions`

Returns `200 OK` with the subscriptions in successful-add order. An empty
collection is returned when the API process has just started.

```json
[
  { "url": "https://example.com/feed.xml" },
  { "url": "https://example.com/feed.xml" }
]
```

Repeated URLs are separate entries and MUST remain in the returned list.

## Add a subscription

`POST /api/subscriptions`

Request body:

```json
{
  "url": "https://example.com/feed.xml"
}
```

On success, append the submitted URL and return `204 No Content` with no
response body. The client then requests the collection to display the updated
list.

The MVP assumes a valid, non-empty URL. It does not validate the URL or make a
network request to it. Repeating the request creates another entry.

## Runtime and configuration behavior

- The list is held in memory by the backend and is cleared when that process
  stops.
- The frontend API base URL is supplied by its local configuration.
- Backend CORS allows the configured local frontend origin only.
- The API exposes no item, removal, persistence, or refresh operations in this
  feature.
