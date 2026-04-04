# Content Management

Create, edit, publish, schedule, and manage blog content in emdash.

## Reading Order

| Task | Start With | Then Read |
|------|-----------|-----------|
| Create or edit posts | api.md | patterns.md |
| Publish or schedule | api.md | patterns.md |
| Manage drafts/revisions | api.md | gotchas.md |
| Troubleshoot content issues | gotchas.md | api.md |

## Key Concepts

- **Collections** — Content types (e.g., `posts`, `pages`). Each is a real SQL table (`ec_posts`).
- **Portable Text** — Rich text is JSON (not HTML). All content fields use this format.
- **Revisions** — Every edit creates a revision. Up to 50 retained per item. Can restore any.
- **Optimistic Concurrency** — Updates require `_rev` token to prevent conflicts.
- **Status Flow:** `draft` → `published` (or `scheduled` → `published` at `scheduledAt` time)
- **Soft Delete** — `DELETE` trashes items. Use `permanent` endpoint to hard delete.
- **Localization** — Items have `locale` and `translation_group` for i18n.

## Available Interfaces

| Operation | MCP Tool | REST Endpoint | CLI Command |
|-----------|----------|---------------|-------------|
| List items | `content_list` | `GET /content/{collection}` | `emdash content list` |
| Get item | `content_get` | `GET /content/{collection}/{id}` | `emdash content get` |
| Create | `content_create` | `POST /content/{collection}` | `emdash content create` |
| Update | `content_update` | `PUT /content/{collection}/{id}` | `emdash content update` |
| Delete (trash) | `content_delete` | `DELETE /content/{collection}/{id}` | `emdash content delete` |
| Publish | `content_publish` | `POST /.../publish` | `emdash content publish` |
| Unpublish | `content_unpublish` | `POST /.../unpublish` | `emdash content unpublish` |
| Schedule | `content_schedule` | `POST /.../schedule` | `emdash content schedule` |
| Discard draft | `content_discard_draft` | `POST /.../discard-draft` | -- |
| Compare | `content_compare` | `GET /.../compare` | -- |
| Duplicate | `content_duplicate` | `POST /.../duplicate` | -- |
| Restore (trash) | `content_restore` | `POST /.../restore` | `emdash content restore` |
| Hard delete | `content_permanent_delete` | `DELETE /.../permanent` | -- |
| List trashed | `content_list_trashed` | `GET /.../trash` | -- |
| Translations | `content_translations` | `GET /.../translations` | `emdash content translations` |
| Search | `search` | `GET /search` | `emdash search` |
| List revisions | `revision_list` | `GET /.../revisions` | -- |
| Restore revision | `revision_restore` | `POST /revisions/{id}/restore` | -- |
