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

## Architecture

This is a Jekyll static site for Base11 Studios, built on the Urban template. The site is hosted on GitHub Pages at base11studios.com.

### Collections
- `_posts/` - Blog posts (date-prefixed markdown files)
- `_clients/` - Client/portfolio project pages (output as individual pages)
- `_staff_members/` - Team member profiles (not output as pages, used in templates)
- `_drafts/` - Unpublished blog drafts

### Data Files (`_data/`)
- `company.yml` - Company contact info reused across the site
- `footer.yml` - Footer link configuration
- `navigation.yml` - Site navigation structure

### Layouts (`_layouts/`)
- `default.html` - Base layout
- `page.html` - Standard pages
- `post.html` - Blog posts
- `client.html` - Portfolio/client pages
- `archive.html` - Category archives

### Styling
- `css/screen.scss` - Main stylesheet (SCSS)
- `_sass/` - SCSS partials
- Color palette: https://coolors.co/2a2d34-009ddc-f26430-6761a8-009b72

### App Support Pages
Root-level HTML/MD files for app privacy policies, terms, and FAQs (present-sense-*, octonote-*, fit-foods-coach-*, etc.)
