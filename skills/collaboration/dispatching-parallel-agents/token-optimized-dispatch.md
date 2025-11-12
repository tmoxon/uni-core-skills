# Minimal Context Dispatch

Give agents the minimum context to understand their goal. They can ask for more.

## Pattern

```
TASK: [Clear goal statement]
ACCEPTANCE:
- [ ] Specific outcome 1
- [ ] Specific outcome 2

CONSTRAINTS: [If any]
REFERENCES: [Point to skills/docs, don't paste them]
```

## Why This Works

- Agents infer patterns from workspace structure
- Acceptance criteria define success, not implementation
- References available if needed
- Reduces token waste on context the agent won't use

## When to Add More Context

Add context when the agent actually needs it:
- Novel patterns without examples in the workspace
- Ambiguous requirements that need clarification
- Specific implementation approach required

Start minimal. Add incrementally if quality drops.
