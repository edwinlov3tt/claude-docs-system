# Documentation Protocol

This project uses an automated documentation system. Claude auto-extracts and logs issues, decisions, services, and changes from conversations.

---

## Quick Reference

### Primary Commands

| Command | When to Use |
|---------|-------------|
| `/sync` | **End of every session** - auto-extracts everything from conversation |
| `/onboard` | **Start of new instance** - get up to speed on project |
| `/assess` | Check production readiness against PRD/specs |
| `/tasks` | View task board, find parallel-safe work |
| `/task [name]` | Create detailed implementation task |
| `/task-complete [name]` | Archive completed task |
| `/localdev` | Check/setup local development environment |
| `/secrets` | Scan for API keys before git push |
| `/handoff` | Generate handoff doc before sharing |

### Utility Commands

| Command | When to Use |
|---------|-------------|
| `/log` | Quick capture something specific mid-session |
| `/scan` | Find undocumented services/components |
| `/audit` | Full project analysis (first setup, periodic refresh) |
| `/doc-status` | Check documentation health |

### Manual Logging (optional - /sync handles these automatically)

| Command | Usage |
|---------|-------|
| `/issue` | `/issue description` - explicit issue logging |
| `/decision` | `/decision what was decided` - explicit decision logging |
| `/service` | `/service ServiceName` - explicit service docs |

---

## Typical Workflow

### Daily Development
```
1. Code normally with Claude
2. Discuss bugs, make decisions, integrate services
3. At end of session: /sync
4. Done - Claude extracts and logs everything
```

### Planning & Tasks
```
1. /assess                     # See production readiness
2. /tasks                      # View task board
3. /task [feature name]        # Create detailed task
4. Work through task steps...
5. /task-complete [task-name]  # Archive when done
```

### Multi-Instance Work
```
1. /tasks                      # Check parallel groups
2. Pick tasks from DIFFERENT parallel groups
3. Never work on tasks that modify same files
```

**That's it.** No manual documentation needed.

---

## What Gets Auto-Logged

When you run `/sync`, Claude reviews the conversation and extracts:

| If you discussed... | It goes to... |
|---------------------|---------------|
| Bugs, errors, "this doesn't work" | KNOWN_ISSUES.md |
| "Let's use X", "I chose Y because" | DECISIONS.md |
| Stripe, Supabase, API integrations | services/*.md |
| New routes, components, modules | components/*.md |
| What was built/fixed | CHANGELOG.md |

---

## Documentation Locations

| Document | Contains |
|----------|----------|
| `.claude/docs/CHANGELOG.md` | What changed and when |
| `.claude/docs/KNOWN_ISSUES.md` | Bugs, edge cases, tech debt |
| `.claude/docs/DECISIONS.md` | Why things were built this way |
| `.claude/docs/ARCHITECTURE.md` | System overview, env vars |
| `.claude/docs/ASSESSMENT.md` | Production readiness (from /assess) |
| `.claude/docs/LOCAL_DEV.md` | Local dev setup (from /localdev) |
| `.claude/docs/services/*.md` | External service integrations |
| `.claude/docs/components/*.md` | Internal component docs |
| `.claude/tasks/README.md` | Task board overview |
| `.claude/tasks/*.md` | Individual task files |
| `.claude/tasks/archive/` | Completed tasks |

---

## Issue Severity Guide

| Level | Description |
|-------|-------------|
| CRITICAL | System unusable, data loss risk |
| HIGH | Major feature broken, no workaround |
| MEDIUM | Feature impaired, workaround exists |
| LOW | Minor inconvenience |

---

## When to Use Each Command

**`/sync`** (most common)
- End of any coding session
- After a long conversation with multiple changes
- When you want Claude to figure out what to document

**`/log`** (quick capture)
- Mid-session when something notable comes up
- When you want to capture one specific thing
- Faster than /sync for small captures

**`/audit`** (comprehensive)
- First time setting up docs
- After major refactors
- Monthly refresh
- Before onboarding someone new

**`/onboard`** (new instance)
- First thing when starting a new Claude Code session
- When context was lost or instance is fresh
- Gets you up to speed on project state

**`/secrets`** (before push)
- Before any git push to remote
- After adding new integrations
- Catches hardcoded API keys and secrets

**`/scan`** (catch-up)
- When you suspect undocumented services/components
- After adding integrations without documenting
- Returns selectable list to document

**`/handoff`** (sharing)
- Before giving project to another developer
- When someone needs to understand the project quickly
