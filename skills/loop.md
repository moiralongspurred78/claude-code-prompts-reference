# Skill: /loop

**Source:** `src/skills/bundled/loop.ts`

## Purpose

Schedules a recurring prompt/skill using cron and immediately executes it once.

## Registration Metadata

- name: `loop`
- userInvocable: `true`
- argumentHint: `[interval] <prompt>`
- isEnabled: `isKairosCronEnabled()`
- default interval: `10m`

## Usage Message

```
Usage: /loop [interval] <prompt>

Intervals: Ns, Nm, Nh, Nd
Minimum granularity is 1 minute.
If omitted, interval defaults to 10m.
```

## Prompt (`buildPrompt(args)`) Logic

### Parse order

1. leading token `^\d+[smhd]$`
2. trailing `every <N><unit>` clause
3. fallback default `10m`

If parsed prompt is empty -> show usage, do not schedule.

### Interval to cron mapping

- `Nm` (<=59): `*/N * * * *`
- `Nm` (>=60): `0 */H * * *`
- `Nh` (<=23): `0 */N * * *`
- `Nd`: `0 0 */N * *`
- `Ns`: round up to minutes (`ceil(N/60)m`)

If interval doesn't divide cleanly, round to nearest clean interval and tell user.

### Action steps

1. call `CronCreate` with:
   - `cron`
   - `prompt` (verbatim)
   - `recurring: true`
2. confirm schedule, cron, human cadence, auto-expiry (`DEFAULT_MAX_AGE_DAYS`), and cancel hint with `CronDelete` + job ID
3. immediately execute parsed prompt now (skill invocation or direct action)
