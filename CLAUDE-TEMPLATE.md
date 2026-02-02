# Documentation Protocol

This project uses claude-docs-system for automated documentation and project management.

---

## Commands

| Command | When | What |
|---------|------|------|
| `/onboard` | New session | Read docs, get project briefing |
| `/dev` | Ready to code | Check env, start servers |
| `/sync` | End of session | Auto-extract issues/decisions/services |
| `/push` | Ready to ship | Scan secrets → commit → push |
| `/feature` | Have an idea | Log with phase/complexity assessment |
| `/task` | Ready to build | Create, view, or complete tasks |
| `/audit` | Status check | Deep analysis, production readiness |
| `/handoff` | Sharing project | Generate developer handoff doc |

---

## Typical Flow

```
/onboard           # Start: get briefed
/dev               # Start servers
# ... code ...
/feature [idea]    # Log ideas as they come
# ... code ...
/sync              # End: log everything
/push              # Ship: scan + commit + push
```

---

## What `/sync` Extracts

| Discussion | → Document |
|------------|-----------|
| Bugs, errors, "doesn't work" | KNOWN_ISSUES.md |
| "Let's use X because Y" | DECISIONS.md |
| npm install, API integrations | services/*.md |
| New routes, components | components/*.md |
| Session summary | CHANGELOG.md |

---

## Documentation

| Document | Purpose |
|----------|---------|
| `.claude/docs/ARCHITECTURE.md` | System overview, tech stack |
| `.claude/docs/CHANGELOG.md` | What changed when |
| `.claude/docs/DECISIONS.md` | Why things were built this way |
| `.claude/docs/KNOWN_ISSUES.md` | Bugs, tech debt, blockers |
| `.claude/docs/ASSESSMENT.md` | Production readiness (from /audit) |
| `.claude/docs/LOCAL_DEV.md` | Local dev setup (from /dev) |
| `.claude/docs/services/*.md` | External service integrations |
| `.claude/docs/components/*.md` | Internal component docs |
| `.claude/features/README.md` | Feature backlog by phase |
| `.claude/tasks/README.md` | Task board |

---

## Issue Severity

| Level | Description |
|-------|-------------|
| CRITICAL | System unusable, data loss, security |
| HIGH | Major feature broken, no workaround |
| MEDIUM | Feature impaired, workaround exists |
| LOW | Minor inconvenience |

---

## Feature Phases

| Phase | Criteria |
|-------|----------|
| MVP | Can't ship without it |
| v1.0 | Important for first release |
| v1.1 | Ship shortly after launch |
| v2.0 | Future vision |

---

## Multi-Instance Rules

When running parallel Claude instances:
- ✅ Tasks in different parallel groups (different files)
- ✅ Independent documentation work
- ❌ Never parallelize tasks that modify the same files
- ❌ Never parallelize database migrations or package.json changes
- Check `/task` for parallel execution groups before starting
