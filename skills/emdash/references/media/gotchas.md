# Media Gotchas

## MCP Cannot Upload

The MCP server exposes `media_list`, `media_get`, `media_update`, and `media_delete` — but **not upload**. To upload files, use:

- **REST API:** `POST /media` with multipart/form-data
- **CLI:** `emdash media upload <file>`

## Content Hash Deduplication

If you upload a file with the same content hash as an existing media item, emdash returns the existing item instead of creating a duplicate. This is by design.

## Permanent Deletion

`media_delete` permanently removes the file from R2 storage. There is **no trash/restore** for media (unlike content). Be careful.

## File Size Limits

Cloudflare Workers have request size limits. For files over 100MB, use the presigned upload flow (`POST /media/upload-url`) to upload directly to R2.

## Image Dimensions

When uploading via REST, emdash auto-detects `width` and `height` for images. When using presigned uploads, you should provide dimensions in the confirm step for proper rendering.

## Supported File Types

EmDash accepts any file type for upload. Common types:

- Images: JPEG, PNG, WebP, GIF, SVG, AVIF
- Documents: PDF
- Video: MP4, WebM
- Audio: MP3, WAV, OGG

The `mimeType` query parameter on `media_list` can filter by type.
