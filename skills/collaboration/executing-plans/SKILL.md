---
name: Executing Plans
description: Execute detailed plans with parallel task dispatch for independent tasks and review checkpoints
when_to_use: when partner provides a complete implementation plan to execute with parallel optimization for independent tasks and review checkpoints
version: 2.3.0
---

# Executing Plans

## Context

- You are running inside a Docker container.
- `/workspace` is your own code and logic.
- `/target` is the actual target repository where code should be written.
- Always treat `/target` as the main project.

## Overview

Load behavioral plan, analyze dependency graph, execute independent tasks in parallel using strict Test-Driven Development methodology, report for review between batches.

**Core principle:** TDD-driven execution with parallel dispatch for independent tasks and checkpoints for architect review.

**Every task MUST follow Red-Green-Refactor cycle using @testing/test-driven-development skill.**

**Parallel execution:** Independent main tasks execute concurrently using @collaboration/dispatching-parallel-agents pattern.

**Announce at start:** "I'm using the Executing Plans skill to implement this plan with Test-Driven Development and parallel dispatch for independent tasks."

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
1. **Check plan structure** - Look for either:
   - Single plan file: `docs/plans/YYYY-MM-DD-<name>.md` (legacy)
   - Multi-file plan: `docs/plans/YYYY-MM-DD-<name>/plan.md` (preferred)
2. **Verify plan is committed to git:**
   ```bash
   cd /target
   git status docs/plans/
   ```
   - **If plan files show as untracked or modified:** 
     - **STOP execution immediately**
     - Tell partner: "⚠️ The implementation plan in `docs/plans/<folder-name>/` is not committed to git. Please commit it first using: `git add docs/plans/<folder-name>/ && git commit -m 'docs: Add implementation plan for <feature-name>'`"
     - **Wait for partner to commit before proceeding**
   - **If plan is committed:** Proceed to next step
3. **If multi-file plan:**
   - Read `plan.md` for orchestration overview and execution order
   - Identify all task files (`task1.md`, `task2.md`) and subtask files (variable quantity based on plan complexity)
   - Parse dependency chain from task overview
4. **If single plan file:** Parse hierarchical structure from single file
5. **Verify plan contains hierarchical task structure (main tasks and subtasks as determined by complexity)**
6. **Build dependency graph** - map which tasks depend on which other tasks
7. **Identify parallel execution opportunities:**
7. **Identify parallel execution opportunities:**
   - **Independent main tasks:** Tasks with no dependencies between them (Task 1, Task 3 both independent)
   - **Independent subtask clusters:** Subtasks within a main task that can run concurrently
   - **Sequential chains:** Tasks that must run in order due to dependencies
8. **Extract isolated contexts** - separate what each agent needs to know
9. Review critically - identify any questions about task dependencies or isolation
10. **Confirm each task has clear behavioral specifications and interface contracts**
11. If concerns: Raise them with your human partner before starting
12. If plan lacks hierarchical structure: Ask partner to revise using @collaboration/writing-plans
13. If no concerns: Create TodoWrite with hierarchical task list and parallel execution strategy

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

### Step 4: Execute Tasks with Parallel Dispatch
**Execute tasks using dependency-aware parallel dispatch**

#### 4.1 Analyze Execution Strategy
1. **Group tasks by dependency level:**
   - **Level 0:** Independent main tasks with no dependencies
   - **Level 1:** Tasks that depend only on Level 0 tasks
   - **Level N:** Tasks that depend on Level N-1 tasks
2. **Identify parallel opportunities within each level:**
   - Multiple independent main tasks → **Parallel main task dispatch**
   - Multiple independent subtasks within a main task → **Parallel subtask dispatch**
3. **Choose execution approach:**
   - **Sequential:** Dependencies require strict ordering
   - **Parallel:** Independent tasks can run concurrently
   - **Hybrid:** Some levels parallel, some sequential

#### 4.2 Dispatch Execution Strategy

