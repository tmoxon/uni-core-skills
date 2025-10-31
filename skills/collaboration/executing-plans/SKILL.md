---
name: Executing Plans
description: Execute detailed plans in batches with review checkpoints
when_to_use: when partner provides a complete implementation plan to execute in controlled batches with review checkpoints
version: 2.2.0
---

# Executing Plans

## Context

- You are running inside a Docker container.
- `/workspace` is your own code and logic.
- `/target` is the actual target repository where code should be written.
- Always treat `/target` as the main project.

## Overview

Load plan, review critically, execute tasks in batches, report for review between batches.

**Core principle:** Batch execution with checkpoints for architect review.

**Announce at start:** "I'm using the Executing Plans skill to implement this plan."

## The Process

### Step 1: Understand the target repository
1. Read `/target/claude.md` if it exists — this describes the project setup (framework, conventions, coding style, etc.).
2. If that file does not exist, infer the project type by reading:
   - `/target/package.json`
   - `/target/tsconfig.json`
   - `/target/src` folder structure
3. Summarize what you learn (e.g., "React + Next.js app using TypeScript").
4. Keep this in mind when generating code or patching files.

### Step 2: Load and Review Plan
1. Read plan file
2. Review critically - identify any questions or concerns about the plan
3. If concerns: Raise them with your human partner before starting
4. If no concerns: Create TodoWrite and proceed

### Step 3: Implement the plan in the target repo
- Create or update files directly under `/target/src/...`
- If a file needs patching (e.g., add route, enable flag), add those patch instructions to `/target/claude.md` under `actions:`.
- Do **not** write code to `/workspace`; all code goes in `/target`.
- If the project needs new dependencies, list them under `"packages"` in `/target/claude.md` (this will trigger `npm install` later).

Example:

```json
{
  "actions": [
    {
      "kind": "applyPatch",
      "file": "src/config/example-config.ts",
      "patch": "--- a/src/config/example-config.ts\n+++ b/src/config/example-config.ts\n@@\n-export const featureEnabled = false;\n+export const featureEnabled = true;\n"
    }
  ]
}
```

### Step 4: Execute Batch
**Default: First 3 tasks**

For each task:
1. Mark as in_progress
2. Follow each step exactly (plan has bite-sized steps)
3. Run verifications as specified
4. Mark as completed

### Step 5: Apply and commit the plan

When you're done writing or updating `/target/claude.md`, run the following commands:

```bash
# make git safe (once per container)
git config --global --add safe.directory /target

# create or switch to a working branch
git -C /target checkout -B chore/claude/apply

# apply structured steps (patches, file writes, npm installs)
node /workspace/src/cli.js --repo /target --apply

# add and commit all changes
git -C /target add -A
git -C /target commit -m "chore: apply Claude plan" || echo "nothing to commit"
```

After running, confirm the branch contains your changes:

```bash
git -C /target status
```

### Step 6: Report
When batch complete:
- Show what was implemented
- Show verification output
- Say: "Ready for feedback."

### Step 7: Continue
Based on feedback:
- Apply changes if needed
- Execute next batch
- Repeat until complete

### Step 8: Report back
After applying and committing:
- Print a summary of what changed (files added, modified, deleted).
- Confirm the current branch name.
- Remind the user: "Push this branch to open a PR."

When creating a branch, name it based on the plan topic, for example:
- Feature: `feature/add-dashboard`
- Bug fix: `fix/login-error`
- Refactor: `refactor/auth-service`
- Fallback: `chore/claude/apply`

### Step 9: Complete Development

After all tasks complete and verified:
- Announce: "I'm using the Finishing a Development Branch skill to complete this work."
- Switch to skills/collaboration/finishing-a-development-branch
- Follow that skill to verify tests, present options, execute choice

## When to Stop and Ask for Help

**STOP executing immediately when:**
- Hit a blocker mid-batch (missing dependency, test fails, instruction unclear)
- Plan has critical gaps preventing starting
- You don't understand an instruction
- Verification fails repeatedly

**Ask for clarification rather than guessing.**

## When to Revisit Earlier Steps

**Return to Review (Step 1) when:**
- Partner updates the plan based on your feedback
- Fundamental approach needs rethinking

**Don't force through blockers** - stop and ask.

## Remember
- Review plan critically first
- Follow plan steps exactly
- Don't skip verifications
- Reference skills when plan says to
- Between batches: just report and wait
- Stop when blocked, don't guess
