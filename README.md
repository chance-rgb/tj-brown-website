# Downtown TJ Brown — Website

Single-page coaching website for T.J. "Downtown" Brown — UFC veteran and private MMA / BJJ / striking coach based in Little Rock, Arkansas. Pure HTML/CSS/JS, single file, no build step, no dependencies.

## Deploy to Vercel

The site is configured and ready to ship. Three deploy paths:

### Option 1 — Drag and drop (easiest, no CLI)
1. Go to [vercel.com/new](https://vercel.com/new)
2. Drag the entire `TJ Brown ( Website )` folder onto the page
3. Click "Deploy"
4. ~30 seconds later you'll have a live URL like `tj-brown-website.vercel.app`

### Option 2 — Vercel CLI
```bash
npm i -g vercel
cd "TJ Brown ( Website )"
vercel              # first run: link the project + preview deploy
vercel --prod       # promote to production
```

### Option 3 — Git-connected (recommended long-term)
1. Push this folder to a GitHub/GitLab/Bitbucket repo
2. On Vercel, click "Import Project" and select the repo
3. Every future push to `main` auto-deploys

### Custom domain
Once deployed, in the Vercel dashboard: Project Settings → Domains → add `downtowntjbrown.com` (or whatever domain). Vercel will give you the DNS records to point at it.

The included `vercel.json` already handles clean URLs, image caching, and security headers — nothing to configure.

## File structure

```
TJ Brown ( Website )/
├── index.html                       ← The whole site
├── vercel.json                      ← Deployment config
├── .gitignore                       ← Git ignores
├── CLAUDE.md                        ← Context for Claude Code
├── README.md                        ← You are here
├── TJ_BROWN_BRAND_PROFILE.md        ← Source-of-truth research doc
└── images/
    ├── tj-hero.webp                 ← Hero action photo
    ├── tj-coaching.jpg              ← About section photo
    └── README.txt                   ← Photo specs
```

## Local preview

Double-click `index.html` to open it in your browser. That's it.

If you want a proper local server (some features behave better):
```bash
python3 -m http.server 8000
# then visit http://localhost:8000
```

## Wiring the booking form to Gmail (Phase 2)

The booking form is fully built and validates on submit, but currently runs in **demo mode** — it shows a thank-you message without sending anywhere. To route submissions to TJ's Gmail, follow these steps once:

### Setup (one-time, ~5 minutes)

1. Go to [script.google.com](https://script.google.com) **logged in as TJ** (so emails come from his Gmail).
2. New Project → paste this code:

   ```javascript
   function doPost(e) {
     const data = JSON.parse(e.postData.contents);
     const subject = `New Booking — ${data.firstName} ${data.lastName} (${data.sessionType})`;
     const body = `
   NEW BOOKING REQUEST — downtowntjbrown.com

   Name:        ${data.firstName} ${data.lastName}
   Email:       ${data.email}
   Phone:       ${data.phone || '—'}

   Session:     ${data.sessionType}
   Experience:  ${data.experience}
   Date:        ${data.preferredDate || '—'}
   Time:        ${data.timeWindow || '—'}

   Goals / Notes:
   ${data.goals}

   Submitted:   ${data.submittedAt}
   `;
     MailApp.sendEmail({
       to: 'tj@downtowntjbrown.com',     // <-- TJ's preferred booking inbox
       replyTo: data.email,
       subject: subject,
       body: body
     });
     return ContentService.createTextOutput(JSON.stringify({ok: true}))
       .setMimeType(ContentService.MimeType.JSON);
   }
   ```

3. **Deploy → New Deployment** → Type: **Web App** → Execute as: **Me (TJ)** → Who has access: **Anyone**
4. Copy the deployment URL (looks like `https://script.google.com/macros/s/AKfy.../exec`)
5. Open `index.html`, find `const GMAIL_ENDPOINT = '';` (in the `<script>` block at the bottom)
6. Paste the URL between the quotes
7. Redeploy to Vercel

Every form submission now lands in TJ's inbox.

### Optional: also drop a tentative event on the calendar

Inside the `doPost` function, after `MailApp.sendEmail`:

```javascript
const startTime = new Date(data.preferredDate + 'T10:00:00');
const endTime = new Date(startTime.getTime() + 60*60*1000);
CalendarApp.getDefaultCalendar().createEvent(
  `Tentative: ${data.firstName} ${data.lastName} (${data.sessionType})`,
  startTime, endTime,
  { description: `Email: ${data.email}\nGoals: ${data.goals}`, location: 'Little Rock, AR' }
);
```

## Open TODOs

- Real lesson rates (cards currently show "By appointment")
- Confirm social URLs (FB / YouTube / X tiles point to `#`)
- "The Testimony" podcast card title/date/host once TJ confirms
- Set `GMAIL_ENDPOINT` once Apps Script is deployed
- Register and point `downtowntjbrown.com` (or chosen domain) at Vercel

## Photos

To replace the existing photos, drop new versions in `images/` using the same filenames:
- `tj-hero.webp` (or `.jpg`) — action shot, ~3:4 portrait aspect ratio
- `tj-coaching.jpg` — wider shot, ~16:9 landscape

If you swap formats, update the `<img src="...">` tags in `index.html` to match.

---

Built May 11, 2026.
