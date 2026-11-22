---
name: safe-production-changes
description: >-
  Guardrails for state-changing work on live systems. Use before running
  scripts, deployments, bulk edits, deletions, rollbacks or status transitions
  against production or shared resources such as tickets, cloud resources and
  directory groups. Covers dry runs, snapshots, environment parity and not
  touching resources you did not create.
---

# Safe Production Changes

Anything with a side effect on real data needs a preview, a snapshot and a named target.

## Rules

1. **Name the environment.** State which environments a change applies to (for example test and production) and which one you actually changed. When asked to "deploy", deploy to every environment requested, or say plainly which were skipped.
2. **Never test on real data.** Do not run rollbacks, status transitions, deletes or write tests against real tickets, groups or resources. Use test objects or mocks.
3. **Dry run first.** Give every script that mutates state a `--dry-run` that prints each change it would make. Show the output before the real run.
4. **Snapshot before bulk changes.** Save the full current state of every target (fields, status, links) to a timestamped JSON file before writing. Rollback then means replaying the snapshot, and anything that cannot be restored is flagged up front.
5. **Canary, then full run.** Apply to one chosen target, pause for confirmation, then run the rest.
6. **Do not delete what you did not create.** If cleanup is needed, list candidates and ask. A permission block on a delete is a signal to stop, not to find another route.
7. **Confirm destructive or irreversible steps** unless the user already authorised that exact action.
8. **Produce an audit report** (for example a spreadsheet) of what changed, with before and after values.

## Before You Start

State in one short block: targets, environments, how you will verify, how to undo. Wait for approval on anything beyond read-only work.

## Related

- `safe-repo-operations` covers risky git and GitHub operations (force-push, history rewrites, renames). This skill covers state-changing work on live systems such as tickets, cloud resources and directory groups.
- `verify-live-state` for confirming the result of a change against live data.
