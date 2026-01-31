---
description: Quick-log anything notable from the last few messages (issues, decisions, services)
allowed-tools: ["Read", "Write", "Edit"]
---

# Quick Log

Fast extraction from recent conversation context. Use this when you want to capture something specific without a full sync.

**Optional focus:** $ARGUMENTS

## What to Extract

Review the last few exchanges and identify anything worth logging:

### If an issue/bug was discussed:
→ Add to KNOWN_ISSUES.md with severity, location (if known), and description

### If a technical decision was made:
→ Add to DECISIONS.md with context and reasoning

### If a service was integrated or modified:
→ Create/update the service doc in services/

### If a component was created or significantly changed:
→ Create/update the component doc in components/

## Quick Formats

### Issue (abbreviated)
```markdown
### [SEVERITY] [Title]
- **Location**: `[file]` (or "Various" / "TBD")
- **Symptom**: [Brief description]
- **Workaround**: [If any]
- **Proper Fix**: [Brief]
- **Added**: [date]
```

### Decision (abbreviated)
```markdown
## [Title]
- **Date**: [date]
- **Status**: Accepted

**Context**: [1-2 sentences]
**Decision**: [What was chosen and why]
**Tradeoff**: [Key tradeoff if any]
```

## Output

```markdown
## Logged

| Type | Title | File |
|------|-------|------|
| [Issue/Decision/Service/Component] | [Title] | [doc file] |

[1-2 sentence summary of what was captured]
```

## Notes

- If $ARGUMENTS is provided, focus on that specific thing
- If nothing notable in recent messages, say so
- Don't over-document trivial things
- Use `/sync` for comprehensive extraction
