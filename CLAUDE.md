# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Build Commands

```bash
# Install dependencies
bundle install

# Run local development server
bundle exec jekyll serve

# Build the site (outputs to _site/)
bundle exec jekyll build
```

## Deployment

GitHub Pages via GitHub Actions (`.github/workflows/jekyll.yml`). Pushes to `main` trigger automatic builds with `JEKYLL_ENV=production` on ubuntu-22.04 with Ruby 3.1. The site is served at base11studios.com (see `CNAME` file).

## Architecture

Jekyll static site for Base11 Studios, built on the Urban template (CloudCannon).

### Collections
- `_posts/` - Blog posts (date-prefixed markdown, default layout: `post`)
- `_clients/` - Portfolio project pages (output: true, default layout: `client`)
- `_staff_members/` - Team member profiles (output: false, used in templates only)
- `_drafts/` - Unpublished blog drafts

### Data Files (`_data/`)
- `company.yml` - Company contact info (title, emails, address)
- `footer.yml` - Footer link columns and social icons
- `navigation.yml` - Top nav links (supports `highlight` class for CTAs, `target` for external)

### Layouts (`_layouts/`)
- `default.html` - Base layout (includes analytics, nav, footer, vanilla-tilt.js)
- `page.html` extends default - Standard pages with hero section
- `post.html` extends page - Blog posts with author bio and next-post nav
- `client.html` extends default - Portfolio pages with featured image and external link
- `archive.html` extends default - Category archive listings

### Includes (`_includes/`)
- `navigation.html` - Nav from `_data/navigation.yml` with mobile hamburger toggle
- `post-summary.html` - Blog list item (thumbnail + excerpt, 12-column grid)
- `post-title.html` - Categories and date display
- `staff-member.html` - Author profile card with vanilla-tilt 3D effect
- `relative-src.html` - URL helper (prepends baseurl for relative paths)

### Styling
- `css/screen.scss` - Main entry point, imports `_sass/` partials
- `css/grid12.css` - 12-column grid system
- `css/syntax.css` - Code syntax highlighting
- SCSS color variables: brand `#009DDC`, secondary `#009B72`, tertiary `#6761A8`
- Responsive breakpoints: tablet (450px), mid-point (620px), desktop (768px)
- Color palette: https://coolors.co/2a2d34-009ddc-f26430-6761a8-009b72

### Plugins
jekyll-feed, jekyll-seo-tag, jekyll-paginate (10 posts/page at `/blog/:num/`), jekyll-archives (categories), jekyll-sitemap, kramdown-parser-gfm

### App Support Pages
Root-level HTML/MD files for app-specific content, named by app prefix: `fuzzzy-*`, `octonote-*`, `fit-foods-coach-*`, `present-sense-*`, `morts-minions-*`, `caddie-snap-*`. Each app typically has FAQ, privacy policy, and terms pages.
