---
description: Scan for API keys, secrets, and hardcoded sensitive data before pushing to GitHub
allowed-tools: ["Read", "Write", "Edit", "Bash", "Grep"]
---

# Security Scan - Check for Secrets

Scan the entire codebase for API keys, secrets, tokens, and hardcoded sensitive information before pushing to GitHub.

## Phase 1: Pattern-Based Scan

### 1.1 Common Secret Patterns
```bash
echo "=== Scanning for secrets ==="

# API Keys (generic patterns)
echo "--- API Keys ---"
grep -rn --include="*.ts" --include="*.tsx" --include="*.js" --include="*.jsx" --include="*.py" --include="*.json" --include="*.yml" --include="*.yaml" --include="*.env" \
  -E "(api[_-]?key|apikey|api_secret)['\"]?\s*[:=]\s*['\"][a-zA-Z0-9_\-]{16,}" . 2>/dev/null | grep -v node_modules | grep -v ".env.example"

# AWS Keys
echo "--- AWS Credentials ---"
grep -rn -E "(AKIA[0-9A-Z]{16}|aws[_-]?(access|secret)[_-]?key)" . 2>/dev/null | grep -v node_modules

# Private Keys
echo "--- Private Keys ---"
grep -rn -E "(BEGIN (RSA |EC |DSA |OPENSSH )?PRIVATE KEY)" . 2>/dev/null | grep -v node_modules

# Tokens
echo "--- Tokens ---"
grep -rn --include="*.ts" --include="*.tsx" --include="*.js" --include="*.jsx" --include="*.py" \
  -E "(token|bearer|auth)['\"]?\s*[:=]\s*['\"][a-zA-Z0-9_\-\.]{20,}" . 2>/dev/null | grep -v node_modules | grep -v ".env.example"

# Database URLs with credentials
echo "--- Database URLs ---"
grep -rn -E "(postgresql|mysql|mongodb|redis)://[^:]+:[^@]+@" . 2>/dev/null | grep -v node_modules | grep -v ".env.example"

# Slack/Discord webhooks
echo "--- Webhooks ---"
grep -rn -E "hooks\.(slack|discord)\.com/services/[A-Za-z0-9/]+" . 2>/dev/null | grep -v node_modules

# Generic secrets
echo "--- Generic Secrets ---"
grep -rn --include="*.ts" --include="*.tsx" --include="*.js" --include="*.jsx" --include="*.py" \
  -E "(password|secret|credential)['\"]?\s*[:=]\s*['\"][^'\"]{8,}" . 2>/dev/null | grep -v node_modules | grep -v ".env.example" | grep -v "password.*process\.env" | grep -v "secret.*process\.env"
```

### 1.2 Service-Specific Keys
```bash
# Stripe
echo "--- Stripe ---"
grep -rn -E "(sk_live_|sk_test_|pk_live_|pk_test_)[a-zA-Z0-9]{24,}" . 2>/dev/null | grep -v node_modules

# OpenAI
echo "--- OpenAI ---"
grep -rn -E "sk-[a-zA-Z0-9]{48}" . 2>/dev/null | grep -v node_modules

# Anthropic
echo "--- Anthropic ---"
grep -rn -E "sk-ant-[a-zA-Z0-9\-]{40,}" . 2>/dev/null | grep -v node_modules

# GitHub
echo "--- GitHub ---"
grep -rn -E "(ghp_[a-zA-Z0-9]{36}|github_pat_[a-zA-Z0-9_]{22,})" . 2>/dev/null | grep -v node_modules

# Supabase
echo "--- Supabase ---"
grep -rn -E "eyJ[a-zA-Z0-9_-]*\.eyJ[a-zA-Z0-9_-]*\.[a-zA-Z0-9_-]*" . 2>/dev/null | grep -v node_modules | head -10

# Cloudflare
echo "--- Cloudflare ---"
grep -rn -E "([a-f0-9]{37}|[a-zA-Z0-9_]{40})" . 2>/dev/null | grep -i cloudflare | grep -v node_modules

# SendGrid
echo "--- SendGrid ---"
grep -rn -E "SG\.[a-zA-Z0-9_-]{22}\.[a-zA-Z0-9_-]{43}" . 2>/dev/null | grep -v node_modules

# Twilio
echo "--- Twilio ---"
grep -rn -E "(AC[a-f0-9]{32}|SK[a-f0-9]{32})" . 2>/dev/null | grep -v node_modules
```

### 1.3 Hardcoded URLs and IPs
```bash
echo "--- Hardcoded Production URLs ---"
grep -rn --include="*.ts" --include="*.tsx" --include="*.js" --include="*.jsx" \
  -E "https?://[a-z0-9\-]+\.(com|io|app|dev|net|org)" . 2>/dev/null | grep -v node_modules | grep -v "localhost" | grep -v "example.com" | grep -v "placeholder" | head -20

echo "--- Hardcoded IPs ---"
grep -rn -E "\b\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}\b" . 2>/dev/null | grep -v node_modules | grep -v "127.0.0.1" | grep -v "0.0.0.0" | grep -v "255." | head -10
```

## Phase 2: File-Based Checks

