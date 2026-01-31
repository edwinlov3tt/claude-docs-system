# Claude Docs System

Automated documentation system for Claude Code projects. Drop into any project and let Claude Code maintain your docs automatically.

**Philosophy**: You code, Claude documents. No manual logging - just run `/sync` and Claude extracts issues, decisions, services, and changes from your conversation automatically.

## Quick Start

### For Claude Code Users

**Install into your current project:**

```
Clone https://github.com/YOUR_USERNAME/claude-docs-system into this project's .claude folder, merging the commands and creating the docs structure. Then run a full audit to populate the documentation based on my actual codebase.
```

Or be more specific:

```
1. Fetch https://github.com/YOUR_USERNAME/claude-docs-system
2. Copy the contents of `commands/` into my `.claude/commands/` folder
3. Copy the contents of `docs-templates/` into `.claude/docs/`, but don't overwrite existing files
4. Merge the CLAUDE-TEMPLATE.md documentation protocol into my existing CLAUDE.md
5. Run /audit to analyze my project and populate all documentation
```

---

## What's Included

### Slash Commands

#### Primary Commands (use these)

| Command | Purpose | When to Use |
|---------|---------|-------------|
| `/audit` | Full project analysis + doc generation | First time setup, periodic refresh |
| `/sync` | Auto-extract issues/decisions/services from conversation | End of coding sessions |
| `/assess` | Production readiness assessment against PRD/specs | Check how far from launch |
| `/tasks` | View task board, dependencies, parallel opportunities | Plan what to work on |
| `/task` | Create detailed implementation task | Break down a feature |
| `/handoff` | Generate developer handoff doc | Before sharing project |

#### Development Commands

| Command | Purpose | When to Use |
|---------|---------|-------------|
| `/localdev` | Check local dev setup, generate run instructions | Setting up or debugging local env |
| `/task-complete` | Archive completed task, unblock dependents | When finishing a task |
| `/log` | Quick capture from recent messages | Grab something specific fast |
| `/doc-status` | Check documentation health | Periodic checkup |

#### Utility Commands (manual alternatives)

| Command | Purpose | Usage |
|---------|---------|-------|
| `/doc` | Manual changelog update | When /sync is overkill |
| `/doc-init` | Create docs folder structure | Manual setup only |
| `/issue` | Log a specific bug | `/issue API timeout` |
| `/decision` | Record a specific decision | `/decision Using X over Y` |
| `/service` | Document a specific service | `/service Stripe` |

### Documentation Structure

```
.claude/
├── commands/           # Slash commands
│   ├── audit.md        # Full project audit
│   ├── sync.md         # Auto-extract from conversation
│   ├── assess.md       # Production readiness check
│   ├── tasks.md        # View task board
│   ├── task.md         # Create implementation task
│   ├── task-complete.md# Archive completed task
│   ├── localdev.md     # Local dev readiness
│   ├── log.md          # Quick capture
│   ├── handoff.md      # Generate handoff doc
│   ├── doc.md          # Manual updates
│   ├── doc-init.md     # Initialize structure
│   ├── doc-status.md   # Check doc health
│   ├── decision.md     # Manual decision logging
│   ├── issue.md        # Manual issue logging
│   └── service.md      # Manual service docs
│
├── docs/               # Documentation
│   ├── ARCHITECTURE.md # System overview
│   ├── CHANGELOG.md    # What changed when
│   ├── DECISIONS.md    # Why things were built this way
│   ├── KNOWN_ISSUES.md # Bugs, edge cases, tech debt
│   ├── ASSESSMENT.md   # Production readiness (generated)
│   ├── LOCAL_DEV.md    # Local dev setup guide (generated)
│   ├── components/     # Internal component docs
│   └── services/       # External service docs
│
└── tasks/              # Implementation tasks
    ├── README.md       # Task board overview
    ├── [task-name].md  # Individual task files
    └── archive/        # Completed tasks
```

---

## Installation Methods

### Method 1: Claude Code (Recommended)

Just tell Claude Code:

```
Install the documentation system from https://github.com/YOUR_USERNAME/claude-docs-system into this project
```

