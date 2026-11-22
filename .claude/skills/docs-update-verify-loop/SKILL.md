---
name: docs-update-verify-loop
description: >-
  Update wiki or Confluence-style pages and diagrams safely. Use when editing
  documentation through an API or MCP server, syncing docs to merged code, or
  publishing diagrams. Enforces clean content, chunked writes, concurrent-edit
  checks and a fetch-and-verify pass after every update.
---

# Docs Update and Verify Loop

Documentation edits fail quietly: truncated payloads, cached diagrams, overwritten revisions and stray notes. Close the loop every time.

## Content Rules

- Describe the **current state only**. No "pending change", status or meta notes in page content.
- Do not import concepts from another framework or platform unless asked.
- Use the naming convention from the project's reference page. Confirm it before applying, and do not assume prefixes.
- Keep diagrams editable (source file attached, not only an image).

## Update Loop

1. **Plan.** List every page and diagram to change. Show the list.
2. **Read.** Fetch the page and note its revision right before writing. If the revision changed since you first read it, re-read and merge. Never overwrite a newer revision.
3. **Write in chunks.** Large payloads get truncated or rejected. Split the page into sections and update one at a time.
4. **Re-fetch and verify.** Read the page back. Check the text, links and macros, that no unwanted notes slipped in, and that the revision incremented.
5. **Verify diagrams.** Download the attachment and render it. Check names are current, arrows are straight where possible, no label is cut off, and the page shows the new version rather than a cached or pinned old revision. Upload updates under a new file name.
6. **Report.** Finish with a table of page, check and pass or fail, with evidence.

## Related

- `verify-before-delivery` for the wider hand-over checklist.
- `confluence-storage-format` and `drawio-confluence-diagrams` for the page markup and diagram rules this loop relies on.
