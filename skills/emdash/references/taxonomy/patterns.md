# Taxonomy Patterns

## Setting Up a Blog Taxonomy Structure

Standard blog taxonomy setup:

### 1. Categories (hierarchical)

```
taxonomy_create_term({ taxonomy: "categories", slug: "engineering", label: "Engineering" })
taxonomy_create_term({ taxonomy: "categories", slug: "frontend", label: "Frontend", parentId: "engineering-id" })
taxonomy_create_term({ taxonomy: "categories", slug: "backend", label: "Backend", parentId: "engineering-id" })
taxonomy_create_term({ taxonomy: "categories", slug: "product", label: "Product" })
```

### 2. Tags (flat)

```
taxonomy_create_term({ taxonomy: "tags", slug: "javascript", label: "JavaScript" })
taxonomy_create_term({ taxonomy: "tags", slug: "typescript", label: "TypeScript" })
taxonomy_create_term({ taxonomy: "tags", slug: "react", label: "React" })
```

### 3. Assign to content

```
PUT /content/posts/{id}/terms/categories
Body: { "termIds": ["frontend-id"] }

PUT /content/posts/{id}/terms/tags
Body: { "termIds": ["javascript-id", "react-id"] }
```

## Navigation Menu Setup

### Primary navigation

```
1. POST /menus  Body: { "name": "primary", "label": "Primary Navigation" }
2. POST /menus/primary/items  Body: { "type": "custom", "label": "Home", "customUrl": "/", "sortOrder": 0 }
3. POST /menus/primary/items  Body: { "type": "custom", "label": "Blog", "customUrl": "/blog", "sortOrder": 1 }
4. POST /menus/primary/items  Body: { "type": "custom", "label": "About", "customUrl": "/about", "sortOrder": 2 }
```

### Dropdown menu with nested items

```
1. Create parent: POST /menus/primary/items  Body: { "label": "Topics", "customUrl": "#", "sortOrder": 3 }
   → Get parentId from response
2. Create children:
   POST /menus/primary/items  Body: { "label": "Engineering", "customUrl": "/topics/engineering", "parentId": "<parent-id>", "sortOrder": 0 }
   POST /menus/primary/items  Body: { "label": "Product", "customUrl": "/topics/product", "parentId": "<parent-id>", "sortOrder": 1 }
```

## Taxonomy-Driven Content Organization

Use taxonomies to create topic clusters for SEO:

```
Pillar: "React Development" (category)
├── "Getting Started with React" (post, tagged: react, beginner)
├── "React Hooks Deep Dive" (post, tagged: react, advanced)
├── "React Performance Tips" (post, tagged: react, performance)
└── "React vs Vue Comparison" (post, tagged: react, vue, comparison)
```

Each post links to the pillar category page, creating internal linking value.