Claude will:
1. Clone or fetch the repo
2. Copy commands to `.claude/commands/`
3. Create `.claude/docs/` structure
4. Merge documentation protocol into your `CLAUDE.md`
5. Run initial audit

### Method 2: Manual Installation

```bash
# Navigate to your project
cd your-project

# Clone into a temp directory
git clone https://github.com/YOUR_USERNAME/claude-docs-system /tmp/claude-docs

# Copy commands
mkdir -p .claude/commands
cp /tmp/claude-docs/commands/*.md .claude/commands/

# Copy doc templates
mkdir -p .claude/docs/services .claude/docs/components
cp /tmp/claude-docs/docs-templates/*.md .claude/docs/

# Append to CLAUDE.md (or create it)
cat /tmp/claude-docs/CLAUDE-TEMPLATE.md >> CLAUDE.md

# Cleanup
rm -rf /tmp/claude-docs
```

Then in Claude Code: `/audit`

---

## Workflows

### New Project Setup

1. Create your project as normal
2. Tell Claude Code:
   ```
   Install the documentation system from https://github.com/YOUR_USERNAME/claude-docs-system and run a full audit
   ```
3. Review generated documentation
4. Commit the `.claude/` folder

### Existing Project Setup

1. Tell Claude Code:
   ```
   Install the documentation system from https://github.com/YOUR_USERNAME/claude-docs-system into this project. 
   Merge with any existing .claude files. Then run /audit to analyze the codebase and populate all documentation.
   ```
2. Review and adjust generated docs
3. Commit changes

### Daily Development Workflow

**The simple version:**
```
# Code normally with Claude
# ...
# When done, just run:
/sync
```

That's it. Claude reviews your conversation and auto-logs:
- Any bugs or issues you discussed
- Technical decisions you made
- Services you integrated
- Components you created/modified
- Changelog entries

**If you want to capture something specific quickly:**
```
/log
```

### Production Planning Workflow

```
# Check how far from production
/assess

# This analyzes against your PRD/specs and generates:
# - Gap analysis (what's done vs what's needed)
# - Prioritized task list
# - Blocking issues
# - Estimated time to production
```

### Task-Based Development Workflow

For larger features, use the task system:

```
# View current task board
/tasks

# Create a detailed task for a feature
/task implement user authentication

# Work on the task following its steps...

# When complete, archive it
/task-complete user-auth
```

**Multi-instance development:**
- Check `/tasks` for "Parallel Execution Groups"
- Multiple Claude instances can work on tasks in different groups simultaneously
- Never parallelize tasks that modify the same files

### Local Development Setup

```
# Check if local dev environment is ready
/localdev

# This generates:
# - Setup instructions in .claude/docs/LOCAL_DEV.md
# - Updates CLAUDE.md with dev server commands
# - Lists any blocking issues (missing env vars, services not running)
```

### Before Handoff

```
/doc-status    # Check documentation health
/assess        # Check production readiness
/handoff       # Generate comprehensive handoff document
```

---

## Command Details

### /sync - Auto-Extract Everything (PRIMARY COMMAND)

This is the main command. Run it at the end of a coding session and Claude will:

