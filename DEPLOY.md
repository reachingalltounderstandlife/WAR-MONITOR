# Deploy tonight — ~15 minutes

## What you're deploying

3 files → 1 GitHub repo → free public URL, auto-updated every 6 hours forever.

```
conflict-monitor/
├── index.html                      ← the dashboard
├── data.json                       ← auto-updated by GitHub Actions
└── .github/workflows/fetch.yml     ← the cron job
```

---

## Step 1 — Create a GitHub account (skip if you have one)

Go to https://github.com/signup — it's free.

---

## Step 2 — Create a new repository

1. Go to https://github.com/new
2. Repository name: `conflict-monitor`  (or anything you like)
3. Set it to **Public** ← important, GitHub Pages only works on public repos on free tier
4. Check "Add a README file"
5. Click **Create repository**

---

## Step 3 — Upload the files

### Option A — GitHub web UI (no Git needed)

1. In your new repo, click **Add file → Upload files**
2. Upload `index.html` and `data.json`
3. Click **Commit changes**

Then create the workflow folder:
1. Click **Add file → Create new file**
2. In the filename box, type exactly: `.github/workflows/fetch.yml`
   (GitHub will auto-create the folders as you type the slashes)
3. Paste the entire contents of `fetch.yml` into the editor
4. Click **Commit changes**

### Option B — Git command line

```bash
git clone https://github.com/YOUR_USERNAME/conflict-monitor.git
cd conflict-monitor

# Copy the 3 files into this folder, then:
git add .
git commit -m "Initial deploy"
git push
```

---

## Step 4 — Add your NewsAPI secret

Your API key must be stored as a GitHub Secret so it's never public.

1. Go to your repo → **Settings** → **Secrets and variables** → **Actions**
2. Click **New repository secret**
3. Name:  `NEWS_API_KEY`
4. Value: paste your NewsAPI key (get one free at https://newsapi.org/register)
5. Click **Add secret**

---

## Step 5 — Enable GitHub Pages

1. Go to your repo → **Settings** → **Pages** (left sidebar)
2. Under "Source", select **Deploy from a branch**
3. Branch: `main` / `(root)`
4. Click **Save**

GitHub will give you a URL like:
`https://YOUR_USERNAME.github.io/conflict-monitor/`

It takes about 1–2 minutes to go live the first time.

---

## Step 6 — Trigger the first data fetch

The cron runs automatically every 6 hours, but you want data now.

1. Go to your repo → **Actions** tab
2. Click **Fetch conflict news** (left sidebar)
3. Click **Run workflow** → **Run workflow** (green button)
4. Watch it run — takes about 30–60 seconds
5. When it turns green, your `data.json` is populated and your site is live

---

## Step 7 — Visit your site

Go to: `https://YOUR_USERNAME.github.io/conflict-monitor/`

That's it. You're live. The workflow runs at 00:00, 06:00, 12:00, 18:00 UTC automatically.

---

## Troubleshooting

**"data.json is empty / no articles showing"**
→ Go to Actions tab, check the workflow run logs for errors.
→ Most likely cause: NEWS_API_KEY secret is wrong. Double-check it.

**"GitHub Actions failed: API key invalid"**
→ Go to Settings → Secrets → delete NEWS_API_KEY and re-add it.

**"Site shows 404"**
→ GitHub Pages takes 2–3 minutes after first commit. Wait and refresh.
→ Make sure repo is Public (Settings → General → scroll down → "Danger Zone" → Change visibility).

**"I want a custom domain"**
→ Settings → Pages → Custom domain. Point your domain's CNAME to `YOUR_USERNAME.github.io`.
→ GitHub provides free HTTPS via Let's Encrypt automatically.

---

## Usage limits (free tier)

| Resource | Limit | Your usage |
|---|---|---|
| NewsAPI calls/day | 100 | 4×6 = 24/day (auto) |
| GitHub Actions minutes/month | 2,000 | ~2 min/run × 4/day × 30 = ~240/month |
| GitHub Pages bandwidth | 100GB/month | negligible for a news dashboard |

You're using roughly 12% of your NewsAPI limit and 12% of your Actions limit.
Plenty of headroom. Completely free forever.

---

## Customising

**Change refresh frequency** — edit the cron line in `fetch.yml`:
- Every 4 hours: `0 */4 * * *`
- Every 12 hours: `0 */12 * * *`
- Every hour (uses 6×24=144 calls/day — close to limit): `0 * * * *`

**Add a new conflict topic** — in `fetch.yml`, add a new fetch line:
```bash
SUDAN=$(fetch_query "Sudan civil war RSF")
```
Then add `for a in sudan: a["_cat"] = "sudan"` in the Python block.

**Change the look** — edit `index.html`. The CSS variables are all at the top under `:root {}`.
