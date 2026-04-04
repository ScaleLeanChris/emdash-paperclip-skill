# Media Management

Upload, organize, and manage images and files in the emdash media library (stored on Cloudflare R2).

## Reading Order

| Task | Start With | Then Read |
|------|-----------|-----------|
| Upload images | api.md | patterns.md |
| Update alt text/captions | api.md | -- |
| Troubleshoot uploads | gotchas.md | api.md |

## Key Concepts

- **R2 Storage** — Media files stored on Cloudflare R2 (S3-compatible)
- **Deduplication** — Uploads are deduplicated by content hash
- **Alt Text** — Always set alt text for accessibility and SEO
- **MCP Limitation** — MCP tools can list, get, update, and delete media, but **cannot upload**. Use REST API or CLI for uploads.

## Available Interfaces

| Operation | MCP Tool | REST Endpoint | CLI Command |
|-----------|----------|---------------|-------------|
| List media | `media_list` | `GET /media` | `emdash media list` |
| Get item | `media_get` | `GET /media/{id}` | `emdash media get` |
| Upload | -- | `POST /media` | `emdash media upload` |
| Update metadata | `media_update` | `PUT /media/{id}` | -- |
| Delete | `media_delete` | `DELETE /media/{id}` | `emdash media delete` |
| Presigned upload | -- | `POST /media/upload-url` | -- |
| Confirm upload | -- | `POST /media/{id}/confirm` | -- |
| Serve file | -- | `GET /media/file/{key}` | -- |
