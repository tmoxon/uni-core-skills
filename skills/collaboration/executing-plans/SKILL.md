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

Load behavioral plan, review critically, execute tasks using strict Test-Driven Development methodology, report for review between batches.

**Core principle:** TDD-driven batch execution with checkpoints for architect review.

**Every task MUST follow Red-Green-Refactor cycle using @testing/test-driven-development skill.**

**Announce at start:** "I'm using the Executing Plans skill to implement this plan with Test-Driven Development."

## The Process

### Step 1: Understand the target repository
1. Read `/target/claude.md` if it exists — this describes the project setup (framework, conventions, coding style, etc.).
2. If that file does not exist, infer the project type by reading:
   - `/target/package.json`
   - `/target/tsconfig.json`
   - `/target/src` folder structure
3. Summarize what you learn (e.g., "React + Next.js app using TypeScript").
4. Keep this in mind when generating code or patching files.

### Step 2: Load and Parse Hierarchical Plan
1. Read behavioral plan file
2. **Verify plan contains hierarchical task structure (1, 1.1, 1.2, 2, etc.)**
3. **Parse task dependency chain** - identify execution order
4. **Extract isolated contexts** - separate what each agent needs to know
5. Review critically - identify any questions about task dependencies or isolation
6. **Confirm each task has clear behavioral specifications and interface contracts**
7. If concerns: Raise them with your human partner before starting
8. If plan lacks hierarchical structure: Ask partner to revise using @collaboration/writing-plans
9. If no concerns: Create TodoWrite with hierarchical task list and proceed

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

### Step 4: Execute Tasks with Isolated Agents
**Execute tasks in dependency order (1, 1.1, 1.2, then 2, 2.1, etc.)**

For each task in execution order:
1. **Mark task as in_progress**
2. **Extract isolated context** for this specific task number
3. **Dispatch dedicated agent** with ONLY the context for this task:

```bash
# Dispatch task-specific agent
Task agent for Task X.Y:
  context: |
    You are implementing Task X.Y from the hierarchical plan.
    
    TASK SCOPE: [Only this specific task's behavior and context]
    ACCEPTANCE CRITERIA: [Only this task's criteria]
    FILES TO CREATE/MODIFY: [Only files for this task]
    DEPENDENCIES AVAILABLE: [Interfaces from completed tasks]
    INTERFACE TO PROVIDE: [What this task must expose for future tasks]
    
    MANDATORY: Use skills/testing/test-driven-development/SKILL.md
    
    You do NOT have access to:
    - Other task details from the plan
    - Full system architecture beyond your scope
    - Implementation details of other tasks
    
    Report back: Interface contracts implemented, tests passing, ready for dependent tasks
```

4. **Agent implements using TDD** (isolated to their task scope)
5. **Verify task completion** and interface contracts
6. **Update dependency chain** - mark interfaces available for dependent tasks
7. **Mark task as completed**

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

### Step 6: Report Hierarchical Progress
When batch complete:
- **Show task hierarchy status:** Which tasks completed (1 ✅, 1.1 ✅, 1.2 🔄, 2 ⏳)
- **Report agent isolation:** Each task agent worked with isolated context
- **Show interface contracts:** What each completed task provides to dependents
- **Report TDD compliance:** All task agents followed Test-Driven Development
- Show test results: All task tests passing, interface contracts verified
- **Next tasks ready:** Which tasks can now proceed (dependencies satisfied)
- Say: "Ready for feedback on hierarchical progress."

### Step 7: Manage Dependencies and Continue  
Based on feedback:
- Apply changes if needed to completed tasks
- **Check dependency chain:** Which tasks are now unblocked
- **Execute next batch** of ready tasks (dependencies satisfied)
- **Maintain interface contracts:** Ensure dependent tasks get what they need
- Repeat until complete hierarchy executed

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
- Review behavioral plan critically first
- **ALWAYS use @testing/test-driven-development for every task**
- Follow Red-Green-Refactor cycle religiously
- Don't skip test failures - they guide implementation
- Reference skills when plan says to (@testing/test-driven-development is mandatory)
- Between batches: report TDD compliance and test results
- Stop when blocked, don't guess or skip TDD steps
