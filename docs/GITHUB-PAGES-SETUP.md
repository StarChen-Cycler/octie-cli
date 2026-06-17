# GitHub Pages Setup Guide

## Repository Structure

```
octie-cli-site/
├── index.html          # Main landing page (GitHub Pages entry point)
├── README.md           # Repository documentation
├── .gitignore          # Git ignore rules
├── skills/             # Claude Code skills
│   ├── octie-install/
│   ├── octie-research/
│   ├── octie-plan/
│   ├── octie-dev/
│   ├── octie-fix/
│   └── octie-refine/
└── docs/               # Additional documentation
    └── GITHUB-PAGES-SETUP.md
```

---

## Step-by-Step Setup

### Step 1: Initialize Local Repository

```bash
cd C:/Users/LENOVO/Downloads/core-files/octie-cli-site

# Initialize git (already done)
git init

# Add all files
git add .

# Initial commit
git commit -m "Initial commit: Octie CLI landing page and skills"
```

### Step 2: Connect to GitHub

```bash
# Add remote origin
git remote add origin https://github.com/StarChen-Cycler/octie-cli.git

# Verify remote
git remote -v
```

### Step 3: Push to GitHub

```bash
# Push to main branch
git branch -M main
git push -u origin main
```

### Step 4: Enable GitHub Pages

1. Go to https://github.com/StarChen-Cycler/octie-cli
2. Click **Settings** (top navigation)
3. Scroll down to **Pages** (left sidebar)
4. Under **Source**, select:
   - **Source**: Deploy from a branch
   - **Branch**: `main`
   - **Folder**: `/ (root)`
5. Click **Save**

### Step 5: Verify Deployment

- GitHub will build and deploy your site
- Wait 2-5 minutes for first deployment
- Visit: https://starchen-cycler.github.io/octie-cli/

---

## Alternative: Use `docs/` Folder

If you want to keep the landing page separate from the README:

### Option A: Move index.html to docs/

```bash
# Move index.html to docs/
mv index.html docs/

# Update GitHub Pages settings:
# Source: Deploy from a branch
# Branch: main
# Folder: /docs
```

### Option B: Use GitHub Actions (Recommended)

Create `.github/workflows/deploy.yml`:

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [ main ]

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Pages
        uses: actions/configure-pages@v4

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: '.'

      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

---

## Custom Domain (Optional)

To use a custom domain (e.g., `octie.dev`):

1. Add a `CNAME` file to the root:
   ```
   octie.dev
   ```

2. In GitHub Settings > Pages:
   - Add your custom domain
   - Enable "Enforce HTTPS"

3. Configure DNS:
   - Add CNAME record pointing to `starchen-cycler.github.io`

---

## Troubleshooting

### Site not loading

- Check GitHub Pages is enabled in Settings
- Verify `index.html` is in the correct location
- Wait 5-10 minutes for first deployment
- Check Actions tab for build errors

### 404 errors

- Ensure `index.html` is at the root (or `/docs` if using docs folder)
- Check file names are lowercase
- Verify no typos in file paths

### Styling not loading

- Check CSS is inline in `index.html` (not external file)
- Verify font URLs are accessible
- Clear browser cache

---

## URLs After Setup

| Resource | URL |
|----------|-----|
| **Repository** | https://github.com/StarChen-Cycler/octie-cli |
| **GitHub Pages** | https://starchen-cycler.github.io/octie-cli/ |
| **npm Package** | https://www.npmjs.com/package/octie-cli |

---

## Updating the Site

```bash
# Make changes to files
# Then commit and push
git add .
git commit -m "Update landing page"
git push

# GitHub Actions will automatically redeploy
```

---

## File Sizes

| File | Size | Purpose |
|------|------|---------|
| index.html | ~50KB | Landing page (self-contained) |
| README.md | ~5KB | Repository documentation |
| skills/ | ~25KB | Claude Code skills (6 skills) |
| **Total** | ~80KB | Complete package |
