---
description: Scan for undocumented services and components, then selectively document them
allowed-tools: ["Read", "Write", "Edit", "Bash", "Grep"]
---

# Scan for Undocumented Services & Components

Scan the codebase to find services and components that are implemented but not documented. Returns a list for you to select which to document.

**Selection (optional):** $ARGUMENTS

## Mode Detection

If `$ARGUMENTS` is provided and looks like a comma-separated list (e.g., "stripe, supabase, auth-provider"):
- Skip scanning
- Document only the specified items
- Go directly to Phase 4

Otherwise, run full scan.

---

## Phase 1: Discover External Services

### 1.1 Scan Package Dependencies
```bash
echo "=== Package Dependencies ==="
# Node.js
cat package.json 2>/dev/null | grep -E "(@supabase|@stripe|@sendgrid|@twilio|openai|@anthropic|@aws-sdk|@google-cloud|@azure|@cloudflare|firebase|@prisma|@planetscale|@upstash|resend|postmark|mailgun|@sentry|datadog|@segment|mixpanel|amplitude|@auth0|@clerk|lucia|next-auth|passport)" | sed 's/[",:]//g' | awk '{print $1}'

# Python
cat requirements.txt pyproject.toml 2>/dev/null | grep -E "(supabase|stripe|sendgrid|twilio|openai|anthropic|boto3|google-cloud|azure|cloudflare|firebase|prisma|sentry|datadog|segment|auth0)" 2>/dev/null
```

### 1.2 Scan for Service Imports
```bash
echo "=== Service Imports in Code ==="
grep -rh --include="*.ts" --include="*.tsx" --include="*.js" --include="*.py" \
  -E "^import.*from ['\"](@supabase|@stripe|stripe|@sendgrid|@twilio|openai|@anthropic|@aws-sdk|@google-cloud|@azure|@cloudflare|firebase|@prisma|@planetscale|@upstash|resend|postmark|@sentry|@segment|@auth0|@clerk|next-auth)" . 2>/dev/null | \
  grep -v node_modules | \
  sed 's/.*from ["\x27]\([^"\x27]*\)["\x27].*/\1/' | \
  sort -u
```

### 1.3 Scan Environment Variables for Services
```bash
echo "=== Service-Related Env Vars ==="
grep -rh --include="*.ts" --include="*.tsx" --include="*.js" --include="*.env*" \
  -E "(SUPABASE|STRIPE|SENDGRID|TWILIO|OPENAI|ANTHROPIC|AWS|GOOGLE|AZURE|CLOUDFLARE|FIREBASE|SENTRY|DATADOG|SEGMENT|AUTH0|CLERK|RESEND|POSTMARK|REDIS|DATABASE|POSTGRES|MONGO|MYSQL)" . 2>/dev/null | \
  grep -v node_modules | \
  grep -oE "[A-Z_]+_(KEY|SECRET|URL|TOKEN|ID|API)" | \
  sort -u
```

### 1.4 Get Currently Documented Services
```bash
echo "=== Already Documented ==="
ls .claude/docs/services/*.md 2>/dev/null | xargs -I {} basename {} .md
```

---

## Phase 2: Discover Internal Components

### 2.1 Scan for Major Modules
```bash
echo "=== Major Directories ==="
# Find directories that likely contain components
find src lib app components pages api routes services utils hooks -type d -maxdepth 1 2>/dev/null | sort -u

# Find files with significant exports
echo "=== Files with Multiple Exports ==="
grep -rl --include="*.ts" --include="*.tsx" "^export " . 2>/dev/null | grep -v node_modules | head -30
```

### 2.2 Scan for API Routes
```bash
echo "=== API Routes ==="
# Next.js App Router
find app -name "route.ts" -o -name "route.js" 2>/dev/null

# Next.js Pages Router  
find pages/api -name "*.ts" -o -name "*.js" 2>/dev/null

# Express/Hono routes
grep -rln --include="*.ts" --include="*.js" "app\.\(get\|post\|put\|delete\|patch\)\|router\.\(get\|post\|put\|delete\|patch\)" . 2>/dev/null | grep -v node_modules
```

