# Media API Reference

## List Media

**MCP:** `media_list`
**REST:** `GET /media`
**CLI:** `emdash media list`

Query: `cursor`, `limit`, `mimeType` (filter by MIME type, e.g., `image/png`)

Response: `{ "items": [{ "id", "filename", "mimeType", "size", "width", "height", "alt", "caption", "url", "storageKey", "createdAt" }], "nextCursor": "..." }`

## Get Media Item

**MCP:** `media_get`
**REST:** `GET /media/{id}`
**CLI:** `emdash media get <id>`

## Upload (REST/CLI only)

**REST:** `POST /media` (multipart/form-data)
**CLI:** `emdash media upload <file> --alt "Description" --caption "Caption"`

```bash
curl -X POST \
  -H "Authorization: Bearer $EMDASH_API_TOKEN" \
  -F "file=@/path/to/image.jpg" \
  "$EMDASH_URL/_emdash/api/media"
```

Response: `{ "item": { "id", "filename", "url", ... } }` (status 201)

Duplicate files (same content hash) return the existing media item instead of creating a new one.

### Presigned Upload (large files)

For direct-to-R2 uploads, use the two-step process:

```bash
# Step 1: Get presigned URL
POST /media/upload-url
Body: { "filename": "large-video.mp4", "contentType": "video/mp4", "size": 52428800 }

# Response: { "uploadUrl", "method", "headers", "mediaId", "storageKey", "expiresAt" }

# Step 2: Upload directly to R2 using the presigned URL
# Step 3: Confirm upload
POST /media/{mediaId}/confirm
Body: { "size": 52428800, "width": 1920, "height": 1080 }
```

## Update Metadata

**MCP:** `media_update`
**REST:** `PUT /media/{id}`

```json
{
  "alt": "A sunset over the ocean",
  "caption": "Photo taken in Malibu, 2026",
  "width": 1920,
  "height": 1080
}
```

## Delete Media

**MCP:** `media_delete`
**REST:** `DELETE /media/{id}`
**CLI:** `emdash media delete <id>`

**Permanently deletes** the file from R2 storage. Not recoverable.

## Serve Media File

**REST:** `GET /media/file/{storageKey}`

Returns the binary file. Used for serving media to the frontend.
