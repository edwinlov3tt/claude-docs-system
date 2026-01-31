---
description: Generate comprehensive developer handoff document
allowed-tools: ["Read", "Write", "Bash"]
---

# Generate Handoff Document

Create a comprehensive HANDOFF.md for onboarding another developer.

## Gather Information

1. Read all `.claude/docs/` files
2. Read `package.json`, `CLAUDE.md`, `README.md`
3. Get git info: `git remote -v`, `git log --oneline -10`

## Generate HANDOFF.md

```markdown
# Project Handoff

**Generated**: [DATE]
**Project**: [NAME]

## Quick Start

### Prerequisites
- [Required tools and versions]
- [Accounts/access needed]

### Setup
```bash
git clone [repo]
cd [project]
[install command]
cp .env.example .env
# Fill in: [required vars]
[run command]
```

## Project Overview
[What this does - 2-3 sentences]

## Tech Stack
| Layer | Technology |
|-------|------------|
| Frontend | |
| Backend | |
| Database | |
| Hosting | |

## Architecture Summary
[Key points from ARCHITECTURE.md]

## ⚠️ Known Issues
[Priority-sorted from KNOWN_ISSUES.md]

## Key Decisions
[Important items from DECISIONS.md]

## External Services
[Summary with links to full docs]

## Environment Variables
| Variable | Purpose | Source |
|----------|---------|--------|

## Development
- Run locally: `[command]`
- Run tests: `[command]`
- Deploy: `[process]`

## Next Steps / Roadmap
[Planned work]
```

Save to project root as `HANDOFF.md`.

Confirm when generated.
