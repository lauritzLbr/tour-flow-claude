---
name: tour-flow
description: Work with the user's tour-flow workspace, the tour and event management app for live productions — tours, shows, day schedules, day sheets, hotels, crew, contacts, travel days, flights, off days, production sheets, notes, guest lists, todos, checklists, documents, show flows and setlists, booklets, meetings, projects and financials. Answer questions (what's on this week, where is the crew sleeping, when is load-in) and make changes there. Use whenever the user mentions tour-flow, a tour, a show, a venue, a run of show or anything they manage in tour-flow.
---

# tour-flow

The tour-flow MCP server works on the workspaces the user connected. When
the connection covers several, every tool takes an optional `workspace_id`
(the server instructions list them; without it the default workspace is
used). IDs only work in the workspace they come from. Every
call runs with the user's own permissions, so results only include what they
can see, and changes only succeed where they may edit in tour-flow. If the
user allowed read access only, the changing tools are not available.

## Which tool

1. **Named tools for everyday questions** (fast, compact):
   - `get_agenda`: "this week", "what's on tomorrow", "next show" (date
     range, max 92 days)
   - `get_day_sheet`: everything for one show day in one call (event,
     schedule, hotels, contacts, crew, notes)
   - `list_events`, `list_tours`, `get_tour`, `get_event`,
     `get_event_schedule`, `search`, `get_workspace`, `whoami`
   - `create_event`, `update_event`, `add_schedule_item`,
     `update_schedule_item`, `delete_schedule_item`
2. **Full API for everything else** — travel days, flights, off days,
   production sheets, guest lists, todos, checklists, documents, show flows,
   songs, booklets, crew messages and requests, meetings, projects,
   financials, templates, tab settings:
   1. `tourflow_api_catalog` without arguments lists the areas; with a `tag`
      or `query` (e.g. "hotel", "guest", "flight") it lists operations.
   2. `tourflow_api_describe` gives an operation's exact path parameters,
      query parameters and body schema. Always describe an operation before
      its first use: parameter names differ between operations (some use
      `id`, others `eventId`).
   3. Run it with the tool `tourflow_api_describe` names:
      `tourflow_api_read` (GET), `tourflow_api_write` (create or update) or
      `tourflow_api_replace_or_delete` (replaces a whole list or deletes).

## Finding things

- Never guess IDs. Get them from `list_tours`, `list_events`, `search` or a
  catalog list operation (`search` needs at least 2 characters).
- Lists are paginated; use `pageSize`, `page` or `cursor`, and filters like
  `q`, `tourId` or date ranges instead of reading everything.

## Times and timezones

Times are local to the event. Resolve the timezone as event → tour →
workspace (`get_workspace`). When the user is in another timezone, say which
zone a time is in instead of converting silently.

## Answering

- Day sheets: date, venue and city, schedule in time order, hotel, key
  contacts and notes. Leave out empty fields and sections marked unavailable.
- Keep German or other non-English notes as written.
- If a tool returns an error (missing permission, feature not on the plan,
  tab switched off, rate limit), tell the user plainly; don't retry in a loop.

## Making changes

Changes are visible to the whole crew right away, so be deliberate:

- Read the current state first and say what you are about to change.
- Prefer the smallest change: add or update single items (one schedule item,
  one contact, one todo) instead of sending whole lists.
- For updates that accept `expectedUpdatedAt`, pass the `updatedAt` you just
  read. A 412 means someone edited it meanwhile: re-read, show the user the
  difference, and ask before retrying.
- `tourflow_api_replace_or_delete` overwrites whole lists or removes data.
  Before using it, read the current data, show exactly what will be replaced
  or deleted, and wait for the user's explicit yes. When replacing a list
  (e.g. event hotels or contacts), send the complete new list: anything left
  out is removed.
- File uploads aren't possible through Claude; point the user to tour-flow.
