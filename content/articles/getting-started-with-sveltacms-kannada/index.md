---
title: "Sveltia CMS ಪ್ರಾರಂಭ: Git ಆಧಾರಿತ ಸಂಪಾದನಾ ವ್ಯವಸ್ಥೆ"
date: 2026-04-12T10:30:00+05:30
lastmod: 2026-04-12T10:30:00+05:30
draft: false
description: "Sveltia CMS ಒಂದು ಆಧುನಿಕ, Git ಆಧಾರಿತ headless CMS. ಇದು ಏನು, Netlify/Decap CMS ಗಿಂತ ಹೇಗೆ ಭಿನ್ನ, ಮತ್ತು ನಿಮ್ಮ Hugo ಸೈಟ್ ಗೆ ಕೆಲವೇ ನಿಮಿಷಗಳಲ್ಲಿ ಹೇಗೆ ಸಂಪರ್ಕಿಸಬೇಕು ಎಂದು ತಿಳಿಯಿರಿ."
author: Admin
categories:
  - Tech
tags:
  - cms
  - sveltia
  - jamstack
  - hugo
  - git
  - kannada
image: 'images/sveltia-logo.svg'
image_caption: ''
toc: true
comments: true
extra_font: "https://fonts.googleapis.com/css2?family=Baloo+Tamma+2:wght@400;600;700&display=swap"
---

<style>
.post-content, .post-title, .post-description, .post-toc {
  font-family: 'Baloo Tamma 2', system-ui, sans-serif;
}
</style>

## ಪೀಠಿಕೆ

ನೀವು Hugo, Astro ಅಥವಾ Eleventy ನಿಂದ ಸ್ಟ್ಯಾಟಿಕ್ ಸೈಟ್ ನಿರ್ಮಿಸಿದ್ದರೆ, ವಿಷಯ ಸಂಪಾದನೆ ಸಮಸ್ಯೆ ಎದುರಿಸಿರಬಹುದು: ನಿಮ್ಮ ಸೈಟ್ ವೇಗ ಮತ್ತು ಸುರಕ್ಷಿತ, ಆದರೆ ತಾಂತ್ರಿಕ ಜ್ಞಾನ ಇಲ್ಲದ ಸಂಪಾದಕರಿಗೆ Markdown ಫೈಲ್‌ಗಳು ಮತ್ತು Git commits ಇಲ್ಲದೆ ವಿಷಯ ಸೇರಿಸಲು ಅಥವಾ ನವೀಕರಿಸಲು ಮಾರ್ಗ ಇಲ್ಲ.

Git ಆಧಾರಿತ CMS ಇದನ್ನೇ ಪರಿಹರಿಸುತ್ತದೆ. **Sveltia CMS** ಇಂದು ಲಭ್ಯವಿರುವ ಅತ್ಯುತ್ತಮ ಆಯ್ಕೆಗಳಲ್ಲಿ ಒಂದು — ಉಚಿತ, ಮುಕ್ತ-ಮೂಲ, ಬ್ರೌಸರ್ ಆಧಾರಿತ ವಿಷಯ ಸಂಪಾದಕ ಅದು ನೇರವಾಗಿ ನಿಮ್ಮ Git repository ಗೆ commit ಮಾಡುತ್ತದೆ, ಯಾವ ಸರ್ವರ್, ಯಾವ ಡೇಟಾಬೇಸ್, ಯಾವ ಮಾಸಿಕ ಬಿಲ್ ಇಲ್ಲದೆ.

<!--more-->

## Sveltia CMS ಎಂದರೇನು?

