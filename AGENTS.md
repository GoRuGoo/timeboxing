# Timeboxing workspace rules

This directory is the source of truth for daily timeboxing.

## Defaults

- Timezone: Asia/Tokyo
- Planning window: 09:00-18:00
- Lunch: 12:00-13:00
- Minimum scheduling unit: 30 minutes
- Unallocated buffer: at least 30 minutes per day
- Calendar event prefix: `[TB]`
- Default reminder for newly created timeboxes: popup 5 minutes before start

The user may override these defaults for a day. Record one-day overrides in that day's file; do not silently turn them into permanent defaults.

## Files

- `Inbox.md`: unchecked tasks are pending; checked tasks are complete.
- `Daily/YYYY-MM-DD.md`: proposed schedule, approved schedule, Calendar sync result, and optional work log.

Create a Daily file only when a plan is proposed or Calendar sync is recorded. Use this structure:

```markdown
# YYYY-MM-DD Timeboxing

## Existing commitments

## Proposal

## Approved schedule

## Calendar sync

## Work log
```

## Planning

- Read Calendar before calculating free time. Merge overlapping busy intervals.
- Do not assume an all-day entry consumes all working hours; surface it and ask when its effect is unclear.
- Put tasks involving other people early when practical.
- Favor uninterrupted morning time for design, planning, writing, or other deep work.
- Batch small administrative work and keep context switches low.
- Add realistic travel, break, transition, and interruption buffers.
- If estimates exceed capacity, report the overflow explicitly and ask what to defer. Do not shorten estimates merely to fit.

## Calendar safety gate

- Read-only Calendar operations may run without confirmation.
- Before every create, update, or delete, show the exact action list including calendar, title, date, start, end, and timezone, then obtain explicit user approval.
- Include the reminder setting in the approval list. If the user did not specify one, create each new timebox with a popup reminder 5 minutes before it starts. A user-specified reminder time or explicit `no reminder` request takes precedence.
- Apply the 5-minute default with Calendar reminders set to `use_default: false` and a single `popup` override at 5 minutes; do not depend on the target calendar's own default reminder.
- Do not change reminder settings on existing Calendar events unless the user explicitly requested and approved that exact change.
- A request such as 「今日のタイムボックスを作って」 authorizes reading and proposing only; it is never approval to write.
- Re-read the affected period immediately before writing. Do not create a duplicate when normalized title, start, and end already match.
- Never move or delete an existing event unless the user explicitly requested and approved that exact action.
- Only update timeboxes whose identity and ownership are unambiguous. If uncertain, create no change and ask.
- Never create a test event without prior approval.
- After writing, verify by reading Calendar and record success or failure in the Daily file.

Scheduling a task does not complete it. Do not check off, remove, or move an Inbox task merely because a Calendar event was created.

## Secrets and privacy

- Never store OAuth tokens, cookies, API keys, client secrets, authorization codes, or account passwords in this directory or the repository.
- Use the Codex-managed Google Calendar connection for authentication.
- Do not copy unnecessary attendee or private-event details into Daily files. Generalize existing commitments when detail is not needed for planning.
