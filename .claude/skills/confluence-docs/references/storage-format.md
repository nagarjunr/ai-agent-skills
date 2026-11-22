# Confluence storage format examples

Working templates. See `../SKILL.md` for when to use `plain-text-body` versus `rich-text-body`.

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
