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

**Task Decomposition Intelligence:** Determine subtask quantity based on complexity - simple features may need 1 subtask, complex features may need 5+ subtasks. Do not force rigid numbering patterns.

**Announce at start:** "I'm using the Writing Plans skill to create the implementation plan."

**Context:** This should be run in the `/target` repository (set up by brainstorming skill).

**Plan Organization:** Create folder structure `docs/plans/YYYY-MM-DD-<feature-name>/`
- `plan.md` - Main orchestration file with task overview and execution order
- `task1.md`, `task2.md`, etc. - Individual task files for each main task
- `task1.1.md`, `task1.2.md`, etc. - Subtask files (create as many as needed per main task)

## Hierarchical Task Granularity

**Main Tasks (1, 2, 3...):** High-level features or components (1-4 hours total)
- Represent complete user-facing features or architectural components
- Have clear dependencies on other main tasks
- Break down into focused subtasks

**Subtasks (1.1, 1.2, 1.3... as many as needed):** Isolated behavioral units (15-45 minutes including TDD)
- Single behavioral responsibility 
- Isolated context - agent only sees this subtask
- Clear acceptance criteria and test scenarios
- Well-defined interfaces for task dependencies
- **Determine quantity based on complexity** - simple tasks may have 1 subtask, complex tasks may have 5+

**Agent Isolation Principle:**
- Each task number = one dedicated agent
- Agent gets only the context for their specific task
- No access to full plan or other task details
- Must define interfaces for other tasks to consume

## Plan Structure

### Main Orchestration File (`plan.md`)

**Every plan.md MUST start with this header:**

```markdown
# [Feature Name] Implementation Plan

> **For Claude:** Use `${UNI_SKILLS_ROOT}/skills/collaboration/executing-plans/SKILL.md` to implement this plan task-by-task with hierarchical agent isolation.

**Goal:** [One sentence describing what this builds]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

**Task Execution Model:** Each task number will be assigned to a dedicated agent with isolated context.

## Task Overview

Brief description of each main task and execution order:

### Task 1: [Main Task Name] 
- **File:** `task1.md`
- **Purpose:** [Brief description]
- **Depends On:** [Dependencies]
- **Subtasks:** [Determine based on complexity] (files: `task1.1.md`, `task1.2.md`, etc. as needed)

### Task 2: [Main Task Name]
- **File:** `task2.md` 
- **Purpose:** [Brief description]
- **Depends On:** Task 1
- **Subtasks:** [Determine based on complexity] (files: `task2.1.md`, etc. as needed)

## Execution Order

1. Task 1 → subtasks in dependency order
2. Task 2 → subtasks in dependency order  
3. [Continue based on dependencies]

**Note:** Number of subtasks determined by complexity - simple main task may need only 1 subtask, complex main task may need 5+ subtasks.

---
```

### Individual Task Files (`task1.md`, `task2.md`, etc.)

**Each main task file structure:**

```markdown
# Task 1: [Main Task Name]

**High-Level Behavior:** Overall goal of this main task

**Depends On:** [Other main tasks this requires]
**Provides To:** [Other main tasks that need this]

**Subtasks:**
[Analyze main task complexity and create appropriate number of subtasks]
- Example for simple task: 1 subtask (1.1)
- Example for moderate task: 3 subtasks (1.1, 1.2, 1.3)  
- Example for complex task: 5+ subtasks (1.1, 1.2, 1.3, 1.4, 1.5, etc.)
- Create only as many subtasks as the complexity demands

**Execution Flow:** [Define dependency order based on actual subtasks created]
```

### Individual Subtask Files (`task1.1.md`, `task1.2.md`, etc. as needed)

**Each subtask file structure:**

## Task Hierarchy Planning

**Before writing tasks, create the hierarchy:**

1. **Identify main features/components** → Main tasks (1, 2, 3...)
2. **Analyze complexity per main task** → Determine subtask quantity needed
3. **Break down into isolated behaviors** → Subtasks (as many as needed: simple task = 1 subtask, complex task = 5+ subtasks)
4. **Define dependency chains** → Which tasks need outputs from which other tasks
5. **Design interfaces** → How tasks communicate results to dependent tasks

## Intelligent Task Decomposition

**For each main task, ask:**
- Can this be implemented by one agent in 15-45 minutes with TDD? → **1 subtask**
- Are there 2-3 distinct behavioral concerns? → **2-3 subtasks**
- Complex multi-step process with many edge cases? → **4+ subtasks**
- Massive feature with multiple integration points? → **5+ subtasks**

**Examples:**
- Simple CRUD endpoint → 1 subtask
- User authentication → 3 subtasks (validation, token generation, middleware)
- Complex search feature → 5 subtasks (indexing, query parsing, ranking, filtering, pagination)
- E-commerce checkout → 7+ subtasks (cart validation, payment processing, inventory updates, email notifications, etc.)

**Dependency Rules:**
- Subtasks within a main task can depend on each other (based on actual decomposition)
- Main tasks can depend on other main tasks (Task 2 depends on Task 1) 
- No circular dependencies allowed
- Each task defines clear interface contracts

```markdown
# Task 1.1: [Specific Sub-Behavior]

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
```

## File Creation Process

1. **Create the plan folder:** `docs/plans/YYYY-MM-DD-<feature-name>/`
2. **Create plan.md** with orchestration overview
3. **Create task files** (task1.md, task2.md) for each main task
4. **Create subtask files** (as many as needed based on complexity analysis)
5. **Ensure consistent numbering** across all files

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

After saving all plan files, offer execution choice:

**"Hierarchical behavioral plan complete and saved to `docs/plans/<folder-name>/` with:**
- **`plan.md`** - Main orchestration file
- **`task1.md`, `task2.md`** - Individual task files  
- **Subtask files** - Individual files for each subtask (quantity determined by complexity)

**Each task number will get a dedicated agent with isolated context. Implementation will follow strict Test-Driven Development. Two execution options:**

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
