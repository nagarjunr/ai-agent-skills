---
name: paste-ready-output
description: >-
  Produce text that pastes cleanly into a web form, learning-management-system editor, CMS, email
  composer or chat box without breaking. Use whenever the user will copy your output into another
  tool and says "paste", "paste-ready", "plain text", "for the editor", "no HTML", or complains that
  formatting broke. Defaults to plain text with no markdown tables or pipe characters, no doubled
  blank lines, a measured word count, and an optional short version.
---

# Paste-ready output

Rich markdown looks good in a terminal and often breaks when pasted elsewhere: tables turn into rows of pipe characters, headings arrive as literal `##`, HTML shows its tags, and blank lines double up. Getting the format right the first time saves several correction rounds.

## Default format

- **Plain text.** No HTML. No markdown tables, no pipe `|` characters, no `**bold**` markers, no `#` headings.
- **Headings** as a plain line (for example `Recommendation`), optionally followed by a colon or in capitals.
- **Lists** as short lines starting with a hyphen and a space, or numbered `1.` lines.
- **Exactly one blank line** between paragraphs. Never two in a row. Never trailing spaces.
- **Field labels on their own lines** (`Name:` then the value on the next line) when pasting into several fields, so a paste cannot run labels together.
- **Straight quotes and plain hyphens** unless the target is known to handle typographic characters.

## Limits

When a word, character or page limit is stated, count with a tool and state the number: "487 words, limit 500". Stay under the limit with a little room, not exactly on it, because editors count differently. If asked for a short version, give both: the full text first, then the short one, clearly separated and each with its count.

## Learn the target once, then reuse it

Ask which format has worked before, or for a file that pasted correctly, and use it as the template for the structure and styling. A known-good example beats guessing: when the user points to an earlier file as "the working format", copy its conventions exactly (heading style, spacing, how lists look).

If you do not know the target and cannot ask, deliver plain text, and offer to convert to another format on request. Switching from plain text to rich text later is easy; the reverse loses information.

## Delivery

Save the text to a `.txt` file the user can open and copy from, and also print it in the reply when it is short. Do not wrap it in extra commentary inside the text itself: keep notes about the draft outside the paste block, clearly separated.

## Check before handing over

Read the output as the target editor would see it: no leftover markdown, no doubled blank lines, no stray characters, count within the limit. See the `verify-before-reporting` skill for the wider checklist.
