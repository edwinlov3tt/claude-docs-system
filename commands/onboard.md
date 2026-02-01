---
description: Onboard a new Claude instance - quickly get up to speed on the project
allowed-tools: ["Read", "Write", "Bash", "Grep"]
---

# Onboard New Claude Instance

Get a new Claude Code instance fully up to speed on this project as quickly as possible. This command reads all documentation and provides a comprehensive briefing.

## Phase 1: Project Discovery

### 1.1 Find Core Documentation
Read in order of priority:

```bash
# List available documentation
echo "=== Available Documentation ==="
ls -la CLAUDE.md README.md PRD*.md SPEC*.md 2>/dev/null
ls -la .claude/docs/*.md 2>/dev/null
ls -la docs/*.md 2>/dev/null
```

### 1.2 Read Essential Files

**Priority 1 - Must Read:**
1. `CLAUDE.md` - Project-specific instructions and context
2. `README.md` - Project overview
3. `.claude/docs/ARCHITECTURE.md` - Technical architecture
4. `PRD.md` or similar - Product requirements (if exists)

**Priority 2 - Should Read:**
5. `.claude/docs/KNOWN_ISSUES.md` - Current bugs and blockers
6. `.claude/docs/DECISIONS.md` - Why things were built this way
7. `.claude/docs/ASSESSMENT.md` - Production readiness (if exists)
8. `.claude/docs/LOCAL_DEV.md` - How to run locally (if exists)

**Priority 3 - Reference:**
9. `.claude/tasks/README.md` - Current task board
10. `.claude/docs/CHANGELOG.md` - Recent changes
11. `.claude/docs/services/*.md` - External integrations
12. `.claude/docs/components/*.md` - Internal components

## Phase 2: Codebase Overview

### 2.1 Project Structure
```bash
# Get high-level structure
find . -type d -maxdepth 2 | grep -v node_modules | grep -v .git | grep -v __pycache__ | sort

# Key config files
ls -la package.json pyproject.toml Cargo.toml wrangler.toml vercel.json tsconfig.json 2>/dev/null
```

### 2.2 Tech Stack Summary
```bash
# Package.json dependencies
cat package.json 2>/dev/null | grep -A50 '"dependencies"' | head -30

# Python requirements
cat requirements.txt pyproject.toml 2>/dev/null | head -30
```

### 2.3 Entry Points
```bash
# Find main entry files
ls -la src/index.* src/main.* src/app.* app/page.* pages/index.* main.* index.* 2>/dev/null
```

## Phase 3: Current State Assessment

### 3.1 Recent Activity
```bash
# Recent commits
git log --oneline -10 2>/dev/null

# Current branch
git branch --show-current 2>/dev/null

# Uncommitted changes
git status --short 2>/dev/null
```

### 3.2 Task Status
Read `.claude/tasks/README.md` for:
- Tasks in progress
- Blocked tasks
- Ready to start tasks

### 3.3 Known Issues
Read `.claude/docs/KNOWN_ISSUES.md` for:
- CRITICAL issues (blocking)
- HIGH issues (important)
- Active bugs being worked on

## Phase 4: Generate Briefing

Create a comprehensive briefing for the new Claude instance:

```markdown
# Project Briefing

**Generated**: [DATE]
**For**: New Claude Code Instance

---

## 🎯 Project Overview

**Name**: [from package.json or README]
**Purpose**: [1-2 sentence description from README/PRD]
**Type**: [Web app / API / CLI / Extension / etc.]

---

## 🛠 Tech Stack

| Layer | Technology |
|-------|------------|
| Frontend | [X] |
| Backend | [X] |
| Database | [X] |
| Hosting | [X] |
| Key Libraries | [X, Y, Z] |

---

## 📁 Project Structure

```
[Key directories and their purposes]
```

---

## 🚦 Current Status

### Development Phase
[Where we are: Early development / Feature complete / Production ready / etc.]

### Recent Work
[Summary of last 5-10 commits]

### Active Tasks
| Task | Status | Priority |
|------|--------|----------|
| [task] | 🔄 In Progress | P0 |
| [task] | ⬜ Ready | P1 |

### Blocked By
[Any blocking issues or dependencies]

---

## ⚠️ Critical Information

### Known Issues (Priority)
1. **[CRITICAL]** [Issue] - [brief description]
2. **[HIGH]** [Issue] - [brief description]

### Key Decisions
[Important architectural decisions that affect current work]

### Gotchas & Warnings
[Things to watch out for, common mistakes]

---

## 🔧 Development Setup

### Quick Start
```bash
[Commands to get running locally]
```

### Environment Variables
[Required env vars - see .env.example]

### Running Tests
```bash
[Test command]
```

---

## 📋 Conventions & Standards

### Code Style
[Key patterns to follow from CLAUDE.md]

### File Organization
[Where to put new files]

### Naming Conventions
[Variable, function, file naming patterns]

---

## 🎯 Immediate Focus Areas

Based on current state, prioritize:

1. **[Highest priority item]**
   - Why: [reason]
   - Files: [relevant files]

2. **[Second priority]**
   - Why: [reason]
   - Files: [relevant files]

3. **[Third priority]**
   - Why: [reason]
   - Files: [relevant files]

---

## 📚 Key Documentation

| Document | Purpose | Read When |
|----------|---------|-----------|
| `CLAUDE.md` | Project rules | Always |
| `.claude/docs/ARCHITECTURE.md` | System design | Understanding structure |
| `.claude/docs/KNOWN_ISSUES.md` | Current bugs | Before fixing bugs |
| `.claude/docs/DECISIONS.md` | Past decisions | Before making changes |
| `.claude/tasks/README.md` | Task board | Planning work |

---

## 🔗 Quick Commands

| Command | Purpose |
|---------|---------|
| `/tasks` | View task board |
| `/assess` | Check production readiness |
| `/sync` | Log work at end of session |
| `/secrets` | Check for exposed secrets |

---

## ❓ If You Need More Context

- **Architecture questions**: Read `.claude/docs/ARCHITECTURE.md`
- **Why was X built this way**: Read `.claude/docs/DECISIONS.md`
- **What's broken**: Read `.claude/docs/KNOWN_ISSUES.md`
- **Service integrations**: Read `.claude/docs/services/[service].md`
- **Production status**: Run `/assess`

---

*This briefing was auto-generated. For the most current information, check the source documentation.*
```

## Phase 5: Output Briefing

Present the briefing directly in the conversation so the new Claude instance has immediate context.

After presenting:

```markdown
---

## ✅ Onboarding Complete

I've reviewed:
- [X] CLAUDE.md
- [X] README.md  
- [X] Architecture documentation
- [X] Known issues
- [X] Current tasks
- [X] Recent git history

### Ready to Help With

Based on current project state, I can:
1. [Most relevant task or area]
2. [Second most relevant]
3. [Third most relevant]

### Questions I'd Suggest Clarifying
[Any ambiguities or missing context that would help]

---

What would you like to work on?
```
