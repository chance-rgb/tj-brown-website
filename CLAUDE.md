# Context for Claude Code

This project is the personal website for **T.J. "Downtown" Brown** — UFC veteran turned private MMA / BJJ / striking coach in Little Rock, Arkansas. The site exists to drive private-lesson bookings, serve as a press kit, and centralize his coaching brand.

## Tech stack

- **Single-file static site** — `index.html` contains all HTML, CSS, and JS. No build step. No framework. No dependencies.
- Fonts are loaded from Google Fonts (Bebas Neue, Oswald, Inter).
- Hero and About photos live in `/images/` and load with graceful `onerror` fallback to styled placeholders.
- The booking form is wired but runs in **demo mode** until `GMAIL_ENDPOINT` is set (see below).

## File map

```
/index.html                       ← The entire site
/vercel.json                      ← Deployment config (clean URLs, caching, security headers)
/.gitignore                       ← Standard ignores
/images/
  ├── tj-hero.webp                ← Hero action photo
  ├── tj-coaching.jpg             ← About section photo
  └── README.txt                  ← Photo specs / drop-in instructions
/README.md                        ← User-facing deployment guide
/CLAUDE.md                        ← You are here
/TJ_BROWN_BRAND_PROFILE.md        ← Source of truth for content, voice, fight data
```

## Deployment target

**Vercel via GitHub** is the chosen workflow (Git-connected auto-deploys on every push to `main`).

**→ Follow `DEPLOY.md` for the full step-by-step playbook.** It includes a known cleanup step for a broken `.git` folder left behind by a previous tooling session.

The included `vercel.json` handles clean URLs, immutable image caching, and basic security headers — no build configuration needed. This is a pure static site (no framework, no `package.json`, no build command).

## Open TODOs

These are intentionally unfinished and tracked here so they don't get lost:

1. **Wire the booking form to Gmail.** Currently `GMAIL_ENDPOINT` is an empty string in `index.html` (search for it — it's near the bottom of the `<script>` block). When the user is ready, see `README.md` for the Google Apps Script setup. Once they deploy the script and have the `/exec` URL, paste it into that constant.
2. **Real lesson rates.** The three `.lesson-card` blocks (1-on-1, Kids, Small Group) currently show "By appointment" / "Ages 6 to 14" / "2–4 athletes" in place of dollar amounts. The user (Chance) needs TJ to confirm pricing before that goes in.
3. **Social URLs.** Footer has IG wired (`@downtowntjbrown`). FB / YouTube / X social tiles point to `#` — replace with real URLs once confirmed.
4. **"The Testimony" podcast card label.** Currently labeled `The Testimony Podcast · TJ Brown — The Full Story` linking to `https://www.youtube.com/watch?v=9TJ8Wm9H0Uk`. Refine title/date/host name once TJ confirms.
5. **Domain.** Plan is `downtowntjbrown.com` — verify availability, register, point to Vercel.

## Style / brand guardrails

- **Tone is laid-back coach, not fight-poster.** Earlier iterations leaned hard into the fight resume; user (Chance) asked for warmer / friendlier / coaching-first. Keep it that way unless he says otherwise.
- **Palette:** white background, blue outlines (`#1E3A8A`), black accents (`#0B0B0B`). No red. Don't reintroduce red without asking.
- **Fight career is supporting material**, not the lead. About section barely mentions it. Highlights are condensed to three cards. No full fight-record table.
- Read `TJ_BROWN_BRAND_PROFILE.md` if you need voice / story / verified factual context (born May 22 1990, Pine Bluff, BJJ black belt under Roli Delgado, Performance of the Night at UFC 282, Fight of the Night vs. Algeo, son Kyler, "Broke the Chain" narrative, etc.).

## Local preview

```bash
# any of these — no build needed
python3 -m http.server 8000
# or
npx serve .
# or just double-click index.html
```
