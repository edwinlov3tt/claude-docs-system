---
description: Audit local development setup - check dependencies, env, CORS, and generate setup instructions
allowed-tools: ["Read", "Write", "Edit", "Bash", "Grep"]
---

# Local Development Readiness Check

Audit the system for local development, identify blockers, and generate/update setup instructions.

## Phase 1: Environment Check

### 1.1 Runtime Detection
```bash
# Node.js
node --version 2>/dev/null || echo "Node.js: NOT FOUND"
npm --version 2>/dev/null || echo "npm: NOT FOUND"
pnpm --version 2>/dev/null || echo "pnpm: NOT FOUND"

# Python
python3 --version 2>/dev/null || echo "Python: NOT FOUND"
pip3 --version 2>/dev/null || echo "pip: NOT FOUND"

# Other runtimes
bun --version 2>/dev/null || echo "Bun: NOT FOUND"
deno --version 2>/dev/null || echo "Deno: NOT FOUND"
cargo --version 2>/dev/null || echo "Rust: NOT FOUND"
```

### 1.2 Package Manager Check
```bash
# Detect which package manager
if [ -f "pnpm-lock.yaml" ]; then echo "Package Manager: pnpm"; fi
if [ -f "yarn.lock" ]; then echo "Package Manager: yarn"; fi
if [ -f "package-lock.json" ]; then echo "Package Manager: npm"; fi
if [ -f "bun.lockb" ]; then echo "Package Manager: bun"; fi
if [ -f "requirements.txt" ]; then echo "Python: pip"; fi
if [ -f "pyproject.toml" ]; then echo "Python: poetry/pip"; fi
```

### 1.3 Dependencies Installed?
```bash
# Check node_modules
if [ -d "node_modules" ]; then
  echo "node_modules: EXISTS ($(ls node_modules | wc -l) packages)"
else
  echo "node_modules: MISSING - run install command"
fi

# Check Python venv
if [ -d ".venv" ] || [ -d "venv" ]; then
  echo "Python venv: EXISTS"
else
  echo "Python venv: MISSING (if Python project)"
fi
```

## Phase 2: Environment Variables Check

### 2.1 Find Required Env Vars
```bash
# Find env usage in code
grep -rh "process\.env\.\|import\.meta\.env\.\|os\.environ\|Env\." --include="*.ts" --include="*.tsx" --include="*.js" --include="*.py" . 2>/dev/null | grep -oE "(process\.env\.[A-Z_]+|import\.meta\.env\.[A-Z_]+)" | sort -u

# Check for .env.example
ls -la .env* 2>/dev/null
```

### 2.2 Verify Env Files
- [ ] `.env.example` or `.env.sample` exists?
- [ ] `.env` or `.env.local` exists?
- [ ] All required vars have values?

Create checklist of missing env vars.

## Phase 3: Service Dependencies

### 3.1 Database Check
```bash
# PostgreSQL
pg_isready 2>/dev/null && echo "PostgreSQL: RUNNING" || echo "PostgreSQL: NOT RUNNING/NOT INSTALLED"

# MySQL
mysqladmin ping 2>/dev/null && echo "MySQL: RUNNING" || echo "MySQL: NOT RUNNING/NOT INSTALLED"

# Redis
redis-cli ping 2>/dev/null && echo "Redis: RUNNING" || echo "Redis: NOT RUNNING/NOT INSTALLED"

# Docker (for containerized services)
docker info 2>/dev/null && echo "Docker: RUNNING" || echo "Docker: NOT RUNNING"
```

### 3.2 Check docker-compose.yml
```bash
if [ -f "docker-compose.yml" ] || [ -f "docker-compose.yaml" ]; then
  echo "Docker Compose: FOUND"
  docker compose config --services 2>/dev/null
fi
```

## Phase 4: Configuration Audit

### 4.1 CORS Configuration
Search for CORS setup:
```bash
grep -rn "cors\|CORS\|Access-Control" --include="*.ts" --include="*.js" --include="*.py" . 2>/dev/null | head -20
```

Check for:
- [ ] CORS middleware configured?
- [ ] Allowed origins include localhost?
- [ ] Credentials handling correct?

### 4.2 API/Backend Port Configuration
```bash
# Find port configurations
grep -rn "PORT\|:3000\|:8000\|:5000\|:4000\|listen" --include="*.ts" --include="*.js" --include="*.py" --include="*.json" . 2>/dev/null | head -20
```

### 4.3 Frontend Proxy/API URL
```bash
# Vite proxy
grep -A5 "proxy" vite.config.* 2>/dev/null

# Next.js rewrites
grep -A10 "rewrites\|async rewrites" next.config.* 2>/dev/null

# Environment API URL
grep "API_URL\|BACKEND_URL\|BASE_URL" .env* 2>/dev/null
```

