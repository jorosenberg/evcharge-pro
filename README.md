# EVCharge Pro — Deployment Guide

## Keeping your API keys secret on GitHub Pages

Your OCM and Firebase keys are stored as **GitHub Secrets** — encrypted,
never visible in the repo. GitHub Actions injects them at deploy time into
a `config.js` file that only exists in the deployed `gh-pages` branch.

---

## One-time setup

### 1 · Create your GitHub repo
```bash
git init
git remote add origin https://github.com/YOUR_USERNAME/evcharge-pro.git
```

### 2 · Add your secrets
Go to your repo on GitHub → **Settings → Secrets and variables → Actions → New repository secret**

Add these two secrets:

| Secret name | Value |
|---|---|
| `OCM_API_KEY` | Your key from openchargemap.org/site/profile |
| `FIREBASE_CONFIG` | Your Firebase config as a JSON object (see below) |

**`FIREBASE_CONFIG` value** — paste the raw JSON object (no quotes around it):
```json
{
  "apiKey": "AIza...",
  "authDomain": "your-app.firebaseapp.com",
  "projectId": "your-app",
  "storageBucket": "your-app.appspot.com",
  "messagingSenderId": "123456789",
  "appId": "1:123:web:abc"
}
```

### 3 · Enable GitHub Pages
Repo → **Settings → Pages → Source: Deploy from branch → `gh-pages` / `/ (root)`**

### 4 · Push your code
```bash
git add index.html .github/ .gitignore README.md
git commit -m "Initial deploy"
git push -u origin main
```

The Action runs automatically. After ~60 seconds your app is live at:
`https://YOUR_USERNAME.github.io/evcharge-pro/`

---

## How it works

```
main branch          gh-pages branch (deployed)
─────────────        ──────────────────────────
index.html     ──►   index.html
.github/             config.js  ← generated from secrets, never in main
.gitignore
```

1. You push to `main`
2. GitHub Actions checks out your code
3. It writes `config.js` using your encrypted secrets
4. It deploys everything (including `config.js`) to `gh-pages`
5. `config.js` writes the keys into `localStorage` on first page load
6. Keys persist in the user's browser for future visits

---

## Local development

When you open `index.html` locally, `config.js` won't exist —
the app silently falls back to **localStorage / manual settings**
(the ⚙ Settings button in the top bar). Enter your keys there once
and they're saved locally.

---

## Rotating keys

Just update the GitHub Secret value → re-run the Action (or push any commit).
The new `config.js` is deployed and overwrites the old one.
