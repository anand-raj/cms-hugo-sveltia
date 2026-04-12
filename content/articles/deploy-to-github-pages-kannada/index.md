---
title: "Hugo ಸೈಟ್ ಅನ್ನು GitHub Pages ಗೆ ಹೇಗೆ ಡಿಪ್ಲೋಯ್ ಮಾಡಬೇಕು"
date: 2026-04-12T10:20:00+05:30
lastmod: 2026-04-12T10:20:00+05:30
draft: false
description: "GitHub Pages ಉಚಿತ, ವಿಶ್ವಾಸಾರ್ಹ ಸ್ಟ್ಯಾಟಿಕ್ ಸೈಟ್ ಹೋಸ್ಟಿಂಗ್ ನೇರ repository ನಿಂದ ನೀಡುತ್ತದೆ. GitHub Actions ಬಳಸಿ ನಿಮ್ಮ Hugo ಸೈಟ್ ಅನ್ನು ಕೆಲವೇ ಹಂತಗಳಲ್ಲಿ ಸ್ವಯಂಚಾಲಿತವಾಗಿ ಹೇಗೆ ಡಿಪ್ಲೋಯ್ ಮಾಡಬೇಕು ಎಂದು ತಿಳಿಯಿರಿ."
author: "Admin"
categories: ["Tech"]
tags: ["hugo", "github pages", "deployment", "github actions", "jamstack", "kannada"]
image: "https://images.unsplash.com/photo-1654277041218-84424c78f0ae?w=1200&fm=webp&q=80"
image_caption: "Photo by Rabaitul Azad on Unsplash"
toc: true
comments: true
extra_font: "https://fonts.googleapis.com/css2?family=Baloo+Tamma+2:wght@400;600;700&display=swap"
---

<style>
.post-content, .post-title, .post-description, .post-toc {
  font-family: 'Baloo Tamma 2', system-ui, sans-serif;
}
</style>

GitHub Pages ಒಂದು ಸ್ಟ್ಯಾಟಿಕ್ ಸೈಟ್ ಹೋಸ್ಟ್ ಮಾಡಲು ಅತ್ಯಂತ ಸರಳ ಮತ್ತು ವೆಚ್ಚ-ಪರಿಣಾಮಕಾರಿ ಮಾರ್ಗ. GitHub Actions ನೊಂದಿಗೆ ಸಂಯೋಜಿಸಿ, `main` ಗೆ ಪ್ರತಿ push ನಲ್ಲಿ ನಿಮ್ಮ Hugo ಸೈಟ್ ಸ್ವಯಂಚಾಲಿತವಾಗಿ ಬಿಲ್ಡ್ ಮತ್ತು ಡಿಪ್ಲೋಯ್ ಆಗುವ ಸಂಪೂರ್ಣ ಸ್ವಯಂಚಾಲಿತ ಪೈಪ್‌ಲೈನ್ ರಚಿಸಬಹುದು — ಯಾವ ಸರ್ವರ್ ನಿರ್ವಹಣೆ ಇಲ್ಲದೆ.

<!--more-->

## ಪೂರ್ವ ಅಗತ್ಯಗಳು

ಪ್ರಾರಂಭಿಸುವ ಮೊದಲು, ನಿಮ್ಮ ಬಳಿ ಇರಬೇಕು:

- GitHub repository ನಲ್ಲಿ Hugo ಸೈಟ್
- Hugo Extended (SCSS ಬೆಂಬಲಕ್ಕಾಗಿ)
- GitHub Pages URL ಗೆ `baseURL` ಹೊಂದಿಸಿದ `hugo.toml`:

```toml
baseURL = 'https://yourusername.github.io/your-repo-name/'
```

## ಹಂತ 1 — GitHub Pages ಸಕ್ರಿಯಗೊಳಿಸಿ

1. GitHub ನಲ್ಲಿ ನಿಮ್ಮ repository ಗೆ ಹೋಗಿ
2. **Settings → Pages** ಕ್ಲಿಕ್ ಮಾಡಿ
3. **Source** ಅಡಿ, **GitHub Actions** ಆಯ್ಕೆ ಮಾಡಿ

ಇದು GitHub ಗೆ ನಿರ್ಮಿತ ಸೈಟ್ push ಮಾಡಲು ಒಂದು workflow ಇರಬೇಕು ಎಂದು ತಿಳಿಸುತ್ತದೆ.

