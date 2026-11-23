---
name: tour-flow
description: Answer questions about the user's tours, shows and day schedules from their tour-flow workspace — what's on today or this week, where the next show is, load-in/doors/show times, which events a tour has, day sheets and run-of-show summaries — and make changes there: create events, change event details, add, move or remove day schedule items. Use whenever the user mentions tour-flow, a tour, a show date, a venue or a schedule they manage there.
---

# tour-flow

The tour-flow MCP tools work on the one workspace the user connected. Each
call runs with the user's own permissions, so results only include what they
can see, and changes only succeed where they may edit in tour-flow. If the
user allowed read access only, the write tools are not available.

## Finding things

- Never guess IDs. Get them from `list_tours`, `list_events` or `search`
  (`search` needs at least 2 characters).
- "This week", "next show", "what's on tomorrow": use `get_agenda` with a date
  range (at most 92 days). It returns each day with its schedule items and
  whether the user is on that day's crew.
- One show in detail: `get_event`, then `get_event_schedule` for the day
  schedule (arrival, load-in, soundcheck, doors, show, curfew).
- Everything on a tour: `get_tour`, then `list_events` with `tourId`, sorted
  by `date`.

## Times and timezones

Times are local to the event. Resolve the timezone as event → tour →
workspace (`get_workspace`). When the user is in another timezone, say which
zone a time is in instead of converting silently.

## Answering

- Day sheets: date, venue and city, then the schedule in time order with
  notes. Leave out empty fields.
- Keep German or other non-English venue notes as written.
- If a tool returns an error (missing permission, feature not on the plan,
  rate limit), tell the user plainly; don't retry in a loop.

## Making changes

Changes are visible to the whole crew right away, so be deliberate:

- Before a change, read the current record (`get_event` or
  `get_event_schedule`) and say what you are about to change.
- For `update_event`, pass `expectedUpdatedAt` from the `get_event` you just
  read. A 412 means someone edited it meanwhile: re-read, show the user the
  difference, and ask before retrying.
- Schedule edits go item by item (`add_schedule_item`, `update_schedule_item`,
  `delete_schedule_item`); the rest of the schedule stays as it is. Times are
  `HH:mm` in the event's timezone.
- Ask for explicit confirmation before `delete_schedule_item`.
- Deleting events or tours, crew, hotels, documents and other sections aren't
  available here; point the user to tour-flow for those.
