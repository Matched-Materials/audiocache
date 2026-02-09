# Audiocache

**Cleaving music from commercialism.**

Audiocache is a self-hosted music platform and newsletter for original music by Daniel. It replaces a WordPress + Google Apps Script + Google Docs workflow with a single system: write a newsletter, publish it to the web, and email it to subscribers — all in one step.

## Prerequisites

You need Node.js installed locally for Ghost theme development. Ghost requires **Node.js 18 LTS**.

```bash
# Check if you have Node.js
node --version   # need v18.x

# If not installed, use nvm (recommended):
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
nvm install 18
nvm use 18

# Install Ghost CLI globally
npm install -g ghost-cli
```

That's all you need locally. Ghost itself (the CMS server, database, etc.) runs on the remote VPS in production — not on your machine. Your local environment is only for building and testing the custom theme.

## Hosting: What Lives Where

GoDaddy currently does two things for you. This project splits those apart:

```
BEFORE (all GoDaddy)                 AFTER (split across free services)
──────────────────────               ──────────────────────────────────

GoDaddy Domain Registrar ──────→    GoDaddy Domain Registrar (keep as-is)
  (you own audiocache.org)             (you still own audiocache.org here)
           +                                        │
GoDaddy Web Hosting ───────────→         ┌──────────┴──────────┐
  (WordPress + PHP + MySQL)              │                      │
                                   Cloudflare (DNS)      Oracle Cloud VPS
                                     FREE tier             FREE ARM tier
                                     - Routes traffic      - Runs Ghost CMS
                                     - SSL certificate     - Node.js + MySQL
                                     - CDN caching         - Nginx web server
                                     - DDoS protection     - Sends newsletters
                                                             via Mailgun
```

**In plain terms:**
- **GoDaddy stays your domain registrar** — you bought `audiocache.org` there, no need to move it
- **Cloudflare replaces GoDaddy's DNS** — you change the nameservers at GoDaddy to point to Cloudflare. Cloudflare then directs visitors to your Oracle Cloud server. This also gives you free SSL, caching, and DDoS protection.
- **Oracle Cloud replaces GoDaddy's web hosting** — Ghost runs here instead of WordPress. This is the actual server that stores your content and serves your website.
- **GoDaddy web hosting gets cancelled** — you don't need it anymore once Ghost is running on Oracle Cloud.

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

Local development is only for building the custom Ghost theme (templates, CSS, audio player JS). You do **not** write newsletters locally — that happens in Ghost's browser-based admin UI on the live site.

```bash
# Set up a local Ghost instance for theme development
mkdir ghost-local && cd ghost-local
ghost install local

# Ghost starts automatically. Admin panel:
open http://localhost:2368/ghost

# Your theme files live in:
# ghost-local/content/themes/audiocache/

# After making theme changes, restart Ghost to pick them up:
ghost restart
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
