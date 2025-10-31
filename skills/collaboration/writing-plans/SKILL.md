---
name: Writing Plans
description: Create detailed implementation plans with bite-sized tasks for engineers with zero codebase context
when_to_use: when design is complete and you need detailed implementation tasks for engineers with zero codebase context
version: 2.1.0
---

# Writing Plans

## Overview

Write comprehensive implementation plans focused on **behavioral specifications** and **abstract syntax** rather than concrete code. Define WHAT needs to be implemented through clear test scenarios, expected behaviors, and architectural patterns. The implementation will follow strict TDD methodology during execution.

Plans should specify **desired behaviors**, **test scenarios**, **architectural constraints**, and **integration points** - but NOT the specific implementation code.

Assume the implementing engineer will follow Test-Driven Development and needs clear behavioral requirements to write meaningful tests.

**Announce at start:** "I'm using the Writing Plans skill to create the implementation plan."

**Context:** This should be run in the `/target` repository (set up by brainstorming skill).

**Save plans to:** `docs/plans/YYYY-MM-DD-<feature-name>.md`

## Bite-Sized Task Granularity

**Each task defines one behavioral unit (10-30 minutes including TDD cycle):**
- Clear behavior description with acceptance criteria
- Expected test scenarios (inputs/outputs, edge cases)
- Integration requirements and constraints
- Definition of "done" (what tests should pass)

**TDD execution will break each task into Red-Green-Refactor cycles during implementation.**

## Plan Document Header

**Every plan MUST start with this header:**

```markdown
# [Feature Name] Implementation Plan

> **For Claude:** Use `${UNI_SKILLS_ROOT}/skills/collaboration/executing-plans/SKILL.md` to implement this plan task-by-task.

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

---
```

## Task Structure

```markdown
### Task N: [Behavior/Component Name]

**Behavior:** Clear description of what this component should do

**Acceptance Criteria:**
- Given [context/input]
- When [action occurs]
- Then [expected outcome]
- And [additional constraints]

**Test Scenarios:**
- Happy path: [normal case description]
- Edge cases: [boundary conditions, empty inputs, null values]
- Error cases: [invalid inputs, system failures]

**Integration Points:**
- Depends on: [other components/services this needs]
- Provides: [interface/API this exposes]
- Side effects: [database changes, file operations, network calls]

**Files to Create/Modify:**
- Implementation: `exact/path/to/file.py` 
- Tests: `tests/exact/path/to/test.py`
- Integration: `exact/path/to/integration_point.py`

**Architecture Notes:**
- Design patterns to use: [e.g., Factory, Observer, Strategy]
- Performance constraints: [response time, memory usage]
- Security considerations: [input validation, authorization]

**Definition of Done:**
- All acceptance criteria have passing tests
- All edge cases covered with tests
- Integration points tested
- Code follows project conventions
```

## Remember

- **Behavior-focused:** Describe WHAT, not HOW
- **Test scenarios:** Clear inputs/outputs for TDD implementation
- **Exact file paths:** Where implementation and tests should go
- **Architectural constraints:** Design patterns, performance, security
- **Integration points:** Dependencies and interfaces
- **No concrete code:** Let TDD drive the implementation
- **Reference relevant skills:** Use @ syntax for skills like @testing/test-driven-development

## Execution Handoff

After saving the plan, offer execution choice:

**"Behavioral plan complete and saved to `docs/plans/<filename>.md`. Implementation will follow strict Test-Driven Development. Two execution options:**

**1. Subagent-Driven (this session)** - I dispatch TDD-focused subagent per task, ensure Red-Green-Refactor cycle compliance

**2. Parallel Session (separate)** - Open new session with executing-plans skill, enforces TDD methodology with batch execution

**Which approach?"**

**If Subagent-Driven chosen:**
- Use skills/collaboration/subagent-driven-development
- Stay in this session
- Each subagent MUST follow @testing/test-driven-development
- Code review includes TDD compliance verification

**If Parallel Session chosen:**
- Guide them to work in the `/target` repository  
- New session uses skills/collaboration/executing-plans
- Executing-plans will enforce @testing/test-driven-development for each task
