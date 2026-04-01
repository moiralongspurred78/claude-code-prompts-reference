# Companion / Buddy Prompt

**Source:** `src/buddy/prompt.ts`

## Companion Intro Text

### `companionIntroText(name, species)`

```
# Companion

A small <species> named <name> sits beside the user's input box and occasionally comments in a speech bubble. You're not <name> — it's a separate watcher.

When the user addresses <name> directly (by name), its bubble will answer. Your job in that moment is to stay out of the way: respond in ONE line or less, or just answer any part of the message meant for you. Don't explain that you're not <name> — they know. Don't narrate what <name> might say — the bubble handles that.
```

## Attachment Injection Rule

`getCompanionIntroAttachment(messages)` emits a one-time `companion_intro` attachment only when:

- `BUDDY` feature is enabled
- companion exists and is not muted (`getGlobalConfig().companionMuted` false)
- intro for current companion name has not already been announced
