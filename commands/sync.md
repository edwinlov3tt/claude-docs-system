---
description: Auto-extract and log all issues, decisions, services, and components from this conversation
allowed-tools: ["Read", "Write", "Edit", "Bash", "Grep"]
---

# Sync Documentation from Conversation

Review the entire conversation context and automatically extract and log:
- Issues discovered or discussed
- Technical decisions made
- Services integrated or modified
- Components created or changed

This is the "I don't want to think about docs" command - just run it and everything gets logged.

## Phase 1: Context Analysis

Review the full conversation and identify:

### 1.1 Issues & Bugs
Look for:
- Bugs discovered during development
- Edge cases that weren't handled
- Error messages or failures encountered
- TODOs mentioned but not resolved
- Performance problems noticed
- Workarounds implemented (indicates underlying issue)
- "We should fix this later" type comments
- Things that "don't work yet" or are "broken"

### 1.2 Technical Decisions
Look for:
- "Let's use X instead of Y"
- "I chose X because..."
- Framework/library selections
- Architecture patterns chosen
- Database schema decisions
- API design choices
- Deployment strategy decisions
- Tradeoffs discussed and resolved
- "The reason we're doing it this way..."

### 1.3 Service Integrations
Look for:
- New external APIs integrated
- SDK installations (npm install, pip install)
- Environment variables added for services
- API keys or credentials discussed
- Webhooks configured
- Third-party services connected (Stripe, Supabase, OpenAI, Cloudflare, etc.)

### 1.4 Component Changes
Look for:
- New files or modules created
- Significant refactors
- New API routes added
- New React components or pages
- Database schema changes
- New utilities or helpers

## Phase 2: Auto-Logging

For each item identified:

### Log Issues → KNOWN_ISSUES.md
```markdown
### [SEVERITY] [Title extracted from context]
- **Location**: `[file if mentioned]`
- **Symptom**: [What was described as broken/wrong]
- **Root Cause**: [If discussed, otherwise "Investigation needed"]
- **Workaround**: [If one was used, otherwise "None"]
- **Proper Fix**: [If discussed, otherwise "TBD"]
- **Added**: [today's date]
- **Context**: Discovered during [brief description of what we were working on]
```

### Log Decisions → DECISIONS.md
```markdown
## [Decision Title]
- **Date**: [today]
- **Status**: Accepted

### Context
[What problem we were solving when this came up]

### Decision
[What was chosen based on conversation]

### Alternatives Considered
[If discussed in conversation, otherwise note "Alternatives not explicitly discussed"]

### Consequences
[Based on what was discussed]
```

### Log Services → services/[name].md
Create or update service doc with:
- What service was integrated
- What it's being used for
- Any env vars mentioned
- Files where it's used
- Any issues or gotchas discovered

### Log Components → components/[name].md
Create or update component doc with:
- What was built/changed
- Purpose
- Key files involved
- Dependencies added

## Phase 3: Update Changelog

Add entry to CHANGELOG.md summarizing what was accomplished in this session.

## Phase 4: Summary Report

```markdown
# Documentation Synced

**Session**: [Brief description of what this conversation was about]
**Date**: [today]

## Auto-Extracted & Logged

### Issues (X found)
| Severity | Title | Location |
|----------|-------|----------|
| [SEV] | [Title] | `[file]` |

### Decisions (X found)
| Decision | Summary |
|----------|---------|
| [Title] | [One-liner] |

### Services (X found)
| Service | Action |
|---------|--------|
| [Name] | [Created/Updated] |

### Components (X found)
| Component | Action |
|-----------|--------|
| [Name] | [Created/Updated] |

### Changelog
- Added entry for [date] summarizing session work

## Files Modified
- `.claude/docs/KNOWN_ISSUES.md` - [X issues added]
- `.claude/docs/DECISIONS.md` - [X decisions added]
- `.claude/docs/CHANGELOG.md` - [session entry added]
- `.claude/docs/services/[x].md` - [created/updated]
- `.claude/docs/components/[x].md` - [created/updated]

---

*Run `/doc-status` to verify documentation health*
```

## Execution Notes

- Be thorough - review the ENTIRE conversation, not just recent messages
- Don't duplicate: check existing docs before adding
- If something was already logged, skip it
- Infer severity for issues based on context (blocking = HIGH, workaround exists = MEDIUM, minor = LOW)
- When uncertain about details, note "Details unclear from conversation" rather than guessing
- Group related items (e.g., multiple issues in the same component)
