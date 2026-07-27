---
name: Search the marketplace and buy via an exchange
description: Search Gameflip listings, inspect a listing, and create an exchange (sell/buy record) to purchase it.
api: openapi/gameflip-gfapi-openapi.yml
operations: [searchListings, getListing, createExchange]
---

# Search and buy on Gameflip

Use the GFAPI to find a listing and purchase it by creating an exchange.

## Auth
Send `Authorization: GFAPI <apikey>:<totp>` on every request (fresh TOTP each call).
See `authentication/gameflip-authentication.yml`.

## Steps
1. **Search** — call `searchListings` (`GET /listing`) with filters such as `term`,
   `category`, `platform`, `price` (range), `digital`, and `status`. Order with
   `sort` (default `_score:desc,created:desc`). Page via `next_page` / `limit`.
2. **Inspect** — call `getListing` (`GET /listing/{id}`) for the chosen `data.id` to
   read full details and current status before buying.
3. **Buy** — call `createExchange` (`POST /exchange`) referencing the listing to open a
   sell/buy record. Track the returned exchange `data.id` and its status through the
   exchange lifecycle.

## Rules
- Default search returns listings with status `onsale` or `sold` and not expired.
- `tags` supports OR (comma) and AND (`^`) logic, e.g. `tags=a,b^c`.
- Check the `status`/`error` envelope on every response
  (`errors/gameflip-error-codes.yml`).
- Prices are integers in cents; respect cursor pagination and request pacing
  (`conventions/gameflip-conventions.yml`).
