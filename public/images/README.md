# Image Organization Guide

## Directory Structure

```
public/images/
├── profile/          # Professional headshots, CV photo
├── blog/             # Blog post images
├── research/         # Research-related images, diagrams, charts
└── teaching/         # Classroom photos, teaching materials
```

## Usage Examples

### In Markdown (Blog Posts)
```markdown
---
title: "My Post"
heroImage: "/images/blog/my-hero-image.jpg"
---

![Description](/images/blog/inline-image.jpg)
```

### In Astro Components
```astro
<img src="/images/profile/headshot.jpg" alt="Benjamin Chapin" />
```

### In CSS (Background Images)
```css
background-image: url('/images/research/lab-background.jpg');
```

## Image Best Practices

1. **Optimize before uploading** - Use tools like TinyPNG or ImageOptim
2. **Use descriptive names** - `climbing-red-rocks-2025.jpg` not `IMG_1234.jpg`
3. **Recommended formats:**
   - Photos: `.jpg` (smaller file size)
   - Graphics/logos: `.png` (transparency support)
   - Icons: `.svg` (scalable)
4. **Size guidelines:**
   - Blog hero images: 1200x630px (good for social sharing)
   - Profile photos: 500x500px
   - Inline images: 800-1200px wide

## Current Placeholder Images

These are in the root of `public/` - you can replace them:
- `/blog-placeholder-1.jpg`
- `/blog-placeholder-2.jpg`
- `/blog-placeholder-3.jpg`
- `/blog-placeholder-about.jpg`

Consider moving them to `images/blog/` and updating references in your posts.

## Note

Files in `public/` are served as-is. Don't put sensitive documents here - use `docs/` for private reference materials that won't be deployed.
