# Jira Field Reference

This document serves as a reference for custom field mappings in our Jira instance.

## Custom Field Mappings

| Field Name | Custom Field ID | Description |
|------------|----------------|-------------|
| Acceptance Criteria | customfield_12307 | Contains the acceptance criteria for stories in bullet point format |
| Components | components | Used to categorize issues by system area, e.g., "Backend Processes" |
| Parent | parent | Links stories to their parent epic using the parent field (not epic link) |
| Epic Link (field) | customfield_11406 | Contains the epic key (e.g., "SAF-3271") - auto-populated when parent is set |
| Epic Name (field) | customfield_11405 | Contains the epic summary - auto-populated when parent is set |

## Usage Example

When updating stories through the Jira API, use the custom field ID to set the acceptance criteria:

```json
{
  "fields": {
    "customfield_12307": "* First acceptance criterion\n* Second acceptance criterion\n* Third acceptance criterion"
  }
}
```

## Common Operations

### Setting Acceptance Criteria via API

```javascript
// Example of setting acceptance criteria via the Jira API
jira.updateIssue({
  issueKey: "SAF-1234",
  fields: {
    customfield_12307: "* First acceptance criterion\n* Second acceptance criterion"
  }
});
```

## Jira Markup Reference

Since Markdown doesn't work in Jira fields, use Jira's wiki markup language instead:

| Format | Jira Markup | Result |
|--------|------------|--------|
| Headings | h1. Heading 1<br>h2. Heading 2<br>h3. Heading 3 | Large, medium, and small headings |
| Bold | *bold text* | **bold text** |
| Italic | _italic text_ | *italic text* |
| Bullet List | * First item<br>* Second item | • First item<br>• Second item |
| Numbered List | # First item<br># Second item | 1. First item<br>2. Second item |
| Table | \|\|heading 1\|\|heading 2\|\|<br>\|row 1, col 1\|row 1, col 2\| | A simple table with headers |
| Links | [link text\|URL] | Hyperlink with custom text |
| Code Block | {code}<br>code here<br>{code} | Formatted code block |

## Additional Notes

- The acceptance criteria field expects bullet points with a leading asterisk (*)
- Each criterion should be on a new line
- No header (like "h2. Acceptance Criteria") should be included in this field
- Markdown formatting **does not work** in Jira fields; use Jira's wiki markup instead (h1., h2., etc.)
- Don't include a header that duplicates the field name (e.g., don't add "Description:" at the start of a Description field)
- For the Description field, include the user story statement first ("As a..."), followed by any background sections
- **Parent Field vs Epic Link**: Use the `parent` field to link stories to epics, not the epic link field. The epic link custom fields are auto-populated when the parent is set. For API updates, set both: `"parent": {"key": "SAF-XXXX"}` and `"customfield_11406": "SAF-XXXX"` to ensure proper linking.
