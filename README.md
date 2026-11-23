# tour-flow for Claude

Ask Claude about your tours, shows and day schedules in
[tour-flow](https://tour-flow.net), the tour and event management app for live
productions. For example: "What's on this week?", "When is load-in in
Zurich?", or "Write me a day sheet for Friday's show." With edit access,
Claude can also create events and update day schedules for you.

The plugin connects Claude to the tour-flow MCP server
(`https://tour-flow.net/api/mcp`) and adds a skill that teaches Claude how
tour-flow schedules, timezones and tours work. Claude sees and changes only
what your tour-flow account may see and change, and asks before it removes
anything.

Full guide: [tour-flow.net/docs/claude](https://tour-flow.net/docs/claude)

## Requirements

- A tour-flow account whose workspace plan includes **API Access** and
  **Claude & MCP Access**
- Claude Code

## Install

```bash
claude plugin marketplace add lauritzLbr/tour-flow-claude
claude plugin install tour-flow@tour-flow
```

Then start a new Claude Code session, type `/mcp`, choose the tour-flow server
and **Authenticate**. A tour-flow page opens in your browser:

1. Sign in with your own tour-flow account.
2. Pick the workspace Claude may use.
3. Choose whether Claude may make changes (turn it off for read-only access).
4. Click **Allow access**.

To disconnect, open tour-flow **Settings > API Keys** and remove Claude under
**Connected apps**. Access stops immediately.

### Without a browser (CI, scripts)

Connect the MCP server directly with a tour-flow API key instead:

```bash
claude mcp add --transport http tour-flow https://tour-flow.net/api/mcp --header "Authorization: Bearer <your API key>"
```

## Tools

| Tool                                         | What it does                                  | Access |
| -------------------------------------------- | --------------------------------------------- | ------ |
| `whoami`                                     | Your user, workspace and permissions          | read   |
| `get_workspace`                              | Workspace timezone, features and status names | read   |
| `list_tours` / `get_tour`                    | Tours and their details                       | read   |
| `list_events` / `get_event`                  | Events, optionally per tour or date range     | read   |
| `get_event_schedule`                         | The day schedule of one event                 | read   |
| `get_agenda`                                 | Everything in a date range, day by day        | read   |
| `search`                                     | Tours, events and projects by name            | read   |
| `create_event`                               | Creates an event                              | write  |
| `update_event`                               | Changes fields of an event                    | write  |
| `add_schedule_item` / `update_schedule_item` | Adds or changes one day schedule item         | write  |
| `delete_schedule_item`                       | Removes one day schedule item                 | delete |

Write tools are only available when you allowed changes, and only succeed
where your tour-flow role may edit. Tours and events can't be deleted through
Claude.

## Data and privacy

- The plugin contains no code that runs on your computer: a skill (text
  instructions for Claude) and the address of the tour-flow MCP server.
- Claude connects only to `tour-flow.net`. It acts as your tour-flow account in
  the one workspace you picked; changes appear in tour-flow's activity log
  under your name.
- Sign-in uses OAuth with PKCE. Claude never sees your password; access tokens
  are short-lived and stored by tour-flow only as hashes.
- What Claude reads from tour-flow becomes part of your Claude conversation.
  See the [tour-flow privacy policy](https://tour-flow.net/privacy) and
  [terms](https://tour-flow.net/terms).

## Support

[it@tour-flow.net](mailto:it@tour-flow.net) ·
[Documentation](https://tour-flow.net/docs/claude)
