---
name: confluence-storage-format
description: >-
  Rules for creating and updating Confluence pages through an MCP server or API using storage format. Covers macro body types (plain-text-body vs rich-text-body), code blocks, info/tip/warning boxes, expand macros and the update workflow. Use when writing or editing Confluence page content programmatically.
compatibility: Requires a Confluence MCP server or REST access with a base URL and personal access token.
---

# Confluence Storage Format

## Confluence Page Creation (MCP or API)

When creating or updating Confluence pages via a Confluence MCP server or REST API, always use **Confluence storage format** (XHTML with `ac:` namespace). Follow these rules exactly.

### Macro body types

Confluence macros use **two different body types** — mixing them causes silent content loss:

| Macro type | Body tag | Content |
| --- | --- | --- |
| `code`, `noformat` | `<ac:plain-text-body><![CDATA[...]]></ac:plain-text-body>` | Literal text (no escaping) |
| `info`, `tip`, `warning`, `note`, `expand`, `panel` | `<ac:rich-text-body>...</ac:rich-text-body>` | XHTML markup |

### Code block (correct)

```xml
<ac:structured-macro ac:name="code">
  <ac:parameter ac:name="language">json</ac:parameter>
  <ac:parameter ac:name="title">example title</ac:parameter>
  <ac:plain-text-body><![CDATA[
{
  "key": "value"
}
  ]]></ac:plain-text-body>
</ac:structured-macro>
```

### Info/tip/warning box (correct)

```xml
<ac:structured-macro ac:name="info">
  <ac:parameter ac:name="title">Note title</ac:parameter>
  <ac:rich-text-body><p>Content with <strong>markup</strong> here.</p></ac:rich-text-body>
</ac:structured-macro>
```

### Expand (accordion) with nested code block

```xml
<ac:structured-macro ac:name="expand">
  <ac:parameter ac:name="title">Click to expand</ac:parameter>
  <ac:rich-text-body>
    <p>Explanation text here.</p>
    <ac:structured-macro ac:name="code">
      <ac:parameter ac:name="language">bash</ac:parameter>
      <ac:plain-text-body><![CDATA[echo "nested code works"]]></ac:plain-text-body>
    </ac:structured-macro>
  </ac:rich-text-body>
</ac:structured-macro>
```

### Workflow

1. **Do not use `generate_macro_html` for code blocks** — it generates `ac:rich-text-body` which silently drops the content.
2. Write the storage format HTML directly and pass it to `update_page_content` with `insert_position: "replace"`.
3. Locate the existing page (search by title, or fetch by ID) before updating, and note its revision.
4. Always increment-safe: `update_page_content` handles versioning automatically.

### Typical setup

The MCP server needs the Confluence base URL and a personal access token. Keep both in environment variables that the MCP server config reads, and export them from your shell profile before the agent starts. Never paste the token into a page, a prompt or a repository.

## Related

- `docs-update-verify-loop` for the update and verification workflow around these rules.
