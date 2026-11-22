---
name: plan-before-building
description: >-
  Lock scope before implementing. Use when a task has several steps, touches
  live systems, or the requirements are still moving, for example automation
  jobs, scripts and multi-page changes. Produces a short written plan with
  scope, out-of-scope items, target environments and acceptance checks, then
  waits for approval.
---

# Plan Before Building

Most rework comes from requirements that shift after code exists. A five-line plan agreed first is cheaper than three redesigns.

## Plan Template

```
Goal:            one sentence
In scope:        bullets
Out of scope:    bullets (anything the user dropped earlier belongs here)
Environments:    where it will run and be deployed
Style rules:     naming, formatting, content rules that must hold
Verification:    how you will prove it works, with evidence
Undo:            how to revert
```

## Steps

1. Write the plan from what the user said. Do not invent requirements.
2. Ask only the questions that change the plan. Offer a default for each.
3. Wait for approval, then implement exactly that scope.
4. When requirements change mid-task, update the plan in a few lines and confirm before continuing. Record what was dropped so it does not creep back.
5. Finish by checking each Verification item and reporting the evidence.

## Skip When

The task is a single read-only lookup or a one-line edit.

## Related

- `verify-before-delivery` for the checks to run against the plan's Verification items.
- `safe-production-changes` when the plan touches live systems.