[Sveltia CMS](https://sveltiacms.app/en/) ಒಂದು headless ವಿಷಯ ನಿರ್ವಹಣಾ ವ್ಯವಸ್ಥೆ ಅದು ಸಂಪೂರ್ಣವಾಗಿ ಬ್ರೌಸರ್‌ನಲ್ಲಿ ಚಾಲಿಸುತ್ತದೆ. ಸಂಪಾದಕರು ನಿಮ್ಮ ಸೈಟ್‌ನ `/admin` ಪುಟಕ್ಕೆ ಭೇಟಿ ನೀಡಿ, GitHub (ಅಥವಾ GitLab) ಮೂಲಕ ಲಾಗಿನ್ ಆಗಿ, ವಿಷಯ ರಚಿಸಿ ಸಂಪಾದಿಸಲು ಸ್ವಚ್ಛ UI ಪಡೆಯುತ್ತಾರೆ. ಪ್ರತಿ save ಗಿಂತ ಹಿಂದೆ, ನಿಮ್ಮ repository ಗೆ ಒಂದು Git commit ಸೇರುತ್ತದೆ.

ಇದು **Netlify CMS** (ಈಗ Decap CMS ಎಂದು ಕರೆಯಲಾಗುತ್ತದೆ) ನ ನೇರ ಉತ್ತರಾಧಿಕಾರಿ, ನೂರಾರು ದೀರ್ಘ-ಕಾಲದ ಸಮಸ್ಯೆಗಳನ್ನು ಪರಿಹರಿಸುತ್ತದೆ — ನಿಧಾನ ಲೋಡ್ ಸಮಯ, ಮುರಿದ media ನಿರ್ವಹಣೆ, ಕಳಪೆ i18n ಬೆಂಬಲ — ಅಸ್ತಿತ್ವದಲ್ಲಿರುವ `config.yml` ಸ್ವರೂಪಕ್ಕೆ ಹೊಂದಿಕೊಂಡಿದ್ದರೂ.

ಮುಖ್ಯ ಗುಣಲಕ್ಷಣಗಳು:

- **Framework-agnostic** — Hugo, Astro, Eleventy, Jekyll ಮತ್ತು ಇತರರೊಂದಿಗೆ ಕೆಲಸ ಮಾಡುತ್ತದೆ
- **Git ಆಧಾರಿತ** — ಬಾಹ್ಯ ಡೇಟಾಬೇಸ್ ಇಲ್ಲ; ನಿಮ್ಮ ವಿಷಯ repo ದಲ್ಲಿ ಇರುತ್ತದೆ
- **ಬ್ರೌಸರ್-ಮಾತ್ರ** — ಚಾಲಿಸಲು ಅಥವಾ ನಿರ್ವಹಿಸಲು backend ಪ್ರಕ್ರಿಯೆ ಇಲ್ಲ
- **i18n-ಮೊದಲ** — ಮೊದಲ ದಿನದಿಂದ ಅಂತರ್ನಿರ್ಮಿತ ಬಹು-ಭಾಷಾ ಬೆಂಬಲ
- **ವೇಗ** — ಸಂಪಾದಕ ಮಿಲಿಸೆಕೆಂಡ್‌ಗಳಲ್ಲಿ ಲೋಡ್ ಆಗುತ್ತದೆ

## ಹೋಲಿಕೆ

| ವೈಶಿಷ್ಟ್ಯ | Sveltia CMS | Decap CMS | Forestry / Tina |
|---|---|---|---|
| ಮುಕ್ತ ಮೂಲ | ಹೌದು | ಹೌದು | ಭಾಗಶಃ |
| Backend ಅಗತ್ಯ | ಇಲ್ಲ | ಇಲ್ಲ | ಇಲ್ಲ |
| i18n ಬೆಂಬಲ | ಅತ್ಯುತ್ತಮ | ಸೀಮಿತ | ಸೀಮಿತ |
| Media ನಿರ್ವಹಣೆ | ಸುಧಾರಿತ | ದೋಷಪೂರಿತ | ಉತ್ತಮ |
| ಚಟುವಟಿಕೆ | ಸಕ್ರಿಯ | ಕನಿಷ್ಠ | ಸಕ್ರಿಯ |
| ವೆಚ್ಚ | ಉಚಿತ | ಉಚಿತ | ಉಚಿತ ಶ್ರೇಣಿ |

## Hugo ನೊಂದಿಗೆ Sveltia CMS ಹೊಂದಿಸುವುದು

### 1. Admin ಫೈಲ್‌ಗಳು ರಚಿಸಿ

ನಿಮ್ಮ `static/admin/` ಫೋಲ್ಡರ್‌ನಲ್ಲಿ ಎರಡು ಫೈಲ್‌ಗಳು ಸೇರಿಸಿ:

**`static/admin/index.html`**

```html
<!doctype html>
<html>
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Content Manager</title>
</head>
<body>
  <script src="https://unpkg.com/@sveltia/cms/dist/sveltia-cms.js"></script>
</body>
</html>
```

**`static/admin/config.yml`**

```yaml
backend:
  name: github
  repo: your-username/your-repo
  branch: main

media_folder: static/images
public_folder: /images

collections:
  - name: articles
    label: Articles
    folder: content/articles
    create: true
    fields:
      - { label: Title, name: title, widget: string }
      - { label: Date, name: date, widget: datetime }
      - { label: Description, name: description, widget: text }
      - { label: Body, name: body, widget: markdown }
```

### 2. GitHub ನಲ್ಲಿ OAuth App ಸಕ್ರಿಯಗೊಳಿಸಿ

Sveltia CMS OAuth ಮೂಲಕ ಸಂಪಾದಕರ ಗುರುತನ್ನು ಪರಿಶೀಲಿಸುತ್ತದೆ. ಎರಡು ಆಯ್ಕೆಗಳಿವೆ:

**Option A — Netlify Identity (ಸರಳ)**
ನಿಮ್ಮ ಸೈಟ್ Netlify ನಲ್ಲಿ deploy ಆಗಿದ್ದರೆ, ಸೈಟ್ ಸೆಟ್ಟಿಂಗ್‌ಗಳಲ್ಲಿ Identity ಸಕ್ರಿಯಗೊಳಿಸಿ — ಹೆಚ್ಚಿನ config ಇಲ್ಲದೆ Sveltia CMS ಕೆಲಸ ಮಾಡುತ್ತದೆ.

**Option B — Cloudflare Workers (ಸ್ವ-ಹೋಸ್ಟ್)**
GitHub Pages ಅಥವಾ Cloudflare Pages deployments ಗಾಗಿ, [Sveltia CMS Auth](https://github.com/sveltia/sveltia-cms-auth) Cloudflare Worker deploy ಮಾಡಿ:

```yaml
backend:
  name: github
  repo: your-username/your-repo
  branch: main
  base_url: https://your-worker.your-subdomain.workers.dev
```

### 3. Hugo ವಿಷಯ ಸಂಗ್ರಹಗಳು ಹೊಂದಿಸಿ

`config.yml` ನಲ್ಲಿ `collections` ಬ್ಲಾಕ್ ನೇರವಾಗಿ ನಿಮ್ಮ `content/` ಫೋಲ್ಡರ್‌ಗಳಿಗೆ ಮ್ಯಾಪ್ ಆಗುತ್ತದೆ. Pageಒಂದು Hugo ಸೈಟ್‌ಗೆ ವಿಶಿಷ್ಟ collection:

```yaml
collections:
  - name: articles
    label: Articles
    folder: content/articles
    create: true
    path: "{{slug}}/index"
    media_folder: images
    public_folder: images
    fields:
      - { label: Title,       name: title,       widget: string }
      - { label: Date,        name: date,         widget: datetime }
      - { label: Description, name: description,  widget: text }
      - { label: Draft,       name: draft,        widget: boolean, default: false }
      - { label: Author,      name: author,       widget: string }
      - { label: Tags,        name: tags,         widget: list }
      - { label: Image,       name: image,        widget: image, required: false }
      - { label: Body,        name: body,         widget: markdown }
```

`path: "{{slug}}/index"` ಸೆಟ್ಟಿಂಗ್ Sveltia CMS ಗೆ page bundle ಡೈರೆಕ್ಟರಿಗಳು ರಚಿಸಲು ಹೇಳುತ್ತದೆ (`content/articles/my-post/index.md`) — Hugo ನ page bundle ರಚನೆಗೆ ಹೊಂದಿಕೆ ಆಗುತ್ತದೆ.

## ಸಂಪಾದಕೀಯ ವರ್ಕ್‌ಫ್ಲೋ

ಹೊಂದಿಸಿದ ನಂತರ, ಸಂಪಾದಕರು:

1. `yoursite.com/admin` ಗೆ ಹೋಗಬಹುದು
2. GitHub ಖಾತೆಯಿಂದ ಲಾಗಿನ್ ಆಗಬಹುದು
3. ಪಕ್ಕದ ಪಟ್ಟಿಯಿಂದ ಒಂದು collection (Articles, News, ಇತ್ಯಾದಿ) ಆಯ್ಕೆ ಮಾಡಬಹುದು
4. ಒಂದು ಸಮೃದ್ಧ Markdown ಸಂಪಾದಕದಲ್ಲಿ entries ರಚಿಸಿ ಸಂಪಾದಿಸಬಹುದು
5. **Publish** ಕ್ಲಿಕ್ ಮಾಡಬಹುದು — Sveltia CMS ನೇರವಾಗಿ ನಿಮ್ಮ repo ಗೆ commit ಮಾಡಿ CI/CD ಪೈಪ್‌ಲೈನ್ ಚಾಲಿಸುತ್ತದೆ

Drafts ಅನ್ನು editorial workflow ಸಕ್ರಿಯಗೊಳಿಸಿದರೆ Git branches ಆಗಿ ಉಳಿಸಲಾಗುತ್ತದೆ:

```yaml
publish_mode: editorial_workflow
```

ಇದು ಸರಿಯಾದ ಪರಿಶೀಲನಾ ಪ್ರಕ್ರಿಯೆ ಕೊಡುತ್ತದೆ: Draft → In Review → Ready → Published.

## ತೀರ್ಮಾನ

Sveltia CMS Hugo + GitHub Pages ಅಥವಾ Cloudflare Pages ವರ್ಕ್‌ಫ್ಲೋ ನಲ್ಲಿ ಸ್ವಚ್ಛವಾಗಿ ಹೊಂದಿಕೊಳ್ಳುತ್ತದೆ. ತಾಂತ್ರಿಕ ಜ್ಞಾನ ಇಲ್ಲದ ಸಂಪಾದಕರಿಗೆ ಸ್ವಚ್ಛ ಸಂಪಾದನಾ interface ನೀಡುತ್ತದೆ, ನಿಮ್ಮ ವಿಷಯ Git ನಲ್ಲಿ ಸಂಪೂರ್ಣ version-controlled ಆಗಿ ಇರುತ್ತದೆ — vendor lock-in ಇಲ್ಲ, ಮಾಸಿಕ subscription ಇಲ್ಲ.

---

**ಮೂಲಗಳು:**

- [Sveltia CMS ದಸ್ತಾವೇಜು](https://sveltiacms.app/en/docs)
- [Sveltia CMS GitHub Repository](https://github.com/sveltia/sveltia-cms)
- [Netlify/Decap CMS ನಿಂದ ವಲಸೆ](https://sveltiacms.app/en/docs/migration/netlify-decap-cms)
- [Sveltia CMS Auth (Cloudflare Worker)](https://github.com/sveltia/sveltia-cms-auth)
