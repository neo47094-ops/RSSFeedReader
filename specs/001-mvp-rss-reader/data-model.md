# Data Model: MVP RSS Reader

## Subscription

Represents one user-added feed URL in the current API process.

| Field | Type | Required | Rules |
|---|---|---:|---|
| `url` | String | Yes | Contains the user-submitted feed URL. The MVP assumes it is valid and does not validate or fetch it. |

### Identity and duplicates

- Each successful add creates a distinct list entry.
- URL equality does not define uniqueness; repeated URLs are allowed.
- The list preserves successful add order.

### Lifecycle

1. A successful add appends the URL to the in-memory list.
2. The entry remains available while the API process is running.
3. Stopping the API process discards all entries; starting it again creates an
   empty list.

## Subscription List

An ordered collection of `Subscription` entries owned by the API process.

- Starts empty when the API process starts.
- Supports append and read-all operations only.
- Has no persistence, removal, feed-item relationships, or background updates.
- The UI reads the collection at page load and after a successful add.
