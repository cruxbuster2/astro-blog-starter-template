# Copilot Instructions for ChapinLab Astro Blog

## Architecture Overview

This is an Astro-based static blog deployed to **Cloudflare Workers** (not Pages). Key architectural decisions:

- **Static site with SSG**: All blog posts are pre-rendered at build time using Astro's Content Collections
- **Cloudflare Workers deployment**: Uses `@astrojs/cloudflare` adapter with static assets binding (not traditional Pages deployment)
- **Content-driven**: Blog posts live in `src/content/blog/` and are type-safe via Zod schemas in `src/content.config.ts`
- **File-based routing**: Pages in `src/pages/` map to URLs; dynamic routes use `[...slug].astro` pattern

## Critical Developer Workflows

### Development & Deployment
```bash
npm run dev                    # Local dev server at localhost:4321
npm run build                  # Build static site to ./dist/
npm run preview                # Build + local Wrangler preview
npm run check                  # Full validation: build + typecheck + dry-run deploy
npm run deploy                 # Deploy to Cloudflare Workers
npm wrangler tail              # View production logs
```

**Important**: Use `npm run preview` (not `astro preview`) for accurate production testing—it runs Wrangler with the actual Workers environment.

### Observability
The project has Wrangler observability enabled (`wrangler.json`). Use `npm wrangler tail` to debug production issues in real-time.

## Content Management Patterns

### Adding Blog Posts
1. Create `.md` or `.mdx` file in `src/content/blog/`
2. Required frontmatter (enforced by Zod schema in `src/content.config.ts`):
   ```yaml
   title: "Post Title"
   description: "SEO description"
   pubDate: "Jul 08 2022"
   updatedDate: "Jul 09 2022"  # Optional
   heroImage: "/blog-placeholder-3.jpg"  # Optional
   ```
3. Posts automatically appear on `/blog/` index and generate individual routes at `/blog/{filename}/`

### Content Collection Pattern
- Import posts via `getCollection('blog')` from `astro:content`
- Type safety: `CollectionEntry<'blog'>` provides typed access to frontmatter
- Rendering: Use `render(post)` to get `<Content />` component (see `src/pages/blog/[...slug].astro`)

## Project-Specific Conventions

### Site Configuration
Global site metadata lives in `src/consts.ts` (not `astro.config.mjs`):
```typescript
export const SITE_TITLE = "ChapinLab";
export const SITE_DESCRIPTION = "Welcome to ChapinLab!";
```
Update these for site-wide branding changes.

### Component Organization
- **Layouts**: `src/layouts/BlogPost.astro` - single blog post template with scoped styles
- **Components**: Reusable UI in `src/components/` (Header, Footer, BaseHead, FormattedDate, HeaderLink)
- **Styling**: Global styles in `src/styles/global.css`; component styles use Astro's scoped `<style>` blocks

### SEO & Metadata
`BaseHead.astro` centralizes SEO setup:
- Canonical URLs
- OpenGraph tags
- Twitter Card metadata
- Font preloading for Atkinson custom font

**Pattern**: Every page imports `BaseHead` and passes `title`/`description` props.

### RSS Feed
The RSS feed at `/rss.xml` is generated server-side via `src/pages/rss.xml.js` using `@astrojs/rss`. It automatically includes all blog collection posts.

## Cloudflare-Specific Details

### Adapter Configuration
`astro.config.mjs` uses Cloudflare adapter with `platformProxy.enabled = true` for local dev environment parity.

### Wrangler Settings (`wrangler.json`)
- **`compatibility_flags: ["nodejs_compat"]`**: Enables Node.js APIs in Workers
- **`assets.binding: "ASSETS"`**: Static files served via Workers Static Assets (not R2)
- **`observability.enabled: true`**: Enables logs/metrics
- **`upload_source_maps: true`**: For debugging production errors

### Deployment Target
Update `site: "https://example.com"` in `astro.config.mjs` to your production Cloudflare Workers domain before first deploy.

## TypeScript Configuration

Strict TypeScript setup (`tsconfig.json`):
- Extends `astro/tsconfigs/strict`
- `strictNullChecks: true` enforced
- Type generation: Run `npm run cf-typegen` to update Cloudflare bindings types

## Common Tasks

**Add a new page**: Create `.astro` file in `src/pages/` - it auto-maps to URL (e.g., `src/pages/projects.astro` → `/projects/`)

**Change header links**: Edit `src/components/Header.astro` and update `HeaderLink` components

**Update footer**: Modify `src/components/Footer.astro`

**Add integrations**: Run `npm run astro add <integration>` then update `astro.config.mjs`

**View build output**: The `./dist/` directory contains the compiled static site + Workers runtime

## Debugging Tips

- **Build fails**: Run `npm run check` for combined build + typecheck validation
- **Deployment issues**: Use `wrangler deploy --dry-run` to test without publishing
- **Runtime errors**: Check Workers logs with `npm wrangler tail`
- **Type errors**: Verify `src/content.config.ts` schema matches frontmatter structure
