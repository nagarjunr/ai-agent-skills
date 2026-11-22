---
name: verify-before-reporting
description: >-
  Check before you hand over a finished artifact or report a finding. Use before presenting any final
  result (document, PDF, spreadsheet, slide deck, image set, script, answer set, written draft) and
  before stating conclusions from an investigation of live data such as cloud resources, identity
  objects, CI jobs, pull requests or API results. Also use when the user asks to double-check,
  verify, proof or "make sure it is right". Covers measured word and page limits, rendering every
  page, recalculating formulas, image crops, recomputing numbers, following every page of API
  results, API version gaps, re-fetching pull request state, and ending with what was and was not
  checked.
---

# Verify before reporting

The expensive mistakes are the ones the user finds after you said "done": a memo 40 words over the limit, a PDF whose page 3 broke, a spreadsheet total that was never recalculated, a failure rate computed from the first page of results. Wrong conclusions usually come from a partial or stale view, not bad reasoning. Look before you present.

**Rule of thumb:** measure, do not estimate. Open or run the real output, and fetch the full, current data, instead of relying on memory of what you wrote or saw.

## A. Finished artifacts

**Text with limits (word, character, page or slide counts).** Count with a tool (`wc -w`, a script, the converted PDF's page count), compare against the stated limit, and report both numbers. Check the draft against the rubric, brief or case facts line by line: list each required item and where it is covered.

**PDF, Word and slides.** Convert to PDF and render every page to an image, then look at each one. Page 1 looking fine says nothing about page 4. Check tables, headings, fonts, overflow, orphaned headings and the page count against any limit.

**Spreadsheets.** Recalculate all formulas (headless LibreOffice or the library's recalc) and confirm no error cells (`#REF!`, `#DIV/0!`, `#NAME?`). Spot-check two or three results by hand. If the user supplied an updated workbook, diff its inputs against the old one so a changed input does not silently flow through stale cells.

**Images, screenshots and crops.** View every crop. Confirm the pieces are distinct (no duplicates), in order, and not cut mid-line. If a crop misfires, say so and redo it.

**Numbers and answers.** Recompute any calculation a second way. Where a question is ambiguous, state the interpretation you used. Do not present a guess as a result.

**Code and scripts.** Run it and the tests. Show the output that proves it works, with the exact command. Check that files it should create exist and are not empty.

**Claims and citations.** Each factual claim should trace to a source you actually read; label anything you could not check.

## B. Live state and findings

1. **Paginate fully.** If a response has `nextLink`, `nextPageToken`, `$skiptoken`, a default page size or `--query` truncation, follow it until exhausted. Never compute counts, durations or failure rates from the first page.
2. **Check the API version.** Stable endpoints can hide objects that beta endpoints return (for example Microsoft Graph v1.0 omitting service principal owners). When an expected object is missing, retry on the other version and in the UI before theorising.
3. **Re-fetch pull request state.** Before drafting review comments or a "what is missing" list, fetch the PR's latest commits and diff. New commits invalidate earlier findings.
4. **Prefer primary evidence.** Audit logs, job history and the resource itself beat inference. When the user shares a screenshot that contradicts your result, treat your query as the suspect.
5. **Re-check the session.** Expired logins and cached tokens return empty or partial results that look valid. Confirm the identity and scope of the session before trusting an empty list.
6. **Drop dead theories.** If live data contradicts a theory, retract it in one line and continue. Do not present an estimate without saying what it covers.

## Final report

End the hand-over with two short lists in plain words:

- **Verified:** what you measured, ran or fetched, with the numbers (for example "512 words, limit 500, trimmed to 492" or "3 pages of results, 142 items, v1.0 and beta endpoints").
- **Not verified:** what you could not check and why (no access to the live site, assumption about a template, endpoint unavailable).

A shorter honest list beats a long confident one. If everything passed, say which checks ran, not only "looks good".

## Related

- `safe-changes` for confirming the result of a state-changing operation against live data.
- `plan-before-building`: its Verification items are the checks to run here.
- `confluence-docs` for the page-by-page verification pass after a wiki update.
- `paste-ready-output` for checking text as the target editor will see it.
