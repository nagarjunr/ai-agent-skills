---
name: confluence-docs
description: >-
  Create and update wiki or Confluence pages and diagrams safely through an MCP server or API. Use
  when writing or editing Confluence page content programmatically, syncing documentation to merged
  code, using Confluence storage format (macros, code blocks, info, tip, warning and expand boxes),
  or generating Draw.io (mxfile XML) diagrams and publishing them as attachment-based drawio macros.
  Enforces current-state-only content, storage-format macro rules, chunked writes, concurrent-edit
  checks, and a fetch-and-verify pass after every update. Diagram rules are in references/drawio.md
  and macro templates in references/storage-format.md.
compatibility: >-
  Requires a Confluence MCP server or REST access with a base URL and personal access token. Draw.io
  rendering checks need the drawio desktop CLI.
---

# Confluence docs

Documentation edits fail quietly: truncated payloads, macro content silently dropped, cached diagrams, overwritten revisions and stray notes. Follow the same loop every time and close it with a verification pass.

## Content rules

- Describe the **current state only**. No "pending change", status or meta notes in page content.
- Do not import concepts from another framework or platform unless asked.
- Use the naming convention from the project's reference page. Confirm it before applying, and do not assume prefixes.
- Keep diagrams editable: attach the source file, not only an image.

## Update loop

1. **Plan.** List every page and diagram to change and show the list.
2. **Read.** Locate the page (search by title or fetch by ID) and note its revision right before writing. If the revision changed since you first read it, re-read and merge. Never overwrite a newer revision.
3. **Write in chunks.** Large payloads get truncated or rejected. Split the page into sections and update one at a time, using `update_page_content` with `insert_position: "replace"` for a section.
4. **Re-fetch and verify.** Read the page back. Check the text, links and macros, that no unwanted notes slipped in, and that the revision incremented.
5. **Verify diagrams.** Download the attachment and render it. Check names are current, arrows are straight where possible, no label is cut off, and the page shows the new version rather than a cached or pinned old revision.
6. **Report.** Finish with a table of page, check and pass or fail, with evidence. See `verify-before-reporting`.

## Storage format

Always write **Confluence storage format** (XHTML with the `ac:` namespace) directly. Macros use two different body types, and mixing them silently loses content:

| Macro type | Body tag | Content |
| --- | --- | --- |
| `code`, `noformat` | `<ac:plain-text-body><![CDATA[...]]></ac:plain-text-body>` | Literal text, no escaping |
| `info`, `tip`, `warning`, `note`, `expand`, `panel` | `<ac:rich-text-body>...</ac:rich-text-body>` | XHTML markup |

- Do **not** use `generate_macro_html` for code blocks: it emits `ac:rich-text-body`, which silently drops the content.
- Working templates for code blocks, info boxes and expand macros with nested code are in `references/storage-format.md`.
- The MCP server needs the Confluence base URL and a personal access token. Keep both in environment variables that the server config reads, exported from your shell profile before the agent starts. Never paste the token into a page, a prompt or a repository.

## Diagrams

Draw.io diagrams are not embedded inline: upload the `.drawio` XML as a page attachment, then reference it by name from a `drawio` macro. Uploading a file with an existing name fails, so use a versioned name (for example `my-diagram-v2.drawio`) for updates. Box styles, orthogonal and straight-line routing, label visibility and the pre-upload checks are in `references/drawio.md`; read it before generating or editing a diagram.

## Related

- `verify-before-reporting` for the wider checklist and the verified / not verified report.
- `safe-changes` when a documentation change is part of a bulk or live-system operation.
- `plan-before-building` for multi-page changes.
