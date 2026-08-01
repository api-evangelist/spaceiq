---
name: Book a meeting room
description: Find an available meeting room in SpaceIQ (SiQ) and reserve it by creating a calendar event, with the ability to cancel.
api: https://api.spaceiq.com/queries
operations:
  - meetingRoomAvailabilitySearch
  - createCalendarEvent
  - deleteCalendarEvent
---

# Book a meeting room (SpaceIQ / SiQ)

Use the SiQ GraphQL API to find and reserve a meeting room.

## Prerequisites
- An access token generated in the SiQ web application (Integration page, by an
  Admin or IT user). Send it on every request:
  `Authorization: bearer: <token>`.
- All calls are `POST https://api.spaceiq.com/queries` with a JSON body
  `{ "query": "<graphql>" }` and header `Content-Type: application/json`.

## Steps
1. **Confirm context** — run the `viewer` query to confirm the token is valid and
   read the associated `company`. A 401 with `{"error":"access_token does not
   correspond to user"}` means the token is invalid or disabled.
2. **Find an available room** — run the Meeting Room Availability Search
   (`meetingRoomAvailabilitySearch`), passing the target `buildings` and the
   desired time window. It returns rooms that are free in that window.
3. **Reserve it** — call the `createCalendarEvent` mutation with the chosen
   meeting-room space and the event start/end (ISO 8601, `YYYY-MM-DDTHH:MM:SSZ`)
   and read back the returned event fields to confirm.
4. **Cancel if needed** — call the `deleteCalendarEvent` mutation with the event
   identifier to release the room.

## Conventions
- Timestamps are ISO 8601; send and expect UTC `Z` times.
- GraphQL returns HTTP 200 with any operation problems in the top-level `errors`
  array — always check `errors` before assuming success (see
  `errors/spaceiq-problem-types.yml`).
- There is no idempotency-key mechanism; do not blindly retry
  `createCalendarEvent` on a timeout — re-query availability first to avoid
  duplicate reservations.