### 2.1 Sensitive Files That Shouldn't Be Committed
```bash
echo "=== Checking for sensitive files ==="

# Check if these exist and are NOT in .gitignore
for file in ".env" ".env.local" ".env.production" ".env.*.local" "*.pem" "*.key" "id_rsa" "id_ed25519" ".npmrc" ".pypirc" "credentials.json" "service-account.json" "secrets.json" "config.local.js"; do
  if ls $file 2>/dev/null; then
    if ! grep -q "$file" .gitignore 2>/dev/null; then
      echo "⚠️  FOUND AND NOT IN .GITIGNORE: $file"
    fi
  fi
done
```

### 2.2 Check .gitignore Coverage
```bash
echo "=== Checking .gitignore ==="
cat .gitignore 2>/dev/null || echo "⚠️  NO .GITIGNORE FILE FOUND"

# Recommended entries
echo ""
echo "--- Recommended .gitignore entries ---"
echo ".env"
echo ".env.local"
echo ".env.*.local"
echo "*.pem"
echo "*.key"
echo ".npmrc"
echo "credentials.json"
echo "service-account*.json"
```

## Phase 3: Analyze Results

### 3.1 Categorize Findings

**CRITICAL** (Block push immediately):
- Actual API keys with real values
- Database credentials
- Private keys
- Production secrets

**HIGH** (Review before push):
- Hardcoded URLs that should be env vars
- Tokens that might be test/fake but look real
- Files that should be in .gitignore

**MEDIUM** (Should fix but not blocking):
- Hardcoded configuration that should be env vars
- Debug credentials left in comments

**FALSE POSITIVES** (Ignore):
- Example values in .env.example
- Documentation showing format
- Test fixtures with fake data
- References to process.env (correct pattern)

## Phase 4: Generate Report

### 4.1 Update KNOWN_ISSUES.md

For each CRITICAL or HIGH finding, add to `.claude/docs/KNOWN_ISSUES.md`:

```markdown
### [CRITICAL] Hardcoded Secret Found - BLOCKS GITHUB PUSH
- **Location**: `[file:line]`
- **Type**: [API Key / Database URL / Token / etc.]
- **Service**: [Stripe / OpenAI / AWS / etc.]
- **Symptom**: Hardcoded secret will be exposed if pushed to GitHub
- **Workaround**: DO NOT PUSH until resolved
- **Proper Fix**: 
  1. Move value to `.env` file
  2. Replace hardcoded value with `process.env.VAR_NAME`
  3. Add `.env` to `.gitignore`
  4. Rotate the exposed secret (it may already be in git history)
- **Added**: [DATE]
```

### 4.2 Create Security Report

```markdown
# Security Scan Report

**Scanned**: [DATE]
**Status**: ✅ CLEAN / ⚠️ ISSUES FOUND / 🚫 BLOCKED

## Summary

| Severity | Count | Action Required |
|----------|-------|-----------------|
| 🔴 CRITICAL | X | DO NOT PUSH - Fix immediately |
| 🟠 HIGH | X | Review before push |
| 🟡 MEDIUM | X | Should fix soon |
| ⚪ False Positives | X | No action needed |

## Critical Findings (BLOCKS PUSH)

| File | Line | Type | Service |
|------|------|------|---------|
| `src/config.ts` | 45 | API Key | Stripe |

## High Priority Findings

| File | Line | Issue | Recommendation |
|------|------|-------|----------------|
| `src/api.ts` | 12 | Hardcoded URL | Move to env var |

## Recommendations

### Immediate Actions
1. [ ] [Specific action for each critical finding]

### Before Push Checklist
- [ ] All CRITICAL issues resolved
- [ ] All HIGH issues reviewed
- [ ] `.gitignore` includes sensitive files
- [ ] `.env.example` has placeholders (not real values)
- [ ] No secrets in git history (if previously committed, rotate them)

### If Secrets Were Previously Committed
If secrets were already pushed to GitHub:
1. **Rotate immediately** - Generate new keys/tokens
2. Consider using `git filter-branch` or BFG Repo-Cleaner
3. Force push won't help - secrets are in git history forever

## Files Checked
- [X] Source files (*.ts, *.tsx, *.js, *.jsx, *.py)
- [X] Config files (*.json, *.yml, *.yaml)
- [X] Environment files (.env*)
- [X] .gitignore coverage
```

## Phase 5: Output

```markdown
# Security Scan Complete

**Status**: [✅ CLEAN - Safe to push / 🚫 BLOCKED - X issues found]

## Results

| Check | Status |
|-------|--------|
| API Keys | ✅/🚫 |
| Database Credentials | ✅/🚫 |
| Private Keys | ✅/🚫 |
| Tokens | ✅/🚫 |
| Hardcoded URLs | ✅/⚠️ |
| .gitignore | ✅/⚠️ |

## Issues Added to KNOWN_ISSUES.md
[List of issues added]

## Before Pushing

```bash
# Verify no secrets in staged files
git diff --cached --name-only

# Check what will be pushed
git log origin/main..HEAD --oneline
```

---

**If clean**: Safe to `git push`
**If issues found**: Fix all CRITICAL issues, then run `/secrets` again
```
