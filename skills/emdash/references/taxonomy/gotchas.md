# Taxonomy Gotchas

## Taxonomy Names Are Singular

Taxonomy names use the **singular form**: `category`, `tag` — not `categories`, `tags`. Always call `taxonomy_list` first to discover the exact names on your instance.

## Term Assignment May Not Work via API

The documented `PUT /content/{collection}/{id}/terms/{taxonomy}` endpoint returned 404 in integration testing. Term assignment may need to be done through the emdash admin UI. MCP tools also do not support term assignment. This is a known limitation.

## Term Assignment Replaces (Not Appends)

If the term assignment endpoint is available, `PUT /content/{collection}/{id}/terms/{taxonomy}` with `{ "termIds": [...] }` **replaces all terms** for that taxonomy. To add a term without removing existing ones:

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

## DELETE Requests May Be Blocked by CSRF

On Cloudflare-deployed emdash instances, DELETE requests via API token may return `403 "Cross-site DELETE form submissions are forbidden"`. If this happens:
- Use the emdash admin UI for delete operations
- Or ensure your requests include the correct origin/referer headers matching the emdash instance domain
