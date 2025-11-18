---
name: Brainstorming Ideas Into Designs
description: Interactive idea refinement using Socratic method to develop fully-formed designs
when_to_use: when partner describes any feature or project idea, before writing code or implementation plans
version: 2.2.0
---

# Brainstorming Ideas Into Designs

## Overview

Transform rough ideas into fully-formed designs through structured questioning and alternative exploration.

**Core principle:** Ask questions to understand, explore alternatives, present design incrementally for validation.

**Announce at start:** "I'm using the Brainstorming skill to refine your idea into a design."

## The Process

### Phase 1: Understanding
- Check current project state in working directory
- Ask ONE question at a time to refine the idea
- Prefer multiple choice when possible
- Gather: Purpose, constraints, success criteria

### Phase 2: Exploration
- Propose 2-3 different approaches
- For each: Core architecture, trade-offs, complexity assessment
- Ask your human partner which approach resonates

### Phase 3: Design Presentation
- Present in 200-300 word sections
- Cover: Architecture, components, data flow, error handling, testing
- Ask after each section: "Does this look right so far?"

### Phase 3.5: ADR Evaluation (when design is approved)
When design is validated and approved, evaluate if an ADR is needed:

**Ask yourself: Does this design meet ADR criteria?**
- Public API contracts changed or added?
- Database schemas changed in a way that impacts other consumers?
- New caches (local/in-memory) that affect system state?
- New infrastructure or services required?
- PII stored in a new way?
- Significant changes to project structure?
- Breaking changes to public APIs?
- **Deliberate deviation from best practices or anti-patterns used?**
- Architectural constraints that need documentation?

**If ANY answer is YES:**
1. Announce: "I'm going to create an ADR to cover the key decisions made as part of this plan"
2. Switch to skills/collaboration/create-adr
3. Create ADR(s) documenting the decisions
4. Commit ADR(s) to git:
   ```bash
   git add docs/adr/
   git commit -m "docs: Add ADR for [decision topic]"
   ```
5. Return to Phase 4

**Trigger signals that indicate ADR needed:**
- User chose "specific reason" for unconventional approach
- Design deliberately violates standard practices
- Architectural trade-offs made between competing concerns
- Technical constraints drive unusual design choices

### Phase 4: Target Repository Setup (for implementation)
When design is approved and implementation will follow:
- Announce: "I'm setting up the target repository for implementation."
- Switch to skills/collaboration/setting-up-target-repository
- Follow that skill's process for branch creation and project setup
- Return here when setup complete

### Phase 5: Planning Handoff
Ask: "Ready to create the implementation plan?"

When your human partner confirms (any affirmative response):
- Announce: "I'm using the Writing Plans skill to create the implementation plan."
- Switch to skills/collaboration/writing-plans skill
- Create detailed plan in the `/target` repository

## When to Revisit Earlier Phases

**You can and should go backward when:**
- Partner reveals new constraint during Phase 2 or 3 → Return to Phase 1 to understand it
- Validation shows fundamental gap in requirements → Return to Phase 1
- Partner questions approach during Phase 3 → Return to Phase 2 to explore alternatives
- Something doesn't make sense → Go back and clarify

**Don't force forward linearly** when going backward would give better results.

## Related Skills

**During exploration:**
- When approaches have genuine trade-offs: skills/architecture/preserving-productive-tensions

**Before proposing changes to existing code:**
- Understand why it exists: skills/research/tracing-knowledge-lineages

## Remember
- One question per message during Phase 1
- Apply YAGNI ruthlessly
- Explore 2-3 alternatives before settling
- Present incrementally, validate as you go
- Go backward when needed - flexibility > rigid progression
- Announce skill usage at start