1. **Review the entire conversation**
2. **Auto-detect and extract:**
   - Bugs discovered → KNOWN_ISSUES.md
   - Decisions made → DECISIONS.md
   - Services integrated → services/*.md
   - Components changed → components/*.md
3. **Update CHANGELOG.md** with session summary
4. **Report what was logged**

**What it looks for:**
- "This is broken" / "doesn't work" → Issue
- "Let's use X instead of Y" → Decision
- `npm install @stripe/stripe-js` → Service integration
- "Created new API route for..." → Component

### /assess - Production Readiness

Analyzes your project against PRD/specs and generates:
- **Gap analysis**: What's complete vs what's missing
- **Prioritized task list**: P0 (blocking), P1, P2
- **Blocking issues**: What must be fixed before launch
- **Time estimate**: Rough estimate to production

Creates `.claude/docs/ASSESSMENT.md` and task stubs for P0 items.

### /tasks - Task Board

View all tasks, their status, dependencies, and parallel execution opportunities:
- **Ready tasks**: No blockers, can start now
- **Parallel groups**: Tasks safe to run in multiple Claude instances
- **Blocked tasks**: What's waiting on what
- **Dependency tree**: Visual task relationships

### /task - Create Implementation Task

Creates a detailed task file with:
- Pre-implementation checklist (what to review first)
- Step-by-step implementation guide
- Code standards to follow (for consistency across Claude instances)
- Files to modify
- Verification checklist
- Dependency mapping

```
/task implement user authentication
```

### /task-complete - Archive Completed Task

When you finish a task:
- Runs verification (build, typecheck, tests)
- Archives task to `.claude/tasks/archive/`
- Unblocks dependent tasks
- Updates changelog

```
/task-complete user-auth
```

### /localdev - Local Dev Readiness

Checks your local development environment:
- Runtime versions (Node, Python, etc.)
- Dependencies installed
- Environment variables
- Database/services running
- CORS configuration
- Port availability

Generates `.claude/docs/LOCAL_DEV.md` with setup instructions.

### /log - Quick Capture

Lighter version of /sync. Reviews just recent messages and grabs anything notable.

```
/log                    # Auto-detect from recent context
/log the auth bug       # Focus on something specific
```

### /audit - Full Project Audit

Comprehensive analysis for first-time setup or periodic refresh. Scans actual code, git history, and populates all documentation.

### /handoff - Developer Handoff

Generates comprehensive `HANDOFF.md` with everything a new developer needs.

---

## Customization

### Adding Project-Specific Commands

Create new `.md` files in `.claude/commands/`:

```markdown
---
description: Your command description
allowed-tools: ["Read", "Write", "Edit", "Bash"]
---

# Command Name

Instructions for Claude Code to follow...
```

### Modifying Templates

Edit files in `.claude/docs/` to match your project's needs.

---

## Best Practices

1. **Run `/sync` at the end of sessions** - Let Claude do the documentation work
2. **Run `/assess` when planning** - Know where you stand before diving in
3. **Use `/task` for complex features** - Break down work into parallel-safe chunks
4. **Check `/tasks` before starting** - See what's ready and what can run in parallel
5. **Run `/audit` periodically** - Monthly or after major changes
6. **Run `/localdev` when onboarding** - Ensure smooth local setup
7. **Run `/handoff` before sharing** - Generates everything a new dev needs

### Multi-Instance Development

When running multiple Claude Code instances:

```
/tasks  # Check parallel execution groups first
```

- ✅ Tasks in different parallel groups are safe
- ✅ Tasks modifying different directories are safe
- ❌ Never parallelize tasks modifying same files
- ❌ Never parallelize database migrations
- ❌ Never parallelize package.json changes

---

## File Structure Reference

After installation:

```
your-project/
├── .claude/
│   ├── commands/
│   │   ├── audit.md           # Full project analysis
│   │   ├── sync.md            # Auto-extract (primary)
│   │   ├── assess.md          # Production readiness
│   │   ├── tasks.md           # Task board view
│   │   ├── task.md            # Create task
│   │   ├── task-complete.md   # Archive task
│   │   ├── localdev.md        # Local dev check
│   │   ├── log.md             # Quick capture
│   │   ├── handoff.md         # Generate handoff
│   │   ├── doc.md             # Manual updates
│   │   ├── doc-init.md        # Initialize structure
│   │   ├── doc-status.md      # Check health
│   │   ├── decision.md        # Manual decision
│   │   ├── issue.md           # Manual issue
│   │   └── service.md         # Manual service doc
│   ├── docs/
│   │   ├── ARCHITECTURE.md
│   │   ├── CHANGELOG.md
│   │   ├── DECISIONS.md
│   │   ├── KNOWN_ISSUES.md
│   │   ├── ASSESSMENT.md      # Generated by /assess
│   │   ├── LOCAL_DEV.md       # Generated by /localdev
│   │   ├── components/
│   │   └── services/
│   └── tasks/
│       ├── README.md          # Task board
│       ├── [task-name].md     # Active tasks
│       └── archive/           # Completed tasks
├── CLAUDE.md (with documentation protocol)
├── HANDOFF.md (generated by /handoff)
└── [your project files]
```

---

## License

MIT - Use freely in your projects.
