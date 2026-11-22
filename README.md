# AI Agent Skills

A collection of skills for Claude Code and other AI agents, for everyday productivity tasks.

## Skills

**Working practices** (how the agent should work; each triggers on its own situation)

| Skill | What it does |
|---|---|
| [verify-before-delivery](#verify-before-delivery) | Measured self-check before handing over any document, spreadsheet, image set or script |
| [paste-ready-output](#paste-ready-output) | Plain text that pastes cleanly into web editors and forms, with measured word counts |
| [safe-repo-operations](#safe-repo-operations) | Careful git and GitHub procedure for pushing, archiving, resetting, force-pushing and renaming |
| [python-env-first](#python-env-first) | Pick the right Python and virtualenv before running anything |
| [verify-live-state](#verify-live-state) | Re-check live state (all pages, latest PR commits, API version gaps) before reporting findings |
| [safe-production-changes](#safe-production-changes) | Dry run, snapshot, canary and environment checks before state-changing work on live systems |
| [plan-before-building](#plan-before-building) | Lock scope, environments and verification in a short plan before implementing |
| [docs-update-verify-loop](#docs-update-verify-loop) | Chunked wiki and diagram updates with a fetch-and-verify pass |

**Tools and methods**

| Skill | What it does |
|---|---|
| [compress-pdf](#compress-pdf) | Compress a PDF to a target size with Ghostscript |
| [linkedin-link-extractor](#linkedin-link-extractor) | Resolve and summarise `lnkd.in` links from LinkedIn posts |
| [youtube-playlist-extractor](#youtube-playlist-extractor) | Pull video titles and URLs from a YouTube playlist without an API key |
| [goldfish-review](#goldfish-review) | Fresh-context fact-check and cold read of a finished draft |
| [deep-research-methodology](#deep-research-methodology) | Independence audit, adversarial critic and citation checks for cited research |
| [confluence-storage-format](#confluence-storage-format) | Confluence storage-format rules for macros, code blocks and page updates through an MCP server or API |
| [drawio-confluence-diagrams](#drawio-confluence-diagrams) | Draw.io styling, straight-line routing and label rules, published to Confluence as editable attachments |

### compress-pdf

Compress PDF files to a target size using Ghostscript while maintaining the best possible quality.

**How to Use:**

Ask Claude to compress a PDF:

```
Compress my-document.pdf to under 500KB
```

```
Make this PDF smaller for email: /path/to/large-file.pdf
```

**What It Does:**

- Intelligently selects compression quality based on target size
- Tries highest quality first, stops when target is met
- Generates timestamped output: `{input_name}_COMPRESSED_{timestamp}.pdf`

**Requirements:**

- Ghostscript: `brew install ghostscript`

---

### linkedin-link-extractor

Extract and verify links from LinkedIn posts. Resolves shortened `lnkd.in` URLs to their actual destinations, fetches page titles, and generates one-line summaries.

**How to Use:**

Paste a LinkedIn post with links and ask Claude to extract them:

```
Extract and verify the links from this LinkedIn post:

VIDEOS
1. LLM Introduction: https://lnkd.in/difutThA
2. Building Agents: https://lnkd.in/dbQGbG8K

REPOS
1. GenAI Agents: https://lnkd.in/d8eHCvcQ
```

**What It Does:**

- Extracts all `lnkd.in` shortened URLs
- Resolves them to actual destination URLs
- Fetches page titles and descriptions
- Generates one-line summaries
- Outputs organized markdown table by category

**Output Example:**

| # | Status | Title | Resolved URL | Summary |
|---|--------|-------|--------------|---------|
| 1 | ✅ | Intro to Large Language Models | https://youtube.com/... | 1 hour introduction to LLMs |
| 2 | ✅ | Building Agents with MCP | https://youtube.com/... | Tutorial on creating AI agents |

**Status indicators:**
- ✅ Successfully resolved and verified
- ⚠️ URL resolved but content couldn't be fetched (e.g., PDFs)
- ❌ Failed to resolve

**Requirements:**

- Python 3.12+ with `requests`, `beautifulsoup4`, `certifi`
- `curl` command-line tool

---

### youtube-playlist-extractor

Extract video titles and URLs from a YouTube playlist using `curl` and the page's embedded `ytInitialData` JSON. No API key and no `yt-dlp`.

**How to Use:**

```
Get the video titles and URLs from this playlist: https://www.youtube.com/playlist?list=PLxxxxxxxx
```

**What It Does:**

- Parses the embedded JSON instead of regex-matching IDs and titles, which pairs the wrong title with a video
- Deduplicates videos and skips UI strings that look like titles
- Handles Unicode and HTML entities in titles
- Includes variations for several playlists at once and for CSV or JSON output

**Not for:** downloading videos (use `yt-dlp`), or playlists that need authentication.

**Requirements:** `curl` and Python 3 (standard library only).

---

### goldfish-review

Fresh-context review of a finished draft before it ships. Two reviewers that have never seen your drafting conversation attack it: a fact-checker that sees only the draft and the raw sources, and a cold reader that sees only the text.

**How to Use:**

```
Fact-check this blog post draft against the sources in ./sources before I publish it
```

```
Red-team my design doc with a cold reader and a fact-checker
```

**What It Does:**

- Builds a clean review folder (draft, extracted image text, source list) with `goldfish_prep.py`
- Produces two ready-to-paste reviewer prompts and explains how to run them in fresh contexts
- Walks through verifying findings, fixing the draft and re-running once
- Includes a checklist of the errors this review keeps finding (a source's claim stated as fact, an inference presented as sourced, a dropped condition)

**Requirements:** Python 3 (standard library only). Idea borrowed from Google's open-source `elephant-goldfish-model`; this is an independent, much smaller implementation.

---

### deep-research-methodology

Deep research for claims that will be cited as evidence: audit source independence, run an adversarial critic pass, verify every citation against its source text.

**How to Use:**

```
Do a deep research pass on whether this claim holds up, with sources that are actually independent
```

**What It Does:**

- Decomposes the question, sweeps sources in parallel with subagents, and clusters derivative copies as one source
- Runs a separate adversarial critic on the draft synthesis
- Checks each citation against the source text and checks papers for retraction

Adapted from the methodology of `jordan-gibbs/hyperresearch` (MIT). No code from that project is used.

---

### verify-before-delivery

A checklist the agent runs before presenting a finished result, so mistakes are caught by the agent instead of the user.

**How to Use:**

```
Before you give me the final version, verify it and tell me what you could not check
```

**What It Does:**

- Counts words and pages with a tool instead of estimating, and checks the draft against the rubric line by line
- Renders every page of a PDF or document, not just page 1
- Recalculates spreadsheet formulas and checks for error cells
- Views every image crop to confirm they are distinct and in order
- Ends with two lists: what was verified (with the numbers) and what was not

---

### paste-ready-output

Produce text that pastes cleanly into a web form, learning-management-system editor, CMS or email without breaking.

**How to Use:**

```
Give me this as plain text I can paste into the editor, under 500 words
```

**What It Does:**

- Defaults to plain text: no HTML, no markdown tables or pipe characters, no doubled blank lines
- Puts field labels on their own lines so a paste cannot run them together
- Counts words and states the number against the limit; offers a short version
- Reuses a known-good earlier file as the format template

---

### safe-repo-operations

A careful procedure for risky git and GitHub operations: look first, back up, do the smallest operation that works, then prove the result.

**How to Use:**

```
Archive the old repo, reset it to upstream exactly, and push
```

```
Rename this repo and fix the local folder and virtualenv
```

**What It Does:**

- Pre-flight checks of branches, unpushed commits, visibility and unrelated histories
- Public versus private triage before anything is published
- Backup branches, exact resets to upstream, and `--force-with-lease` with a full SHA, only when asked
- A rename checklist covering the remote, README, local folder, virtualenv paths and path-keyed settings
- Post-push verification and an honest note about what GitHub still caches after a history rewrite

---

### python-env-first

Choose the right interpreter and environment before running Python, so an outdated system Python or a duplicate virtualenv does not cost a round of confusing errors.

**How to Use:**

```
Run this script with the project's own virtualenv, not the system Python
```

**What It Does:**

- Finds and reuses the project's virtualenv; asks before creating one
- Checks versions and flags interpreters that are too old
- Table of common failures (missing stdlib functions, certificate errors, "bad interpreter" after a move) and their fixes
- Runs scripts in isolated mode when the folder holds downloaded files

---

### verify-live-state

Re-check live data before stating a finding. Complements `verify-before-delivery`, which checks finished artifacts.

**How to Use:**

```
Investigate why this job fails, and use verify-live-state
```

**What It Does:**

- Follows every page of API results instead of computing from the first page
- Retries on the beta endpoint or in the UI when a stable API hides an object
- Re-fetches the latest PR commits before drafting review comments
- Ends findings with what was checked, how much, and what could not be verified

---

### safe-production-changes

Guardrails for state-changing work on live systems such as tickets, cloud resources and directory groups. Complements `safe-repo-operations`, which covers git and GitHub.

**How to Use:**

```
Update these tickets in bulk, using safe-production-changes
```

**What It Does:**

- Names the target environments and reports which one was actually changed
- Requires a `--dry-run`, a state snapshot and a canary before the full run
- Forbids write tests on real data and deleting resources the agent did not create
- Produces an audit report of before and after values

---

### plan-before-building

A five-line written plan agreed before implementation, so shifting requirements do not cause repeated redesigns.

**How to Use:**

```
Plan this first with plan-before-building, then wait for my OK
```

**What It Does:**

- Records scope, out-of-scope items, environments, style rules, verification and undo
- Asks only the questions that change the plan, each with a default
- Updates the plan in a few lines when requirements change, so dropped items do not return

---

### docs-update-verify-loop

Update wiki or Confluence pages and diagrams safely through an API or MCP server.

**How to Use:**

```
Sync the architecture pages to the merged code, using docs-update-verify-loop
```

**What It Does:**

- Keeps page content to the current state, with no pending-change or status notes
- Writes in chunks and re-reads the revision first so a newer edit is never overwritten
- Re-fetches the page and renders each diagram attachment after the update
- Finishes with a page-by-page pass or fail table with evidence

---

### confluence-storage-format

Rules for writing Confluence storage-format XHTML, so macro content is not silently dropped.

**How to Use:**

```
Create this Confluence page with a code block and an info box
```

**What It Does:**

- Distinguishes `plain-text-body` (code, noformat) from `rich-text-body` (info, tip, expand)
- Gives working templates for code blocks, info boxes and expand macros with nested code
- Defines the update workflow: locate the page, write storage format directly, replace content

---

### drawio-confluence-diagrams

Draw.io diagram rules for Confluence pages, with Confluence publishing steps.

**How to Use:**

```
Draw the container view as a Draw.io diagram and publish it to the page
```

**What It Does:**

- Sharp-cornered boxes, a standard colour palette and orthogonal edges with arc jumps
- Straight connectors wherever possible, with exit and entry points computed from a shared coordinate
- Edge labels that stay fully visible, off borders and boxes
- Render, check and upload under a new file name, then compare file sizes

## Using a Skill

- **Claude Code:** copy a folder from `.claude/skills/` into `~/.claude/skills/` (all projects) or into your project's `.claude/skills/`.
- **Claude.ai:** zip a skill folder and upload it in the skills settings (availability depends on your plan).
- **Other agents:** each skill is a plain `SKILL.md` with optional reference files and scripts, so any agent that reads Agent Skills can use them.

## Installation

1. Clone this repository
2. Create and activate a Python virtual environment:
   ```bash
   python -m venv .venv
   source .venv/bin/activate
   ```
3. Install Python dependencies (only `linkedin-link-extractor` needs them):
   ```bash
   pip install -r requirements.txt
   ```
4. (For PDF compression) Install Ghostscript:
   ```bash
   brew install ghostscript
   ```

## License

This project is provided as-is for educational and productivity purposes. Feel free to use, modify, and share these scripts and skills in your own projects. A link back to this repository is appreciated but not required.
