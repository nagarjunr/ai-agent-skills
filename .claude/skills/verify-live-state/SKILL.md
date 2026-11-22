---
name: verify-live-state
description: >-
  Re-check live state before reporting findings. Use when investigating cloud
  resources, directory or identity objects, CI jobs, or pull requests, when
  estimating from API results, or when a conclusion depends on data that may be
  partial or stale. Covers pagination, API version gaps, fresh PR commits and
  stating what was and was not checked.
---

# Verify Live State

Wrong conclusions in investigations usually come from a partial or stale view, not from bad reasoning. Fetch the full, current picture before you state a finding.

## Checklist

1. **Paginate fully.** If a response has `nextLink`, `nextPageToken`, `$skiptoken`, `--query` truncation or a default page size, follow it until exhausted. Never compute counts, durations or failure rates from the first page.
2. **Check the API version.** Stable endpoints can hide objects that beta endpoints return (for example Microsoft Graph v1.0 omitting service principal owners). When an expected object is missing, retry on the other version and in the UI before theorising.
3. **Re-fetch PR state.** Before drafting review comments or a "what is missing" list, fetch the PR's latest commits and diff. New commits invalidate earlier findings.
4. **Prefer primary evidence.** Audit logs, job history and the resource itself beat inference. When the user shares a screenshot that contradicts your result, treat your query as the suspect.
5. **Re-check sessions.** Expired logins and cached tokens return empty or partial results that look valid. Confirm the identity and scope of the session before trusting an empty list.
6. **Report the coverage.** End findings with: what you checked, how many pages or items, which endpoint or version, and anything you could not verify.

## Do Not

- Present an estimate without saying what it covers.
- Keep a theory after live data contradicts it. Retract it in one line and continue.

## Related

- `verify-before-delivery` covers checking a finished artifact (document, spreadsheet, script) before handing it over. This skill covers checking live data before stating a finding.
