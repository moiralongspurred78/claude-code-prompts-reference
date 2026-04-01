# Scheduling Tools

Tools for creating, listing, and deleting cron-based scheduled tasks.

---

## ScheduleCronTool (`CronCreate`, `CronDelete`, `CronList`)

**Source:** `src/tools/ScheduleCronTool/prompt.ts`

### Feature Gate Logic

- `isKairosCronEnabled()` requires:
  - build flag `AGENT_TRIGGERS`
  - env override `CLAUDE_CODE_DISABLE_CRON` not set
  - GrowthBook kill switch `tengu_kairos_cron` (default true)
- `isDurableCronEnabled()` gates disk-persistent cron (`tengu_kairos_cron_durable`, default true)

### Tool Names

- `CRON_CREATE_TOOL_NAME = "CronCreate"`
- `CRON_DELETE_TOOL_NAME = "CronDelete"`
- `CRON_LIST_TOOL_NAME = "CronList"`

### CronCreate Description

Durable-enabled variant:

```
Schedule a prompt to run at a future time — either recurring on a cron schedule,
or once at a specific time. Pass durable: true to persist to .claude/scheduled_tasks.json;
otherwise session-only.
```

Session-only variant:

```
Schedule a prompt to run at a future time within this Claude session — either recurring
on a cron schedule, or once at a specific time.
```

### CronCreate Prompt (core content)

```
Schedule a prompt to be enqueued at a future time. Use for both recurring schedules and
one-shot reminders.

Uses standard 5-field cron in the user's local timezone:
minute hour day-of-month month day-of-week.

## One-shot tasks (recurring: false)
For "remind me at X" requests — fire once then auto-delete.

## Recurring jobs (recurring: true)
For every-N-minutes/hour/weekdays-at-time requests.

## Avoid the :00 and :30 minute marks when task allows it
When timing is approximate, pick off-minutes to avoid fleet-wide load spikes.
Only use 0/30 when user explicitly requires exact timing.

## Durability
- durable: false (default): session-only, no disk write
- durable: true: persist to `.claude/scheduled_tasks.json`, survives restarts

## Runtime behavior
- jobs fire only while REPL idle
- recurring tasks have deterministic jitter (up to 10% period, max 15 min)
- one-shot tasks landing on :00/:30 may fire up to 90s early
- recurring jobs auto-expire after ${DEFAULT_MAX_AGE_DAYS} days

Returns a job ID you can pass to CronDelete.
```

### CronDelete

Description:

```
Cancel a scheduled cron job by ID
```

Prompt (durable enabled):

```
Cancel a cron job previously scheduled with CronCreate.
Removes it from .claude/scheduled_tasks.json (durable jobs)
or the in-memory session store (session-only jobs).
```

Prompt (session-only):

```
Cancel a cron job previously scheduled with CronCreate.
Removes it from the in-memory session store.
```

### CronList

Description:

```
List scheduled cron jobs
```

Prompt (durable enabled):

```
List all cron jobs scheduled via CronCreate, both durable (.claude/scheduled_tasks.json)
and session-only.
```

Prompt (session-only):

```
List all cron jobs scheduled via CronCreate in this session.
```
