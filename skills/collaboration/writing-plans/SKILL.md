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

## Hierarchical Task Granularity

**Main Tasks (1, 2, 3...):** High-level features or components (1-4 hours total)
- Represent complete user-facing features or architectural components
- Have clear dependencies on other main tasks
- Break down into focused subtasks

**Subtasks (1.1, 1.2, 1.3...):** Isolated behavioral units (15-45 minutes including TDD)
- Single behavioral responsibility 
- Isolated context - agent only sees this subtask
- Clear acceptance criteria and test scenarios
- Well-defined interfaces for task dependencies

**Agent Isolation Principle:**
- Each task number = one dedicated agent
- Agent gets only the context for their specific task
- No access to full plan or other task details
- Must define interfaces for other tasks to consume

## Plan Document Header

**Every plan MUST start with this header:**

```markdown
# [Feature Name] Implementation Plan

> **For Claude:** Use `${UNI_SKILLS_ROOT}/skills/collaboration/executing-plans/SKILL.md` to implement this plan task-by-task with hierarchical agent isolation.

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

**Task Execution Model:** Each task number (1, 1.1, 1.2, 2, etc.) will be assigned to a dedicated agent with isolated context.

---
```

## Task Hierarchy Planning

**Before writing tasks, create the hierarchy:**

1. **Identify main features/components** → Main tasks (1, 2, 3...)
2. **Break down into isolated behaviors** → Subtasks (1.1, 1.2, 1.3...)  
3. **Define dependency chains** → Which tasks need outputs from which other tasks
4. **Design interfaces** → How tasks communicate results to dependent tasks

**Dependency Rules:**
- Subtasks within a main task can depend on each other (1.1 → 1.2)
- Main tasks can depend on other main tasks (Task 2 depends on Task 1)
- No circular dependencies allowed
- Each task defines clear interface contracts

## Hierarchical Task Structure

Create a numbered hierarchy with main tasks (1, 2, 3) and subtasks (1.1, 1.2, 2.1, etc.):

```markdown
## Task 1: [Main Feature/Component Name]

**High-Level Behavior:** Overall goal of this main task

**Depends On:** [Other main tasks this requires: Task 2, Task 3]
**Provides To:** [Other main tasks that need this: Task 4, Task 5]

### Task 1.1: [Specific Sub-Behavior]

**Agent Context:** Everything this agent needs to know, isolated from other tasks

**Behavior:** Specific, focused behavior this subtask accomplishes

**Acceptance Criteria:**
- Given [context/input]
- When [action occurs] 
- Then [expected outcome]
- And [additional constraints]

**Test Scenarios:**
- Happy path: [normal case description]
- Edge cases: [boundary conditions, empty inputs, null values]
- Error cases: [invalid inputs, system failures]

**Agent-Specific Context:**
- Files to create: `exact/path/to/file.py`
- Tests to create: `tests/exact/path/to/test.py`
- Dependencies available: [interfaces from completed tasks]
- Interfaces to provide: [what other tasks will consume]

**Architecture Constraints:**
- Design patterns: [specific to this subtask]
- Performance requirements: [specific constraints]
- Security considerations: [validation, auth for this subtask]

**Definition of Done:**
- All acceptance criteria have passing tests
- Interfaces defined for dependent tasks
- Code follows project conventions
- Agent reports completion with interface contracts

### Task 1.2: [Another Sub-Behavior]

[Same structure as 1.1, isolated context]

## Task 2: [Next Main Feature/Component]

[Same structure as Task 1]
```

**Key Principles:**
- Each task number gets a dedicated agent
- Each agent gets ONLY the context they need
- No agent sees the full plan - only their task scope
- Clear dependency chains between tasks

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

**"Hierarchical behavioral plan complete and saved to `docs/plans/<filename>.md`. Each task number will get a dedicated agent with isolated context. Implementation will follow strict Test-Driven Development. Two execution options:**

**1. Subagent-Driven (this session)** - I dispatch isolated TDD-focused agent per task number, ensure hierarchical execution with dependency management

**2. Parallel Session (separate)** - Open new session with executing-plans skill, enforces hierarchical TDD methodology with agent isolation

**Which approach?"**

**If Subagent-Driven chosen:**
- Use skills/collaboration/subagent-driven-development
- Stay in this session
- Each task agent gets ONLY their isolated context
- Each agent MUST follow @testing/test-driven-development
- Code review includes TDD compliance and interface contract verification

**If Parallel Session chosen:**
- Guide them to work in the `/target` repository  
- New session uses skills/collaboration/executing-plans
- Executing-plans will dispatch isolated agents per task with TDD enforcement
