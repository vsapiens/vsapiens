# Personal GitHub Pages Site — Design Document

**Date:** 2026-03-09
**Repo:** `vsapiens/vsapiens.github.io`
**URL:** `https://vsapiens.github.io`

## Overview

A dark & techy, multi-page personal site serving as both a CV/resume and a developer portfolio. Built with Astro + Tailwind CSS, deployed to GitHub Pages. Targets recruiters, hiring managers, and the broader dev community.

## Tech Stack

- **Astro** — static site generator with content collections for blog
- **Tailwind CSS** — utility-first styling
- **GitHub API** — fetch pinned repos at build time for projects page
- **GitHub Actions** — CI/CD to GitHub Pages on push + weekly cron for project refresh
- **Formspree / Web3Forms** — contact form submission (no backend)

## Theme

- Background: `#0d1117` (dark)
- Accent: `#a78bfa` (purple, matching profile README)
- Text: `#c9d1d9` (light gray)
- Elevated surfaces: `#161b22`
- Typography: mono font for headings/accents, sans-serif for body
- Vibe: terminal/code-inspired, consistent with GitHub profile README

## Site Architecture

```
/                  → Home (hero + about + skills summary)
/experience        → Work experience timeline
/projects          → GitHub pinned repos (fetched at build time)
/blog              → Blog listing (Markdown posts + external links)
/blog/[slug]       → Individual blog post pages
/education         → Certifications & education
/contact           → Contact form + social links
/resume            → Embedded PDF viewer + download
```

## Page Designs

### Home Page

**Hero (full viewport):**
- GitHub avatar (circular, purple glow border)
- Name: "Erick Gonzalez"
- Tagline: "Performance Engineer | Backend Dev | AI Builder" (typing animation)
- Subtle animated background (particle/grid)
- CTA buttons: "View Projects" + "Download Resume"

**About (below hero):**
- 2-3 paragraphs condensed from README bio
- Terminal-style or card container

**Skills Summary:**
- Tech stack icons in a grid, grouped by category (Languages, Frameworks, Infrastructure, AI/ML)
- Hover effects with purple glow

### Experience Page

- Vertical timeline, alternating left/right on desktop, stacked on mobile
- Each entry: company, role, date range, 2-3 bullets, tech stack tags
- Timeline line in purple, glowing nodes
- Cards: dark elevated background with purple left border
- Data source: `src/data/experience.json`

### Projects Page

- GitHub API fetched at build time (pinned/starred repos)
- Responsive grid (2-3 columns desktop, 1 mobile)
- Each card: repo name, description, primary language badge, stars/forks, links (repo + demo)
- Purple border on hover, lift animation
- Auto-refresh via weekly GitHub Actions cron

### Blog

**Two content types:**
1. Markdown posts in `src/content/blog/` — Astro content collections, rendered at `/blog/[slug]`
2. External links in `src/data/external-posts.json` — cards that link out with platform icon

**Listing page:**
- All posts (internal + external) sorted by date
- Cards: title, date, description, tags, read time or platform icon
- Tag filtering
- External link icon indicator

**Post page:**
- Max-width prose container
- Syntax highlighting (Shiki, dark theme)
- Table of contents sidebar on desktop
- Previous/next navigation

### Education & Certifications

- Two subsections: Education + Certifications
- Grid of cards, dark elevated backgrounds, purple left accent
- Certifications: name, org, date, verify link
- Data: `src/data/education.json` + `src/data/certifications.json`

### Contact Page

- Form: name, email, message (submits via Formspree/Web3Forms)
- Social links below: LinkedIn, GitHub, email as icon buttons
- Fallback email displayed

### Resume Page

- Embedded PDF viewer (iframe/object)
- "Download PDF" button
- PDF stored at `public/resume.pdf`

## Cross-Cutting Concerns

**Navigation:** Persistent top navbar — name/logo left, page links right, hamburger on mobile.

**Footer:** LinkedIn, GitHub, email links. Consistent across all pages.

**Performance:** Zero JS by default (Astro). Interactive components use `client:load` only where needed. Images optimized with Astro `<Image>`.

**SEO:** Reusable `<SEO>` component per page — title, meta description, Open Graph tags. Blog posts auto-generate or use default OG images.

**Responsive:** Mobile-first with Tailwind breakpoints.

**Deployment:** GitHub Actions — build on push to `main`, deploy to Pages. Optional weekly cron to refresh project data from GitHub API.

## Data Files

```
src/data/experience.json       — work experience entries
src/data/education.json        — education entries
src/data/certifications.json   — certification entries
src/data/external-posts.json   — external blog post links
public/resume.pdf              — downloadable resume
```
