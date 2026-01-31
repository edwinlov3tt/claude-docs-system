---
description: Assess production readiness - compare current state against PRD/specs and generate actionable tasks
allowed-tools: ["Read", "Write", "Edit", "Bash", "Grep", "Glob"]
---

# Production Readiness Assessment

Analyze the project against PRD/specs to determine how far from production and generate actionable next steps.

## Phase 1: Gather Requirements

### 1.1 Find Specification Documents
Search for:
```bash
# Find PRD, specs, requirements docs
find . -type f \( -name "*.md" -o -name "*.txt" -o -name "*.pdf" \) | xargs grep -l -i "requirement\|specification\|PRD\|feature\|milestone" 2>/dev/null | head -20

# Common locations
ls -la PRD*.md SPEC*.md README.md docs/*.md .claude/docs/*.md 2>/dev/null
```

Read any found documents:
- PRD.md, SPEC.md, REQUIREMENTS.md
- README.md (often has feature lists)
- docs/ folder
- .claude/docs/ARCHITECTURE.md

### 1.2 Extract Requirements
From the documents, extract:
- **Must-have features** (P0/Critical)
- **Should-have features** (P1/High)
- **Nice-to-have features** (P2/Medium)
- **Non-functional requirements** (performance, security, scalability)
- **Integration requirements** (APIs, services, auth)

## Phase 2: Analyze Current State

### 2.1 Feature Inventory
Map what's actually implemented:
```bash
# Count routes/endpoints
grep -rn "app\.\(get\|post\|put\|delete\)\|router\." --include="*.ts" --include="*.js" . 2>/dev/null | wc -l

# Count components
find . -name "*.tsx" -o -name "*.vue" -o -name "*.svelte" 2>/dev/null | wc -l

# Count tests
find . -name "*.test.*" -o -name "*.spec.*" 2>/dev/null | wc -l
```

### 2.2 Check Critical Systems

**Authentication**
- [ ] Auth system implemented?
- [ ] Protected routes?
- [ ] Session/token management?

**Database**
- [ ] Schema complete?
- [ ] Migrations ready?
- [ ] Seed data?

**Error Handling**
- [ ] Global error handling?
- [ ] User-friendly error messages?
- [ ] Error logging?

**Security**
- [ ] Input validation?
- [ ] CORS configured?
- [ ] Rate limiting?
- [ ] Environment variables (no hardcoded secrets)?

**Performance**
- [ ] Caching implemented?
- [ ] Optimized queries?
- [ ] Asset optimization?

**Deployment**
- [ ] Build process works?
- [ ] Environment configs?
- [ ] CI/CD pipeline?

### 2.3 Review KNOWN_ISSUES.md
Check for blocking issues that must be resolved before production.

## Phase 3: Gap Analysis

Create a gap matrix:

| Requirement | Status | Gap | Priority | Effort |
|-------------|--------|-----|----------|--------|
| [Feature from PRD] | ✅/🔄/❌ | [What's missing] | P0/P1/P2 | S/M/L |

Status:
- ✅ Complete
- 🔄 Partial (X% done)
- ❌ Not Started

Effort:
- S = Small (< 1 day)
- M = Medium (1-3 days)
- L = Large (3+ days)

## Phase 4: Generate Assessment Report

Create `.claude/docs/ASSESSMENT.md`:

```markdown
# Production Readiness Assessment

**Generated**: [DATE]
**Project**: [NAME]
**Assessed Against**: [PRD/SPEC file names]

## Executive Summary

**Overall Readiness**: [X]% production ready
**Estimated Time to Production**: [X days/weeks]
**Blocking Issues**: [count]

### Quick Stats
| Category | Complete | Partial | Missing |
|----------|----------|---------|---------|
| Core Features | X | X | X |
| Auth & Security | X | X | X |
| API Endpoints | X | X | X |
| UI/UX | X | X | X |
| Testing | X | X | X |
| DevOps | X | X | X |

---

## Feature Gap Analysis

### P0 - Must Have (Blocking Production)

| Feature | Status | Gap | Effort | Task |
|---------|--------|-----|--------|------|
| [Feature] | ❌ | [Missing piece] | M | [Link to task] |

### P1 - Should Have (Can ship without, but soon after)

| Feature | Status | Gap | Effort |
|---------|--------|-----|--------|
| [Feature] | 🔄 50% | [Missing piece] | S |

### P2 - Nice to Have (Post-launch)

| Feature | Status | Notes |
|---------|--------|-------|
| [Feature] | ❌ | Defer to v1.1 |

---

## Critical Path to Production

### Phase 1: Blockers (Must complete first)
1. [ ] [Blocking item 1] - [effort]
2. [ ] [Blocking item 2] - [effort]

### Phase 2: Core Features
1. [ ] [Feature 1] - [effort]
2. [ ] [Feature 2] - [effort]

### Phase 3: Polish & Testing
1. [ ] [Testing task]
2. [ ] [Polish task]

### Phase 4: Deployment Prep
1. [ ] [Deployment task]
2. [ ] [Monitoring task]

---

## Blocking Issues

Issues from KNOWN_ISSUES.md that block production:

| Issue | Severity | Impact | Resolution |
|-------|----------|--------|------------|
| [Issue] | CRITICAL | [Impact] | [What to do] |

---

## Non-Functional Requirements

| Requirement | Current State | Target | Gap |
|-------------|---------------|--------|-----|
| Performance | [current] | [target] | [gap] |
| Security | [current] | [target] | [gap] |
| Scalability | [current] | [target] | [gap] |

---

## Recommended Next Actions

### This Week (Immediate)
1. **[Action]** - [Why it's urgent]
2. **[Action]** - [Why it's urgent]

### Next 2 Weeks
1. **[Action]**
2. **[Action]**

### Before Launch
1. **[Action]**
2. **[Action]**

---

## Risk Register

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| [Risk] | High/Med/Low | High/Med/Low | [Plan] |

---

*Run `/task create [feature]` to generate detailed implementation tasks*
*Run `/assess` again after completing major milestones*
```

## Phase 5: Generate Task Stubs

For each P0 gap, create a task stub in `.claude/tasks/`:

```markdown
# Task: [Feature Name]

## Status: ⬜ Not Started

## Priority: P0 - Blocks Production

## Goal
[Brief description]

## Gap Analysis
- **Current**: [What exists]
- **Required**: [What PRD specifies]
- **Delta**: [What's missing]

## Implementation Steps
1. [ ] Step 1
2. [ ] Step 2

## Files to Modify
- `path/to/file.ts`

## Verification
- [ ] [How to verify complete]

## Dependencies
- None / [List dependencies]
```

## Output

```markdown
# Assessment Complete

**Readiness**: [X]% production ready
**Time to Production**: ~[X] days

## Summary
- P0 Blockers: X items
- P1 Should-haves: X items  
- P2 Nice-to-haves: X items
- Blocking Issues: X

## Generated Files
- `.claude/docs/ASSESSMENT.md` - Full assessment report
- `.claude/tasks/[feature].md` - Task stubs for P0 items

## Immediate Actions
1. [Most critical action]
2. [Second most critical]
3. [Third]

Run `/task status` to see all tasks
Run `/task [name]` to expand a task with full implementation details
```
