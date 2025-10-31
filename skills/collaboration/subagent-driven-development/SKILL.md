---
name: Subagent-Driven Development
description: Execute behavioral plans using TDD-focused subagents for each task, with code review verifying TDD compliance
when_to_use: when executing behavioral implementation plans with independent tasks in the current session, enforcing Test-Driven Development
version: 2.0.0
---

# Subagent-Driven Development

Execute behavioral plans by dispatching TDD-focused subagent per task, with code review verifying TDD compliance after each.

**Core principle:** Fresh TDD-compliant subagent per task + TDD compliance review = high quality, test-driven iteration

## Overview

**vs. Executing Plans (parallel session):**
- Same session (no context switch)
- Fresh TDD-focused subagent per task (no context pollution)
- TDD compliance review after each task (catch violations early)
- Faster iteration with test-driven quality assurance

**When to use:**
- Staying in this session  
- Tasks are mostly independent behavioral requirements
- Want continuous TDD-driven progress with quality gates
- Behavioral plan exists (not concrete code plan)

**When NOT to use:**
- Plan contains concrete code instead of behavioral specs (rewrite with @collaboration/writing-plans first)
- Tasks are tightly coupled (manual TDD execution better)
- Plan needs behavioral revision (brainstorm first)

## The Process

### 1. Load Plan

Read plan file, create TodoWrite with all tasks.

### 2. Execute Task with Subagent

For each task:

**Dispatch fresh subagent:**
```
Task tool (general-purpose):
  description: "Implement Task N: [task name] using Test-Driven Development"
  prompt: |
    You are implementing Task N from [plan-file] using STRICT Test-Driven Development.

    MANDATORY: Use skills/testing/test-driven-development/SKILL.md for every implementation.

    Read the behavioral task specification carefully. Your job is to:
    1. **Follow TDD Red-Green-Refactor cycle for every behavioral requirement**
    2. **RED:** Write failing test based on acceptance criteria
    3. **GREEN:** Write minimal code to pass the test
    4. **REFACTOR:** Clean up while keeping tests green
    5. Commit after each complete Red-Green-Refactor cycle
    6. Verify all acceptance criteria have passing tests
    7. Report back with TDD compliance details

    Work from: [directory]

    Report: What behaviors you implemented, TDD cycles completed, test results, files changed, TDD compliance verification
```

**Subagent reports back** with summary of work.

### 3. Review Subagent's Work

**Dispatch code-reviewer subagent:**
```
Task tool (code-reviewer):
  Use template at skills/collaboration/requesting-code-review/code-reviewer.md

  WHAT_WAS_IMPLEMENTED: [from subagent's report]
  PLAN_OR_REQUIREMENTS: Task N from [plan-file]
  BASE_SHA: [commit before task]
  HEAD_SHA: [current commit]
  DESCRIPTION: [task summary]
  TDD_COMPLIANCE: Verify Red-Green-Refactor cycles followed, tests written first, behavioral requirements covered
```

**Code reviewer returns:** Strengths, Issues (Critical/Important/Minor), TDD Compliance Assessment

### 4. Apply Review Feedback

**If issues found:**
- Fix Critical issues immediately (including TDD violations)
- Fix Important issues before next task
- **TDD Compliance Issues:** Must be fixed immediately - no exceptions
- Note Minor issues

**Dispatch follow-up subagent if needed (must follow TDD):**
```
"Fix issues from code review: [list issues]"
```

### 5. Mark Complete, Next Task

- Mark task as completed in TodoWrite
- Move to next task
- Repeat steps 2-5

### 6. Final Review

After all tasks complete, dispatch final code-reviewer:
- Reviews entire implementation
- Checks all plan requirements met
- Validates overall architecture

### 7. Complete Development

After final review passes:
- Announce: "I'm using the Finishing a Development Branch skill to complete this work."
- Switch to skills/collaboration/finishing-a-development-branch
- Follow that skill to verify tests, present options, execute choice

## Example Workflow

```
You: I'm using Subagent-Driven Development to execute this plan.

[Load plan, create TodoWrite]

Task 1: Hook installation script

[Dispatch implementation subagent]
Subagent: Implemented install-hook with tests, 5/5 passing

[Get git SHAs, dispatch code-reviewer]
Reviewer: Strengths: Good test coverage. Issues: None. Ready.

[Mark Task 1 complete]

Task 2: Recovery modes

[Dispatch implementation subagent]
Subagent: Added verify/repair, 8/8 tests passing

[Dispatch code-reviewer]
Reviewer: Strengths: Solid. Issues (Important): Missing progress reporting

[Dispatch fix subagent]
Fix subagent: Added progress every 100 conversations

[Verify fix, mark Task 2 complete]

...

[After all tasks]
[Dispatch final code-reviewer]
Final reviewer: All requirements met, ready to merge

Done!
```

## Advantages

**vs. Manual execution:**
- Subagents follow TDD naturally
- Fresh context per task (no confusion)
- Parallel-safe (subagents don't interfere)

**vs. Executing Plans:**
- Same session (no handoff)
- Continuous progress (no waiting)
- Review checkpoints automatic

**Cost:**
- More subagent invocations
- But catches issues early (cheaper than debugging later)

## Red Flags

**Never:**
- Skip code review between tasks
- Proceed with unfixed Critical issues
- Dispatch multiple implementation subagents in parallel (conflicts)
- Implement without reading plan task

**If subagent fails task:**
- Dispatch fix subagent with specific instructions
- Don't try to fix manually (context pollution)

## Integration

**Pairs with:**
- skills/collaboration/writing-plans (creates the plan)
- skills/collaboration/requesting-code-review (review template)
- skills/testing/test-driven-development (subagents follow this)

**Alternative to:**
- skills/collaboration/executing-plans (parallel session)

See code-reviewer template: skills/collaboration/requesting-code-review/code-reviewer.md
