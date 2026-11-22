---
name: safe-changes
description: >-
  Guardrails for any state-changing or destructive work: scripts, deployments, bulk edits,
  deletions, rollbacks and status transitions against production or shared resources such as
  tickets, cloud resources and directory groups, and risky git and GitHub operations such as
  pushing to a public repo, archiving, resetting to upstream, rewriting history, force-pushing,
  deleting branches or forks, renaming a repo, and moving content between public and private repos.
  Use before running anything with side effects on real data, and whenever the user asks to push,
  publish, archive, reset, force push, scrub, rename, clean up history or "keep history clean".
  Covers dry runs, snapshots and backups, canaries, environment parity, confirmation, authorship and
  post-change verification. Git and GitHub specifics are in references/git-github.md.
---

# Safe changes

Anything with a side effect on real data needs a preview, a backup and a named target. The pattern never changes: look first, back up, do the smallest operation that works, then prove the result. This holds for a bulk edit of tickets and for a force-push alike.

## Rules

1. **Look first.** Read the current state of every target before touching it. For git that means branches, unpushed commits and remote state; for live systems it means the fields, status and links you are about to change.
2. **Name the environment.** State which environments a change applies to (for example test and production) and which one you actually changed. When asked to "deploy", deploy to every environment requested, or say plainly which were skipped.
3. **Never test on real data.** Do not run rollbacks, status transitions, deletes or write tests against real tickets, groups, resources or branches. Use test objects or mocks.
4. **Dry run first.** Give every script that mutates state a `--dry-run` that prints each change it would make. Show the output before the real run.
5. **Back up before bulk or destructive changes.** Save the full current state of each target (fields, status, links) to a timestamped JSON file, or for git a backup branch or archive repo. Rollback then means replaying the backup, and anything that cannot be restored is flagged up front.
6. **Canary, then full run.** Apply to one chosen target, pause for confirmation, then run the rest.
7. **Do not delete what you did not create.** If cleanup is needed, list candidates and ask. A permission block on a delete is a signal to stop, not to find another route.
8. **Confirm destructive or irreversible steps** unless the user already authorised that exact action. Never take such a step on your own initiative. If you alter something by accident, restore it at once and say so.
9. **Prove the result.** Re-read the live state after the change and compare it with what you intended; do not rely on the command's success message. See `verify-before-reporting`.
10. **Report what changed.** Keep an audit record (a spreadsheet or log) with before and after values.

## Before you start

Write the short plan from `plan-before-building` (targets, environments, how you will verify, how to undo) and wait for approval on anything beyond read-only work.

## Git and GitHub

For pushes, publishing, history rewrites, force-pushes, resets, branch and fork deletion, repo renames and public-versus-private decisions, read `references/git-github.md`. It adds the git-specific pre-flight commands, a safe `--force-with-lease` form, rename checklist, authorship rules and post-push verification.

## Related

- `verify-before-reporting` for checking results against live data.
- `plan-before-building` for locking scope before a multi-step change.