## ಹಂತ 2 — GitHub Actions Workflow ರಚಿಸಿ

ನಿಮ್ಮ repository ನಲ್ಲಿ `.github/workflows/deploy.yml` ಫೈಲ್ ರಚಿಸಿ:

```yaml
name: Deploy Hugo to GitHub Pages

on:
  push:
    branches:
      - main

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: 'latest'
          extended: true

      - name: Build
        run: hugo --minify

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public

  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

## ಹಂತ 3 — Push ಮಾಡಿ ಮತ್ತು ಪರಿಶೀಲಿಸಿ

Workflow ಫೈಲ್ Commit ಮಾಡಿ push ಮಾಡಿ:

```bash
git add .github/workflows/deploy.yml
git commit -m "ci: add GitHub Pages deployment workflow"
git push origin main
```

ನಿಮ್ಮ repository ನಲ್ಲಿ **Actions** ಗೆ ಹೋಗಿ — workflow ಚಾಲನೆ ಆಗುವುದನ್ನು ನೋಡಬಹುದು. ಮುಗಿದ ನಂತರ (ಸಾಮಾನ್ಯವಾಗಿ ಒಂದು ನಿಮಿಷಕ್ಕಿಂತ ಕಡಿಮೆ), ನಿಮ್ಮ ಸೈಟ್ `https://yourusername.github.io/your-repo-name/` ನಲ್ಲಿ ಲೈವ್ ಆಗಿರುತ್ತದೆ.

## ಪೈಪ್‌ಲೈನ್ ಹೇಗೆ ಕೆಲಸ ಮಾಡುತ್ತದೆ?

```
main ಗೆ Push
     ↓
GitHub Actions ಚಾಲಿತ
     ↓
Hugo Extended ಸೈಟ್ ನಿರ್ಮಿಸುತ್ತದೆ → public/
     ↓
public/ Pages artifact ಆಗಿ upload
     ↓
GitHub artifact CDN edge ಗೆ deploy ಮಾಡುತ್ತದೆ
     ↓
ಸೈಟ್ github.io URL ನಲ್ಲಿ ಲೈವ್
```

## Base URL ನಿರ್ವಹಣೆ

ನಿಮ್ಮ repo ಅನ್ನು `yourusername.github.io` ಎಂದು ಹೆಸರಿಸಿದರೆ (user/org ಸೈಟ್), `baseURL` ಕೇವಲ `https://yourusername.github.io/`.

ಅದೊಂದು project repo ಆದರೆ (ಉದಾ. `my-blog`), URL ಒಂದು subdirectory ಹೊಂದಿರುತ್ತದೆ: `https://yourusername.github.io/my-blog/`. Hugo ನ `baseURL` ನಿಖರವಾಗಿ ಹೊಂದಿಕೆ ಆಗಬೇಕು, ಇಲ್ಲದಿದ್ದರೆ CSS ಮತ್ತು ಲಿಂಕ್‌ಗಳು ಮುರಿಯುತ್ತವೆ.

## ತೀರ್ಮಾನ

GitHub Actions ಬಿಲ್ಡ್ ಮತ್ತು ಡಿಪ್ಲೋಯ್ ನಿರ್ವಹಿಸುವುದರಿಂದ, ನಿಮ್ಮ Hugo ಸೈಟ್ ಪ್ರತಿ commit ನಲ್ಲಿ ಸ್ವಯಂಚಾಲಿತವಾಗಿ ಪ್ರಕಟವಾಗುತ್ತದೆ. ನಿರ್ವಹಿಸಲು ಬಿಲ್ಡ್ ಸರ್ವರ್ ಇಲ್ಲ, ತಿರುಗಿಸಲು deploy keys ಇಲ್ಲ — ಬರೀ push ಮಾಡಿದರೆ ಸೆಕೆಂಡ್‌ಗಳಲ್ಲಿ ಸೈಟ್ ನವೀಕರಣ ಆಗುತ್ತದೆ.

---

**ಮೂಲಗಳು:**

- [Hugo Hosting on GitHub Pages](https://gohugo.io/hosting-and-deployment/hosting-on-github/)
- [GitHub Actions — actions/deploy-pages](https://github.com/actions/deploy-pages)
- [peaceiris/actions-hugo](https://github.com/peaceiris/actions-hugo)
