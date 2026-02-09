# Audiocache

**Cleaving music from commercialism.**

Audiocache is a self-hosted music platform and newsletter for original music by Daniel. It replaces a WordPress + Google Apps Script + Google Docs workflow with a single system: write a newsletter, publish it to the web, and email it to subscribers — all in one step.

## What This Project Does

- **Hosts original music** with embedded audio players for in-browser streaming
- **Publishes a monthly newsletter** that simultaneously goes live on the website and emails ~150 subscribers
- **Promotes occasional musical events** via dedicated pages
- **Archives past content** including a legacy map project

## Architecture

```
audiocache.org
├── Ghost CMS ............. content management, newsletter, subscriber management
├── Custom Ghost Theme .... embedded audio players, streaming UI, site design
├── Mailgun ............... email delivery for newsletters (free tier)
├── Oracle Cloud VPS ...... hosting (free ARM tier)
├── Cloudflare ............ DNS, SSL, CDN caching
└── Google Drive .......... audio file storage (existing paid storage)
```

## Key Design Decisions

- **Ghost over WordPress**: Ghost natively unifies web publishing and email newsletters into a single workflow. No plugins, no duplication.
- **Self-hosted Ghost over Ghost Pro**: Free hosting on Oracle Cloud free tier. Full control, no vendor lock-in.
- **Google Drive for audio storage**: Already paid for. Files served via direct links or embedded in custom `<audio>` elements.
- **Open-source stack**: Ghost (MIT), Cloudflare free tier, Mailgun free tier, Oracle Cloud free tier.

## Local Development

```bash
# Install Ghost CLI
npm install -g ghost-cli

# Set up local Ghost instance for theme development
cd theme/
ghost install local

# Start Ghost locally
ghost start

# Ghost admin panel
open http://localhost:2368/ghost
```

## Deployment

See [dev_plan.md](dev_plan.md) for the full setup and deployment plan.

## Project Structure

```
audiocache_site/
├── README.md
├── dev_plan.md
└── theme/               # Custom Ghost theme (to be built)
    ├── package.json
    ├── index.hbs        # Homepage — latest posts + featured audio
    ├── post.hbs         # Newsletter post template with audio player
    ├── page.hbs         # Static pages (events, about, archive)
    ├── default.hbs      # Base layout
    ├── partials/
    │   ├── audio-player.hbs   # Reusable embedded audio player
    │   ├── navigation.hbs
    │   └── newsletter-signup.hbs
    └── assets/
        ├── css/
        └── js/
            └── audio-player.js  # Streaming playback logic
```
