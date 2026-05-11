# Deployment Playbook — for Claude Code

Step-by-step instructions for shipping this site to production via GitHub + Vercel. Follow in order. **Stop and ask the user** at any checkpoint marked `[USER INPUT NEEDED]`.

## Prerequisites the user needs

Before starting, confirm the user has:

- [ ] Git installed (`git --version`)
- [ ] A GitHub account
- [ ] A Vercel account (free tier is fine — `vercel.com/signup`)

If `gh` CLI is installed (`gh --version`), prefer using it for GitHub repo creation — it's much smoother. Otherwise we'll walk the user through the GitHub web UI.

If `vercel` CLI is installed (`vercel --version`), prefer using it for Vercel deploy. Otherwise the user can import via Vercel's web UI.

## Step 1 — Clean up the broken `.git` folder

A previous tooling session left a half-initialized `.git` directory in this folder that can't be cleaned from the sandbox. Delete it before initializing fresh:

```powershell
Remove-Item -Recurse -Force ".git"
```

Verify it's gone with `Get-ChildItem -Force` (should see no `.git` entry).

## Step 2 — Initialize git and make the first commit

```powershell
git init -b main
git config user.email "chance@atlanticsteelstructures.com"
git config user.name "Chance Johnson"
git add -A
git commit -m "Initial commit: Downtown TJ Brown coaching website"
```

`git status` should show nothing pending. `git log --oneline` should show one commit.

## Step 3 — Create the GitHub repo

`[USER INPUT NEEDED]` — ask the user what they'd like to name the repo. Suggested defaults:
- `tj-brown-website`
- `downtown-tj-brown`
- `tj-brown-coaching`

### Option A: `gh` CLI (preferred if available)

```powershell
gh auth status  # confirm logged in; if not, run: gh auth login
gh repo create <REPO-NAME> --public --source=. --remote=origin --push
```

That command creates the repo, sets the remote, and pushes in one shot. Done with Steps 3 and 4.

### Option B: GitHub web UI

Have the user:
1. Open https://github.com/new
2. Repository name: `<REPO-NAME>`
3. Public or Private — confirm preference with user (private is fine, Vercel reads both)
4. **DO NOT** check "Add a README" or "Add .gitignore" or "Choose a license" — repo must be empty for `git push` to succeed
5. Click **Create repository**
6. Copy the HTTPS URL from the next screen (e.g. `https://github.com/username/repo.git`)

Then in PowerShell:

```powershell
git remote add origin <PASTE-URL-HERE>
git push -u origin main
```

If push prompts for credentials, the user logs in with GitHub username + a Personal Access Token (not their password — passwords haven't worked for git auth since 2021). Easier path: install GitHub CLI (`winget install --id GitHub.cli`) and run `gh auth login` first.

## Step 4 — Verify the push worked

```powershell
git remote -v       # shows origin URL
git log --oneline   # shows the initial commit
```

User should also see all files in the GitHub web UI at their repo URL.

## Step 5 — Deploy to Vercel

### Option A: `vercel` CLI (preferred if available)

```powershell
vercel login        # if not already logged in
vercel              # first run links the project; choose defaults; do a preview deploy
vercel --prod       # promote to production
```

Vercel will auto-detect this is a static site (no framework, no build command). Just confirm the defaults.

### Option B: Vercel web UI

Have the user:
1. Open https://vercel.com/new
2. Click **Import Git Repository**
3. If GitHub isn't linked yet, click **Continue with GitHub** and authorize
4. Find the repo created in Step 3 → click **Import**
5. On the configuration screen, leave everything default (Framework Preset: Other; no build command needed)
6. Click **Deploy**

~30 seconds later they get a production URL like `tj-brown-website.vercel.app`.

## Step 6 — Confirm the deploy

Open the production URL in a browser. Sanity-check:

- [ ] Hero loads with the action photo
- [ ] About section shows the press conference photo
- [ ] All section anchor links work (About / Train / Story / FAQ / Press / Book)
- [ ] Mobile menu opens (resize browser narrow)
- [ ] Booking form validates and shows the demo-mode thank-you message

If anything's broken, check the Vercel deploy logs in the project dashboard.

## Step 7 — (Optional) Custom domain

`[USER INPUT NEEDED]` — does the user want to attach a custom domain right now? If yes:

1. In the Vercel project dashboard: **Settings → Domains**
2. Add the domain (e.g. `downtowntjbrown.com`)
3. Vercel shows 1-2 DNS records to add at the domain registrar
4. Add those records at the registrar (GoDaddy / Cloudflare / Namecheap / etc.)
5. Wait for DNS propagation (usually under an hour)

Vercel handles HTTPS automatically once DNS resolves.

## Future deploys

From now on, any `git push` to `main` triggers an auto-deploy on Vercel within ~60 seconds. Pull requests get preview deployments automatically. The user doesn't need to touch the Vercel dashboard again unless changing settings.

Typical update flow:
```powershell
# make changes to index.html or wherever
git add -A
git commit -m "describe the change"
git push
# wait ~60 seconds, refresh production URL
```

## Open TODOs after deploy (don't forget)

These are documented in `CLAUDE.md` but worth re-flagging post-deploy:

1. **Wire booking form to Gmail** via Google Apps Script — see `README.md`. Single line of code to update (`GMAIL_ENDPOINT`).
2. **Real lesson rates** in the three `.lesson-card` blocks.
3. **Social URLs** in the footer (FB / YouTube / X tiles point to `#`).
4. **Confirm "The Testimony" podcast** card title/date once TJ shares episode details.
