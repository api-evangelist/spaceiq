---
name: Explore the workplace hierarchy and find people
description: Traverse the SpaceIQ (SiQ) space hierarchy — company, buildings, floors, spaces — and look up departments and employees, including bulk export.
api: https://api.spaceiq.com/queries
operations:
  - viewer
  - company
  - building
  - floor
  - space
  - department
  - employee
  - employeeExport
---

# Explore the workplace hierarchy and find people (SpaceIQ / SiQ)

Read the SiQ workplace graph to answer questions like "which floor is a team on"
or "list all employees in a department".

## Prerequisites
- Access token from the SiQ web app sent as `Authorization: bearer: <token>`.
- All calls are `POST https://api.spaceiq.com/queries` with
  `{ "query": "<graphql>" }` and `Content-Type: application/json`.

## Steps
1. **Anchor** — run `viewer` to get the current user and their `company`.
2. **Walk the hierarchy** — from `company`, query `building`, then `floor`, then
   `space`. Each level is a Relay-style connection (Connection > Edge > Node), so
   read `edges { node { ... } }` and page with cursors for large sites.
3. **Departments and people** — query `department` for organizational units and
   `employee` for individuals; employees relate to a department and an assigned
   space.
4. **Bulk export** — for reporting or sync, use `employeeExport` to pull employee
   records in bulk rather than paging the `employee` connection.
5. **Search** — when you know a name or attribute, use the search entities
   (`EmployeeSearch`, `SpaceSearch`, etc.) instead of a full traversal.

## Conventions
- Only request the fields you need — GraphQL returns exactly the selected shape.
- Blank fields come back as explicit `null`, not omitted.
- Discover exact field names via introspection (`__type(name: "Company")`) before
  building a query; the schema is version 1.7 and fully introspectable.
- Check the top-level `errors` array on every response.
