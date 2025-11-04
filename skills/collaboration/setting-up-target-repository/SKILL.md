---
name: Setting Up Target Repository
description: Prepare the target repository for feature development with branch creation and project setup
when_to_use: when starting feature work that needs a clean branch in the target repository, before executing implementation plans
version: 2.0.0
---

# Setting Up Target Repository

## Overview

Prepare the `/target` repository for feature development by creating a new branch and ensuring the project is ready for implementation.

**Core principle:** Clean branch + verified setup = reliable development environment.

**Announce at start:** "I'm setting up the target repository for feature development."

## Setup Process

Work directly in the `/target` directory which contains the main project repository.

## Branch Creation

### 1. Create Feature Branch

```bash
cd /target
git checkout -b "$BRANCH_NAME"
```

**Branch naming conventions:**
- Feature: `feature/feature-name`
- Bug fix: `fix/bug-description`
- Refactor: `refactor/component-name`

### 2. Run Project Setup

Auto-detect and run appropriate setup:

```bash
# Node.js
if [ -f package.json ]; then npm install; fi

# Rust
if [ -f Cargo.toml ]; then cargo build; fi

# Python
if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
if [ -f pyproject.toml ]; then poetry install; fi

# Go
if [ -f go.mod ]; then go mod download; fi
```

### 2.5. Check Configuration Requirements

**Environment Variables (.env files)**

Many projects need environment configuration. Check for:

```bash
# Look for .env.example or .env.template
if [ -f .env.example ]; then
  echo "⚠️  Copy .env.example to .env and configure:"
  echo "cp .env.example .env"
  echo "# Edit .env with appropriate values"
fi
```

**Node.js Module Type Issues**

If using TypeScript/ES modules in Node.js, ensure `package.json` specifies module type:

```json
{
  "type": "module"
}
```

Common symptoms:
- `MODULE_TYPELESS_PACKAGE_JSON` warnings
- "does not parse as CommonJS" errors
- Import/export syntax errors

### 3. Verify Clean Baseline

Run tests to ensure target repository starts clean:

```bash
# Examples - use project-appropriate command
npm test
cargo test
pytest
go test ./...
```

**If tests fail:** Report failures, ask whether to proceed or investigate.

**If tests pass:** Report ready.

### 4. Report Location

```
Target repository ready in /target
Tests passing (<N> tests, 0 failures)
Ready to implement <feature-name>
```

## Quick Reference

| Situation | Action |
|-----------|--------|
| Tests fail during baseline | Report failures + ask |
| No package.json/Cargo.toml | Skip dependency install |
| Branch already exists | Checkout existing or create with different name |
| Dependencies missing | Auto-detect and install based on project files |
| .env.example exists | Guide user to copy and configure .env |
| ES module syntax errors | Check if package.json needs `"type": "module"` |
| Database connection errors | Check for missing DATABASE_URL or similar |

## Common Mistakes

**Skipping baseline test verification**
- **Problem:** Can't distinguish new bugs from pre-existing issues
- **Fix:** Always run tests before starting implementation

**Assuming project setup requirements**
- **Problem:** Breaks on projects using different tools
- **Fix:** Auto-detect from project files (package.json, etc.)

**Proceeding with failing tests**
- **Problem:** Can't distinguish new bugs from pre-existing issues
- **Fix:** Report failures, get explicit permission to proceed

**Hardcoding setup commands**
- **Problem:** Breaks on projects using different tools
- **Fix:** Auto-detect from project files (package.json, etc.)

**Missing environment configuration**
- **Problem:** App fails with "connection string" or "configuration" errors
- **Fix:** Check for .env.example, guide user through setup

**Node.js module type confusion**
- **Problem:** ES module syntax in CommonJS project (or vice versa)
- **Fix:** Check package.json for `"type": "module"`, add if using import/export

## Example Workflow

```
You: I'm setting up the target repository for feature development.

[Create branch: git checkout -b feature/auth]
[Run npm install]
[Run npm test - 47 passing]

Target repository ready in /target
Tests passing (47 tests, 0 failures)
Ready to implement auth feature
```

## Red Flags

**Never:**
- Skip baseline test verification
- Proceed with failing tests without asking
- Skip dependency installation when needed
- Assume project setup requirements

**Always:**
- Create clean feature branch
- Auto-detect and run project setup
- Verify clean test baseline
- Work directly in /target repository

## Integration

**Called by:**
- skills/collaboration/brainstorming (Phase 4)
- Any skill needing target repository setup

**Pairs with:**
- skills/collaboration/finishing-a-development-branch (cleanup)
- skills/collaboration/executing-plans (work happens here)