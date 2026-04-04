# Media Patterns

## Upload and Embed in Post

```
1. Upload image via REST:
   POST /media  (multipart/form-data with file)
   → Get mediaId from response

2. Reference in Portable Text body:
   {
     "_type": "image",
     "mediaId": "<media-id>",
     "alt": "Description of image"
   }

3. Update the post with the new body content
```

## Featured Image Pattern

Many blog templates expect a featured image. Set it in the content data:

```json
{
  "data": {
    "title": "My Post",
    "featuredImage": "<media-id>",
    "body": [...]
  }
}
```

The field name depends on your collection schema — check `schema_get_collection` to find the correct field.

## Batch Upload

Upload multiple images sequentially:

```
for each file:
  1. POST /media with file
  2. Record returned mediaId
  3. Use mediaIds when creating/updating content
```

## Alt Text Best Practices

Always set descriptive alt text when uploading or updating media:

- Describe what the image shows, not what it is ("A developer reviewing code" not "photo.jpg")
- Keep under 125 characters
- Don't start with "Image of" or "Photo of"
- For decorative images, use empty string `""`

## Media Library Cleanup

Find and remove unused media:

```
1. media_list — get all media items
2. For each, search content for references to that mediaId
3. If no content references it, consider deleting
```

Note: There is no built-in "unused media" report — you must cross-reference manually.
