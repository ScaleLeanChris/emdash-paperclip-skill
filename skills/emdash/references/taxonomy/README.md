# Taxonomy Management

Create and manage categories, tags, custom taxonomies, and navigation menus.

## Reading Order

| Task | Start With | Then Read |
|------|-----------|-----------|
| Create categories/tags | api.md | patterns.md |
| Assign terms to posts | api.md | -- |
| Build navigation menus | api.md (menus section) | patterns.md |
| Troubleshoot | gotchas.md | api.md |

## Key Concepts

- **Taxonomies** — Classification systems (e.g., categories, tags). Defined in the schema.
- **Terms** — Individual items within a taxonomy (e.g., "JavaScript" is a term in the "tags" taxonomy)
- **Hierarchical** — Categories support parent/child relationships. Tags are flat.
- **Menus** — Navigation structures, separate from taxonomies. Support nested items with drag-and-drop ordering.

## Available Interfaces

| Operation | MCP Tool | REST Endpoint | CLI Command |
|-----------|----------|---------------|-------------|
| List taxonomies | `taxonomy_list` | `GET /taxonomies` | `emdash taxonomy list` |
| List terms | `taxonomy_list_terms` | `GET /taxonomies/{name}/terms` | `emdash taxonomy terms` |
| Create term | `taxonomy_create_term` | `POST /taxonomies/{name}/terms` | `emdash taxonomy add-term` |
| Get term | -- | `GET /taxonomies/{name}/terms/{slug}` | -- |
| Update term | -- | `PUT /taxonomies/{name}/terms/{slug}` | -- |
| Delete term | -- | `DELETE /taxonomies/{name}/terms/{slug}` | -- |
| Assign to content | -- | `PUT /content/{col}/{id}/terms/{tax}` | -- |
| List menus | `menu_list` | `GET /menus` | `emdash menu list` |
| Get menu | `menu_get` | `GET /menus/{name}` | `emdash menu get` |
| Create menu | -- | `POST /menus` | -- |
| Add menu item | -- | `POST /menus/{name}/items` | -- |
| Reorder menu | -- | `POST /menus/{name}/reorder` | -- |
