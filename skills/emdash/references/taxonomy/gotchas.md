# Taxonomy Gotchas

## Term Assignment Replaces (Not Appends)

`PUT /content/{collection}/{id}/terms/{taxonomy}` with `{ "termIds": [...] }` **replaces all terms** for that taxonomy. To add a term without removing existing ones:

1. Fetch current terms (from the content item's data)
2. Add the new term ID to the list
3. PUT the full list

## Cannot Delete Terms with Children

Hierarchical taxonomy terms cannot be deleted if they have child terms. Either:
- Delete or reparent all children first
- Then delete the parent

## Term Slugs Must Be Unique Per Taxonomy

Within a taxonomy, each term slug must be unique. Across different taxonomies, the same slug is fine (e.g., both categories and tags can have a "javascript" term).

## MCP Taxonomy Limitations

MCP tools can:
- List taxonomies and terms
- Create terms

MCP tools **cannot**:
- Update terms
- Delete terms
- Assign terms to content

Use the REST API for these operations.

## Menu Item Types

When creating menu items, the `type` field determines what other fields are required:
- `custom` → requires `customUrl`
- `content` → requires `referenceCollection` and `referenceId`
- `taxonomy` → links to a taxonomy term page

## Menu Deletion Cascades

Deleting a menu (`DELETE /menus/{name}`) also deletes all its items. There is no undo.
