# Taxonomy API Reference

## List Taxonomies

**MCP:** `taxonomy_list`
**REST:** `GET /taxonomies`
**CLI:** `emdash taxonomy list`

Response: `{ "taxonomies": [{ "name", "label", "hierarchical", "collections" }] }`

## List Terms

**MCP:** `taxonomy_list_terms`
**REST:** `GET /taxonomies/{name}/terms`
**CLI:** `emdash taxonomy terms <name>`

For hierarchical taxonomies, returns a tree structure with nested children.

Response: `{ "terms": [{ "slug", "label", "description", "parentId", "count", "children": [...] }] }`

## Create Term

**MCP:** `taxonomy_create_term`
**REST:** `POST /taxonomies/{name}/terms`
**CLI:** `emdash taxonomy add-term <taxonomy> --name "Term" --slug "term"`

```json
{
  "slug": "javascript",
  "label": "JavaScript",
  "parentId": null,
  "description": "Posts about JavaScript programming"
}
```

For hierarchical taxonomies, set `parentId` to create a child term.

## Get Term (REST only)

```
GET /taxonomies/{name}/terms/{slug}
```

Response includes `count` (number of content items) and `children` (child terms).

## Update Term (REST only)

```
PUT /taxonomies/{name}/terms/{slug}
Body: { "label": "New Label", "slug": "new-slug", "parentId": "new-parent-id" }
```

## Delete Term (REST only)

```
DELETE /taxonomies/{name}/terms/{slug}
```

Cannot delete a term that has children. Remove or reparent children first.

## Assign Terms to Content (REST only)

```
PUT /content/{collection}/{id}/terms/{taxonomy}
Body: { "termIds": ["term-id-1", "term-id-2"] }
```

This **replaces** all terms for that taxonomy on the content item. To add a term, include all existing term IDs plus the new one.

---

## Menus

### List Menus

**MCP:** `menu_list`
**REST:** `GET /menus`
**CLI:** `emdash menu list`

Response: `[{ "name", "label", "itemCount" }]`

### Get Menu with Items

**MCP:** `menu_get`
**REST:** `GET /menus/{name}`
**CLI:** `emdash menu get <name>`

Response includes nested menu items with type, label, URL, and children.

### Create Menu (REST only)

```
POST /menus
Body: { "name": "main-nav", "label": "Main Navigation" }
```

### Add Menu Item (REST only)

```
POST /menus/{name}/items
Body: {
  "type": "custom",
  "label": "Blog",
  "customUrl": "/blog",
  "target": "_self",
  "parentId": null,
  "sortOrder": 0
}
```

Item types:
- `custom` — Custom URL
- `content` — Links to a content item (`referenceCollection` + `referenceId`)
- `taxonomy` — Links to a taxonomy term page

### Reorder Menu Items (REST only)

```
POST /menus/{name}/reorder
Body: [
  { "id": "item-1", "parentId": null, "sortOrder": 0 },
  { "id": "item-2", "parentId": null, "sortOrder": 1 },
  { "id": "item-3", "parentId": "item-1", "sortOrder": 0 }
]
```

### Delete Menu (REST only)

```
DELETE /menus/{name}
```

Cascades — deletes all menu items within the menu.