## Phase 5: Build & Run Test

### 5.1 Test Build
```bash
# Try build command
if [ -f "package.json" ]; then
  npm run build 2>&1 | tail -20
fi
```

### 5.2 Identify Start Commands
```bash
# Extract scripts from package.json
cat package.json | grep -A30 '"scripts"' 2>/dev/null

# Common commands
# dev, start, serve, dev:frontend, dev:backend
```

## Phase 6: Generate Report & Instructions

### 6.1 Create/Update LOCAL_DEV.md

Create `.claude/docs/LOCAL_DEV.md`:

```markdown
# Local Development Setup

**Generated**: [DATE]
**Last Verified**: [DATE]

## Quick Start

```bash
# 1. Clone and enter directory
git clone [repo-url]
cd [project-name]

# 2. Install dependencies
[install command based on detection]

# 3. Set up environment
cp .env.example .env
# Fill in required values (see Environment Variables below)

# 4. Start services (if needed)
[docker compose up -d / start database commands]

# 5. Run development server
[dev command]
```

## Prerequisites

### Required
| Tool | Version | Status | Install |
|------|---------|--------|---------|
| Node.js | >=18.x | ✅/❌ | https://nodejs.org |
| [Package Manager] | >=X.x | ✅/❌ | [install link] |

### Optional
| Tool | Purpose | Status |
|------|---------|--------|
| Docker | Local services | ✅/❌ |

## Environment Variables

| Variable | Purpose | Required | Example |
|----------|---------|----------|---------|
| `DATABASE_URL` | Database connection | Yes | `postgresql://...` |
| `API_KEY` | External service | Yes | Get from [dashboard] |

### Getting API Keys
- **[Service 1]**: [How to get key]
- **[Service 2]**: [How to get key]

## Running the Application

### Full Stack (Recommended)
```bash
[command to run everything]
```

### Frontend Only
```bash
cd [frontend-dir]
[frontend dev command]
```
- URL: http://localhost:[port]

### Backend Only
```bash
cd [backend-dir]
[backend dev command]
```
- URL: http://localhost:[port]

### With Docker
```bash
docker compose up -d    # Start services
[dev command]           # Start app
docker compose down     # Stop services
```

## Service URLs

| Service | URL | Notes |
|---------|-----|-------|
| Frontend | http://localhost:3000 | |
| Backend/API | http://localhost:8000 | |
| Database | localhost:5432 | |

## Common Issues

### CORS Errors
[How CORS is configured and how to fix issues]

### Database Connection
[How to verify database connection]

### Environment Variables
[Common env var issues]

### Port Conflicts
```bash
# Find what's using a port
lsof -i :[port]
# Kill process
kill -9 [PID]
```

## Useful Commands

```bash
# Reset database
[reset command]

# Run tests
[test command]

# Lint/format
[lint command]

# Build for production
[build command]
```

## Troubleshooting

### [Common Issue 1]
**Symptom**: [What you see]
**Fix**: [How to fix]

### [Common Issue 2]
**Symptom**: [What you see]
**Fix**: [How to fix]
```

### 6.2 Update CLAUDE.md

Add local dev section to CLAUDE.md:

```markdown
## Local Development

### Start Dev Server
```bash
[primary dev command]
```

### URLs
- Frontend: http://localhost:[port]
- Backend: http://localhost:[port]

### Full setup instructions: `.claude/docs/LOCAL_DEV.md`
```

## Phase 7: Output Report

```markdown
# Local Dev Readiness Check

**Status**: ✅ Ready / ⚠️ Issues Found / ❌ Blocked

## Environment
| Check | Status | Action |
|-------|--------|--------|
| Node.js | ✅ v20.x | - |
| Dependencies | ❌ Missing | Run `pnpm install` |
| .env file | ⚠️ Incomplete | Add: API_KEY, DB_URL |

## Services
| Service | Status | Action |
|---------|--------|--------|
| PostgreSQL | ❌ Not running | `docker compose up -d` |
| Redis | ✅ Running | - |

## Configuration
| Check | Status | Notes |
|-------|--------|-------|
| CORS | ✅ Configured | localhost:3000 allowed |
| API URL | ✅ Set | Points to localhost:8000 |

## Blocking Issues
1. [Issue 1] - [How to fix]
2. [Issue 2] - [How to fix]

## To Start Development

```bash
# Fix blockers first:
[commands to fix issues]

# Then start:
[dev command]
```

## Generated/Updated Files
- `.claude/docs/LOCAL_DEV.md` - Full setup guide
- `CLAUDE.md` - Added dev server section

---
*Run this command again after fixing issues to verify*
```