**For Independent Tasks (Parallel Dispatch):**
```typescript
// When multiple main tasks have no dependencies
Task("Execute Task 1 (independent)", task1_context)
Task("Execute Task 2 (independent)", task2_context) 
Task("Execute Task 3 (independent)", task3_context)
// All run concurrently using @collaboration/dispatching-parallel-agents pattern
```

**For Each Agent in Parallel Batch:**
1. **Extract isolated context** from individual task file
2. **Dispatch dedicated agent** with ONLY the context for this task:

```bash
Task agent for Task X:
  task_file_content: [Full content from taskX.md and all subtasks]
  context: |
    You are implementing Task X from the hierarchical plan (PARALLEL EXECUTION).
    
    TASK SCOPE: [Only this specific task's behavior and context]
    ACCEPTANCE CRITERIA: [Only this task's criteria]  
    FILES TO CREATE/MODIFY: [Only files for this task]
    DEPENDENCIES AVAILABLE: [Interfaces from completed dependency levels]
    INTERFACE TO PROVIDE: [What this task must expose for future tasks]
    
    PARALLEL CONSTRAINTS:
    - Do NOT modify files other tasks might edit
    - Stick to your designated file/component scope
    - Use clearly defined interface contracts
    - Avoid shared state modifications
    
    MANDATORY: Use skills/testing/test-driven-development/SKILL.md
    
    You do NOT have access to:
    - Other task details from the plan
    - Full system architecture beyond your scope
    - Implementation details of parallel tasks
    
    Report back: Interface contracts implemented, tests passing, no conflicts with parallel tasks
```

**For Dependent Tasks (Sequential after parallel batch):**
- Wait for all parallel tasks in current level to complete
- Verify interface contracts are available
- Execute next dependency level (may also be parallel within that level)

#### 4.3 Parallel Execution Management
1. **Mark all parallel tasks as in_progress**
2. **Dispatch all parallel agents simultaneously** 
3. **Monitor parallel execution** - agents work independently
4. **Collect results** from all parallel agents
5. **Verify no conflicts:**
   - Check for file modification conflicts
   - Verify interface contracts match expectations
   - Ensure tests pass independently
6. **Mark all parallel tasks as completed** when verified
7. **Proceed to next dependency level**

#### 4.4 Integration and Verification
After each parallel batch:
1. **Run integration tests** - verify parallel implementations work together
2. **Check interface contracts** - ensure dependent tasks can proceed
3. **Resolve any conflicts** - if agents modified overlapping areas
4. **Update dependency status** - mark interfaces available for next level

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

### Step 6: Report Parallel Progress
When parallel batch complete:
- **Show execution strategy used:** Sequential, Parallel, or Hybrid approach
- **Report parallel batch results:** Which tasks executed concurrently and outcomes
- **Show task hierarchy status:** Which tasks completed (based on actual plan structure)
- **Report agent isolation:** Each task agent worked with isolated context without interference
- **Show interface contracts:** What each completed task provides to dependents
- **Report TDD compliance:** All task agents followed Test-Driven Development
- **Integration verification:** All parallel implementations work together, no conflicts
- Show test results: All task tests passing, interface contracts verified, integration tests green
- **Next tasks ready:** Which tasks can now proceed (dependencies satisfied)
- **Performance metrics:** Time saved through parallel execution vs sequential
- Say: "Ready for feedback on parallel execution progress."

### Step 7: Manage Dependencies and Continue Parallel Execution
Based on feedback:
- Apply changes if needed to completed tasks
- **Analyze dependency graph:** Which tasks are now unblocked by completed parallel batch
- **Identify next parallel opportunities:** Group newly available tasks by independence
- **Execute next parallel batch** of ready tasks (dependencies satisfied)
- **Maintain interface contracts:** Ensure dependent tasks get what they need
- **Optimize execution strategy:** Choose parallel vs sequential based on current dependency level
- Repeat until complete hierarchy executed with maximum parallelization

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

## Parallel vs Sequential Decision Making

