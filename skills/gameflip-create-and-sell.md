---
name: Create a listing and put it up for sale
description: Authenticate to Gameflip, create a marketplace listing, attach a photo, and confirm it is discoverable in search.
api: openapi/gameflip-gfapi-openapi.yml
operations: [getMyProfile, createListing, uploadListingPhoto, searchListings]
---

# Create and sell a listing on Gameflip

Use the GFAPI to list an item (game, in-game item, gift card, account, or service) for sale.

## Auth
Every request needs an `Authorization: GFAPI <apikey>:<totp>` header. The `<totp>`
is a fresh TOTP one-time password (SHA1, 6 digits, 30s period) computed from your
TOTP secret on each call. API keys + secrets come from Gameflip account settings
(Beta, gated). See `authentication/gameflip-authentication.yml`.

## Steps
1. **Confirm the account** — call `getMyProfile` (`GET /account/me/profile`) to verify
   credentials and read your seller profile.
2. **Create the listing** — call `createListing` (`POST /listing`) with the item's
   category, platform, name, description, price (integer cents), and tags. The
   response envelope returns `{status: SUCCESS, data: {id, ...}}`; keep `data.id`.
3. **Attach a photo** — call `uploadListingPhoto` (`POST /listing/{id}/photo`) with the
   listing id to add a cover/listing image.
4. **Verify discoverability** — call `searchListings` (`GET /listing`) filtered by
   `owner` (your account id) or `term` to confirm the listing appears.

## Rules
- Responses always carry a `status` field (`SUCCESS`/`FAILURE`); on failure read
  `error.message` and `error.code` (see `errors/gameflip-error-codes.yml`).
- Search results are cursor-paginated: follow `next_page`, use `limit` (default 20,
  max 100). See `conventions/gameflip-conventions.yml`.
- Pace requests; the official client self-throttles to ~3 requests/minute during Beta.
- Use the correct category per Gameflip's terms of service.
