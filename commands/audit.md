---
description: Full project audit - analyze entire codebase and populate all documentation
allowed-tools: ["Read", "Write", "Edit", "Bash", "Grep", "Glob"]
---

# Full Project Audit

Perform a comprehensive analysis of the entire codebase and populate all documentation.

## Phase 1: Discovery

### 1.1 Project Metadata
- Read `package.json`, `requirements.txt`, `pyproject.toml`, `Cargo.toml`, or equivalent
- Identify project name, version, dependencies
- Run `!git remote -v` for repository info

### 1.2 Directory Structure
- Map the full directory tree (excluding node_modules, .git, dist, build, __pycache__, .venv)
- Identify key directories: src, api, workers, components, utils, lib, etc.

### 1.3 Tech Stack Detection
Scan for:
- **Frontend**: React, Vue, Svelte, Next.js, Nuxt, Astro
- **Backend**: Express, FastAPI, Hono, Django, Flask, Next.js API routes
- **Database**: Supabase, PostgreSQL, MongoDB, D1, Prisma, Drizzle
- **Hosting**: Vercel, Cloudflare, Railway, Netlify, AWS
- **Build tools**: Vite, Webpack, esbuild, Turbopack

### 1.4 External Services
Search for:
- API keys and service URLs in env files
- SDK imports (supabase, stripe, openai, anthropic, cloudflare, etc.)
- Configuration files (wrangler.toml, vercel.json, etc.)

## Phase 2: Code Analysis

### 2.1 Entry Points
- Find main entry files
- Trace application startup flow

### 2.2 API Routes / Endpoints
- Find all API endpoints
- Document: method, path, purpose

### 2.3 Database Schema
- Find schema definitions
- Document main entities and relationships

### 2.4 Environment Variables
- Compile ALL env vars used across the codebase
- Categorize: required vs optional, by service

### 2.5 Potential Issues
Scan for:
- TODO/FIXME/HACK/XXX comments: `!grep -rn "TODO\|FIXME\|HACK\|XXX" --include="*.ts" --include="*.tsx" --include="*.js" --include="*.py" . 2>/dev/null | head -50`
- Empty catch blocks
- Hardcoded values
- Missing error handling

## Phase 3: Git History Analysis

```bash
# Recent commits
git log --oneline -20 2>/dev/null

# Most changed files
git log --pretty=format: --name-only -30 2>/dev/null | sort | uniq -c | sort -rn | head -15
```

## Phase 4: Documentation Generation

### 4.1 Update ARCHITECTURE.md
- Real tech stack
- Directory structure
- Environment variables
- Deployment info

### 4.2 Update KNOWN_ISSUES.md
- All TODOs/FIXMEs found
- Potential issues identified
- Missing error handling

### 4.3 Update CHANGELOG.md
- Recent commits grouped by feature/fix

### 4.4 Create Service Documentation
For each external service → `.claude/docs/services/{service}.md`

### 4.5 Create Component Documentation
For major components → `.claude/docs/components/{component}.md`

### 4.6 Update DECISIONS.md
Record obvious architectural decisions

## Phase 5: Summary Report

```markdown
# Project Audit Complete

**Project**: [name]
**Tech Stack**: [summary]

## Documentation Updated
- ARCHITECTURE.md ✅
- KNOWN_ISSUES.md - [X issues]
- CHANGELOG.md ✅
- DECISIONS.md - [X decisions]
- services/*.md - [list]
- components/*.md - [list]

## Key Findings
[Notable discoveries]

## Immediate Attention
[Critical items]

## Next Steps
[Recommendations]
```
