# Audiocache Development Plan

## Overview

Migrate audiocache.org from WordPress on GoDaddy to self-hosted Ghost on Oracle Cloud, with a custom theme featuring embedded audio players and streaming capabilities.

---

## Phase 1: Infrastructure Setup

### 1.1 Oracle Cloud Free Tier VPS
- [ ] Create Oracle Cloud account (free tier — no credit card charge)
- [ ] Provision an ARM Ampere A1 instance (4 OCPU, 24GB RAM — free forever)
- [ ] OS: Ubuntu 22.04 LTS
- [ ] Open ports: 80, 443 in security list
- [ ] SSH access configured with key pair

### 1.2 DNS and SSL
- [ ] Create Cloudflare account (free tier)
- [ ] Add `audiocache.org` to Cloudflare
- [ ] Update nameservers at GoDaddy to point to Cloudflare
- [ ] Create A record pointing to Oracle Cloud instance IP
- [ ] Enable Cloudflare SSL (Full Strict mode)
- [ ] Enable caching rules for audio files

### 1.3 Ghost Installation
- [ ] Install Node.js 18 LTS on the VPS
- [ ] Install Ghost CLI: `npm install -g ghost-cli`
- [ ] Create Ghost directory: `/var/www/ghost`
- [ ] Run `ghost install` (production mode with MySQL, Nginx, systemd)
- [ ] Verify Ghost is live at `https://audiocache.org`
- [ ] Configure Ghost admin account

### 1.4 Email (Mailgun)
- [ ] Sign up for Mailgun (free tier: 1,000 emails/month)
- [ ] Add and verify `audiocache.org` domain in Mailgun
- [ ] Add Mailgun DNS records in Cloudflare (SPF, DKIM, MX)
- [ ] Configure Ghost with Mailgun SMTP credentials
- [ ] Send a test newsletter to yourself

**Milestone: Ghost is live, can publish posts, and can send emails.**

---

## Phase 2: Content Migration

### 2.1 Export from WordPress
- [ ] WordPress Admin → Tools → Export → All Content (XML)
- [ ] Download the export file

### 2.2 Import into Ghost
- [ ] Ghost Admin → Settings → Migration → WordPress
- [ ] Upload the WordPress XML export
- [ ] Review imported posts — fix formatting issues
- [ ] Re-upload any images that didn't transfer

### 2.3 Subscriber Migration
- [ ] Export subscriber list from Google Apps Script (CSV with name, email)
- [ ] Ghost Admin → Members → Import → upload CSV
- [ ] Verify subscriber count (~150)
- [ ] Send a test newsletter to confirm delivery

### 2.4 Legacy Map Archive
- [ ] Create a static page in Ghost for the map
- [ ] Embed the map content as an HTML card (iframe or inline)
- [ ] Link it from the site navigation as "Archive" or "Map"

**Milestone: All existing content and subscribers are in Ghost.**

---

## Phase 3: Custom Theme with Audio Players

This is the creative phase — building the look and feel of audiocache.org.

### 3.1 Theme Scaffolding
- [ ] Initialize theme from Ghost Starter theme
- [ ] Set up local development with `ghost install local`
- [ ] Configure `package.json` with theme metadata
- [ ] Establish base layout in `default.hbs`

### 3.2 Audio Player Component
- [ ] Build `partials/audio-player.hbs` — reusable player partial
- [ ] Implement `assets/js/audio-player.js`:
  - Play/pause with waveform or progress bar
  - Time display (elapsed / total)
  - Volume control
  - Track title and metadata display
  - Mobile-responsive controls
- [ ] Style the player in `assets/css/audio-player.css`
- [ ] Support Google Drive direct links as audio source
  - Google Drive file format: `https://drive.google.com/uc?export=download&id=FILE_ID`
  - Test CORS and streaming behavior; fall back to download link if needed

### 3.3 Page Templates
- [ ] `index.hbs` — homepage showing latest newsletters + a featured track player
- [ ] `post.hbs` — newsletter post layout with inline audio players embedded via HTML cards or custom markup
- [ ] `page.hbs` — static page layout for events and archive
- [ ] `page-events.hbs` — (optional) dedicated events template with date/location styling
- [ ] `partials/navigation.hbs` — site nav
- [ ] `partials/newsletter-signup.hbs` — subscriber sign-up form (uses Ghost's built-in Portal)

### 3.4 Design Direction
- [ ] Dark theme by default (fits the current audiocache aesthetic)
- [ ] Minimal, content-first layout
- [ ] Typography-focused — let the writing breathe
- [ ] Audio players as first-class UI elements, not afterthoughts

**Milestone: Custom theme deployed. Posts with embedded audio are playable in-browser.**

---

## Phase 4: Streaming Experience

Take the audio player from basic playback to a proper streaming experience.

### 4.1 Enhanced Player Features
- [ ] Continuous playback — music keeps playing as user navigates pages (use Ghost's `<iframe>` portal or a persistent player bar)
- [ ] Playlist support — queue up multiple tracks from a post
- [ ] Keyboard shortcuts (spacebar to play/pause, arrows to skip)
- [ ] Remember playback position across page loads (localStorage)

### 4.2 Audio Hosting Evaluation
- [ ] Test Google Drive streaming reliability at scale
- [ ] If Google Drive has CORS or rate-limiting issues, evaluate alternatives:
  - Cloudflare R2 (free egress, S3-compatible)
  - Backblaze B2 (free egress through Cloudflare bandwidth alliance)
- [ ] Implement appropriate solution

### 4.3 Music Library Page
- [ ] Create a dedicated `/music` page listing all available tracks
- [ ] Filterable by date, project, or tag
- [ ] Each track has play button, title, duration, and download option
- [ ] This page acts as the streaming hub

**Milestone: Users can stream music in-browser with a real player experience.**

---

## Phase 5: Polish and Launch

### 5.1 Testing
- [ ] Test newsletter send + web publish workflow end-to-end
- [ ] Test audio playback on: Chrome, Firefox, Safari, iOS Safari, Android Chrome
- [ ] Test mobile responsiveness across all templates
- [ ] Load test with Cloudflare caching enabled
- [ ] Verify email deliverability (check spam folders, SPF/DKIM alignment)

### 5.2 SEO and Metadata
- [ ] Verify Ghost's built-in sitemap (`/sitemap.xml`)
- [ ] Configure Open Graph and Twitter card metadata in theme
- [ ] Set up proper `<meta>` descriptions for key pages
- [ ] Submit sitemap to Google Search Console

### 5.3 Cutover
- [ ] Announce to subscribers: "We've moved — same address, new experience"
- [ ] Redirect any old WordPress URL patterns if needed (Ghost supports redirects via JSON)
- [ ] Cancel GoDaddy hosting
- [ ] Delete WordPress installation
- [ ] Monitor Mailgun delivery stats for first few newsletters

**Milestone: audiocache.org is fully live on Ghost. WordPress is gone.**

---

## Ongoing Costs

| Service | Cost |
|---------|------|
| Oracle Cloud VPS | $0 (free tier) |
| Cloudflare DNS + CDN | $0 (free tier) |
| Mailgun email | $0 (free for first 1,000/month) |
| Google Drive storage | Already paying |
| Domain renewal | ~$12/year |
| **Total** | **~$1/month** |

---

## Future Ideas (Not in Scope Now)

- RSS feed for podcast apps (Ghost generates RSS natively)
- Listener analytics (privacy-respecting, e.g., Plausible or Umami)
- Accept donations/tips via Stripe (Ghost has native Stripe integration)
- Collaborative playlists or listener-submitted music
- API access for a potential mobile app