### Use Parallel Execution When:
- **Independent main tasks:** Multiple tasks with no dependencies (Task 1, Task 3 both standalone)
- **Separate architectural domains:** Frontend + Backend + Database tasks
- **Non-overlapping file scope:** Tasks modify completely different files/components
- **Clear interface contracts:** Well-defined APIs between tasks
- **No shared state:** Tasks don't modify same configuration, globals, or resources

### Use Sequential Execution When:
- **Strong dependencies:** Task 2 requires Task 1's implementation details
- **Shared file modifications:** Multiple tasks need to edit same files
- **Unclear interfaces:** Ambiguous contracts between tasks  
- **Complex integration:** Tasks need tight coupling or coordination
- **Small plan:** Overhead of parallel dispatch exceeds benefits

### Hybrid Approach (Recommended):
- **Parallel within dependency levels:** Execute independent tasks at same level concurrently
- **Sequential between levels:** Wait for dependency level to complete before proceeding
- **Opportunistic parallelization:** Look for parallel opportunities at each level

### Cost-Benefit Analysis:
**Benefits of Parallel:**
- Faster execution for independent tasks
- Better resource utilization
- Isolated failure domains

**Costs of Parallel:**
- 20-40% more tokens (each agent needs full context)
- Integration complexity increases
- Harder to debug conflicts
- API rate limits may serialize anyway

**Decision Rule:** Use parallel when tasks are truly independent and plan complexity justifies overhead.

### Example: E-commerce Checkout Plan

**Dependency Analysis:**
```
Level 0 (Independent - PARALLEL):
├── Task 1: User Authentication (auth service, middleware)  
├── Task 2: Product Catalog (database, API endpoints)
└── Task 3: Payment Gateway (external service integration)

Level 1 (Depends on Level 0 - PARALLEL within level):
├── Task 4: Shopping Cart (needs Task 1: auth, Task 2: products)
└── Task 5: Email Service (needs Task 1: auth for user data)

Level 2 (Depends on Level 1 - SEQUENTIAL):
└── Task 6: Checkout Flow (needs Task 4: cart, Task 5: email, Task 3: payment)
```

**Execution Strategy:**
1. **Parallel Batch 1:** Dispatch Tasks 1, 2, 3 simultaneously (30 min → ~35 min total)
2. **Integration Check:** Verify auth, catalog, and payment interfaces work
3. **Parallel Batch 2:** Dispatch Tasks 4, 5 simultaneously (20 min → ~25 min total) 
4. **Final Integration:** Task 6 sequentially (15 min)

**Time Savings:** 85 minutes vs 120 minutes sequential = 35 minute speedup (29% faster)

## When to Stop and Ask for Help

**STOP executing immediately when:**
- Hit a blocker mid-batch (missing dependency, test fails, instruction unclear)
- Plan has critical gaps preventing starting
- You don't understand an instruction
- Verification fails repeatedly
- **Parallel agents conflict:** Multiple agents modify same files
- **Integration failures:** Parallel implementations don't work together

**Ask for clarification rather than guessing.**

## When to Revisit Earlier Steps

**Return to Review (Step 1) when:**
- Partner updates the plan based on your feedback
- Fundamental approach needs rethinking

**Don't force through blockers** - stop and ask.

## Remember
- Review behavioral plan critically first
- **Build dependency graph** and identify parallel opportunities
- **ALWAYS use @testing/test-driven-development for every task**
- Follow Red-Green-Refactor cycle religiously
- Don't skip test failures - they guide implementation
- **Use @collaboration/dispatching-parallel-agents** pattern for independent tasks
- **Verify no conflicts** between parallel agent implementations
- **Run integration tests** after parallel batches
- Reference skills when plan says to (@testing/test-driven-development is mandatory)
- Between batches: report TDD compliance, parallel execution results, and integration status
- Stop when blocked, don't guess or skip TDD steps
- **Choose parallel vs sequential** based on task independence and cost-benefit analysis