### 2.3 Scan for React Components
```bash
echo "=== React Components ==="
find . -name "*.tsx" -o -name "*.jsx" 2>/dev/null | grep -v node_modules | grep -E "(components|ui|features)" | head -30
```

### 2.4 Scan for Utilities/Hooks
```bash
echo "=== Utilities and Hooks ==="
find . -path "*/utils/*" -o -path "*/hooks/*" -o -path "*/lib/*" 2>/dev/null | grep -v node_modules | grep -E "\.(ts|js)$" | head -20
```

### 2.5 Get Currently Documented Components
```bash
echo "=== Already Documented ==="
ls .claude/docs/components/*.md 2>/dev/null | xargs -I {} basename {} .md
```

---

## Phase 3: Compare and List Undocumented

### 3.1 Build Undocumented Services List

Compare discovered services against `.claude/docs/services/*.md`:

```markdown
## Undocumented Services Found

| # | Service | Evidence | Confidence |
|---|---------|----------|------------|
| 1 | stripe | Package dep + env vars | HIGH |
| 2 | supabase | Package dep + imports | HIGH |
| 3 | openai | Imports found | MEDIUM |
| 4 | sendgrid | Env var only | LOW |
```

### 3.2 Build Undocumented Components List

Compare discovered components against `.claude/docs/components/*.md`:

```markdown
## Undocumented Components Found

| # | Component | Type | Files |
|---|-----------|------|-------|
| 1 | auth-provider | Context/Hook | src/providers/auth.tsx |
| 2 | api-routes | API | app/api/* (15 routes) |
| 3 | database | Utility | src/lib/db.ts |
| 4 | ui-components | UI | src/components/* (20+ files) |
```

---

## Phase 4: Present Selection Interface

```markdown
# Undocumented Items Found

## Services (X found)

| # | Service | Evidence |
|---|---------|----------|
| 1 | stripe | Package + env vars + imports |
| 2 | supabase | Package + imports |
| 3 | openai | Imports in src/lib/ai.ts |
| 4 | resend | Env var RESEND_API_KEY |

## Components (X found)

| # | Component | Type | Location |
|---|-----------|------|----------|
| 5 | auth | Provider/Context | src/providers/ |
| 6 | api | Routes | app/api/ |
| 7 | hooks | Custom Hooks | src/hooks/ |
| 8 | utils | Utilities | src/lib/ |

---

## To Document

Reply with comma-separated numbers or names:

```
/scan 1, 2, 5
```

Or:

```
/scan stripe, supabase, auth
```

Or document all:

```
/scan all
```
```

---

## Phase 5: Document Selected Items (if selection provided)

If `$ARGUMENTS` contains selections, document each one:

### For Services:
1. Search codebase for all usage of that service
2. Find all related environment variables
3. Find all files importing/using the service
4. Create `.claude/docs/services/[service].md` with:
   - Purpose in this project
   - Environment variables required
   - Files where it's used
   - Usage patterns found in code
   - Any error handling patterns

### For Components:
1. Read the component files
2. Identify exports and interfaces
3. Find usage patterns across codebase
4. Create `.claude/docs/components/[component].md` with:
   - Purpose and responsibility
   - Key exports
   - Dependencies
   - Usage examples from actual code

---

## Phase 6: Output

### If Scan Only (no selection):
```markdown
# Scan Complete

**Found**: X undocumented services, Y undocumented components

[Display tables from Phase 4]

**To document selected items:**
```
/scan 1, 3, 5
```

**To document all:**
```
/scan all
```
```

### If Documenting:
```markdown
# Documentation Created

## Services Documented
| Service | File |
|---------|------|
| stripe | `.claude/docs/services/stripe.md` |
| supabase | `.claude/docs/services/supabase.md` |

## Components Documented
| Component | File |
|-----------|------|
| auth | `.claude/docs/components/auth.md` |

## Summary
- Environment variables found: X
- Files analyzed: Y
- Usage patterns documented: Z

---

Run `/scan` again to check for remaining undocumented items.
```
