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
- Hierarchical plan exists with task numbers (1, 1.1, 1.2, 2, etc.)
- Want agent isolation per task with TDD compliance
- Tasks have clear dependency chains and interface contracts
- Behavioral specifications exist (not concrete code plan)

**When NOT to use:**
- Plan lacks hierarchical task structure (rewrite with @collaboration/writing-plans first)
- Plan contains concrete code instead of behavioral specs
- Task dependencies are unclear or circular
- Plan needs behavioral revision (brainstorm first)

## The Process

### 1. Load Hierarchical Plan

Read plan file, parse task hierarchy (1, 1.1, 1.2, 2, etc.), create TodoWrite with dependency-ordered execution.

### 2. Execute Task with Subagent

For each task:

**Dispatch task-isolated subagent:**
```
Task tool (task-isolated):
  description: "Implement Task X.Y: [specific task name] with isolated context and TDD"
  prompt: |
    You are implementing ONLY Task X.Y from a hierarchical plan using STRICT Test-Driven Development.
    
    ISOLATED CONTEXT - You only see:
    ================================
    TASK NUMBER: X.Y
    BEHAVIOR: [Only this task's behavioral requirement]
    ACCEPTANCE CRITERIA: [Only this task's criteria] 
    FILES TO CREATE: [Only files for this task]
    DEPENDENCIES AVAILABLE: [Interfaces from completed tasks]
    INTERFACE TO PROVIDE: [What you must expose for future tasks]
    
    MANDATORY: Use skills/testing/test-driven-development/SKILL.md
    
    RESTRICTIONS - You do NOT have access to:
    ========================================
    - Other task details from the plan
    - Full system architecture beyond your scope  
    - Implementation details of other tasks
    - The complete plan structure
    
    Your job using TDD:
    1. **Follow Red-Green-Refactor for each acceptance criteria**
    2. **Create interface contracts** for dependent tasks
    3. **Verify all acceptance criteria** with passing tests
    4. Commit after each TDD cycle
    
    Work from: [directory]
    
    Report: Task X.Y complete, interface contracts defined, TDD cycles completed, tests passing
```

**Subagent reports back** with summary of work.

### 3. Review Subagent's Work

**Dispatch code-reviewer subagent:**
```
Task tool (code-reviewer):
  Use template at skills/collaboration/requesting-code-review/code-reviewer.md

  WHAT_WAS_IMPLEMENTED: [from isolated subagent's report]
  PLAN_OR_REQUIREMENTS: Task X.Y isolated requirements only
  BASE_SHA: [commit before task]
  HEAD_SHA: [current commit]
  DESCRIPTION: [task X.Y summary]
  TDD_COMPLIANCE: Verify Red-Green-Refactor cycles, tests first, acceptance criteria covered
  INTERFACE_CONTRACTS: Verify interfaces defined for dependent tasks
  ISOLATION_COMPLIANCE: Verify agent only implemented assigned task scope
```

**Code reviewer returns:** Strengths, Issues (Critical/Important/Minor), TDD Compliance, Interface Contract Verification

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
