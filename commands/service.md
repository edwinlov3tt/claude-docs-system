---
description: Document a service integration (manual - /sync does this automatically)
allowed-tools: ["Read", "Write", "Edit", "Bash", "Grep"]
---

# Document Service

**Service:** $ARGUMENTS

## Analyze Integration

```bash
grep -rn "[service]" --include="*.ts" --include="*.js" --include="*.py" . 2>/dev/null | head -20
grep -i "[service]" .env* 2>/dev/null
```

## Create/Update `.claude/docs/services/{service}.md`

```markdown
# [Service] Integration

## Overview
| | |
|---|---|
| **Service** | [Name] |
| **Purpose** | [Why we use it] |
| **Docs** | [URL] |

## Configuration

| Variable | Purpose | Required |
|----------|---------|----------|
| `API_KEY` | Auth | Yes |

## Implementation
- **Files**: Where it's used
- **SDK**: Package and version

## Usage Example
```typescript
// Example code
```

## Known Issues
[Any gotchas]
```

Confirm when documented.
