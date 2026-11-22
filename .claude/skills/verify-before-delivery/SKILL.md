---
name: verify-before-delivery
description: >-
  Self-check checklist to run before handing over any finished artifact (document, PDF, spreadsheet,
  slide deck, image set, script, answer set or written draft). Use before presenting a final result,
  and whenever the user asks you to double-check, verify, proof or "make sure it is right". Covers
  measured word and page limits, rendering every page, recalculating spreadsheet formulas, confirming
  image crops are distinct and in order, recomputing numbers a second way, and reporting what could
  not be verified.
---

# Verify before delivery

The expensive mistakes are the ones the user finds after you said "done": a memo 40 words over the limit, a PDF whose page 3 broke, an Excel total that was never recalculated, crops in the wrong order. Each is cheap to catch if you look before presenting. This skill moves that checking from the user to you.

## Rule of thumb

Measure, do not estimate. Open or run the real output, not your memory of what you wrote.

## Checks by artifact

**Text with limits (word, character, page or slide counts).**
Count with a tool (`wc -w`, a script, or the converted PDF's page count), compare against the stated limit, and report both numbers. Check the draft against the rubric, brief or case facts line by line, not just overall: list each required item and where it is covered.

**PDF, Word and slides.**
Convert to PDF and render every page to an image, then look at each one. Page 1 looking fine says nothing about page 4. Check tables, headings, fonts, overflow, orphaned headings and page count against any limit.

**Spreadsheets.**
Recalculate all formulas (for example headless LibreOffice, or the library's recalc) and confirm no error cells (`#REF!`, `#DIV/0!`, `#NAME?`). Spot-check two or three results by hand. If the user supplied an updated workbook, diff its inputs against the old one so a changed input does not silently flow through stale cells.

**Images, screenshots and crops.**
When you slice or crop, view every output. Confirm the pieces are distinct (no duplicates), in the right order, and not cut mid-line. If a crop misfires, say so and redo it instead of continuing.

**Numbers and answers.**
Recompute any calculation a second way (different formula path or a quick script). Where a question is ambiguous, state the interpretation you used. Do not present a guess as a result.

**Code and scripts.**
Run it. Run the tests. Show the output that proves it works, and the exact command. Check that files it should create exist and are non-empty.

**Claims and citations.**
Each factual claim should trace to a source you actually read. If you could not check a claim, label it as unchecked.

## Final report

End the hand-over with two short lists, in plain words:

- **Verified:** what you measured or ran, with the numbers (for example "512 words, limit 500 is exceeded, trimmed to 492").
- **Not verified:** what you could not check and why (no access to the live site, assumption about a template, and so on).

A shorter honest list beats a long confident one. If everything passed, say which checks ran rather than only "looks good".
