---
title: "ಈ ಸೈಟ್‌ನಲ್ಲಿ ಹುಡುಕಾಟ ಹೇಗೆ ಕೆಲಸ ಮಾಡುತ್ತದೆ"
date: 2026-04-12T10:50:00+05:30
lastmod: 2026-04-12T10:50:00+05:30
draft: false
description: "ಸೈಟ್‌ನ client-side ಹುಡುಕಾಟ ಹೇಗೆ ನಿರ್ಮಿಸಲಾಗಿದೆ ಎಂಬ ಒಳನೋಟ — Hugo ನ JSON ಔಟ್‌ಪುಟ್, Fuse.js fuzzy matching, ಮತ್ತು ಶೂನ್ಯ backend infrastructure ಬಳಸಿ."
author: "Admin"
categories:
  - Tech
tags:
  - hugo
  - search
  - fuse.js
  - jamstack
  - kannada
toc: true
image: "https://images.unsplash.com/photo-1555421689-d68471e189f2?w=1200&fm=webp&q=80"
image_caption: ""
comments: true
extra_font: "https://fonts.googleapis.com/css2?family=Baloo+Tamma+2:wght@400;600;700&display=swap"
---

<style>
.post-content, .post-title, .post-description, .post-toc {
  font-family: 'Baloo Tamma 2', system-ui, sans-serif;
}
</style>

ಈ ಸೈಟ್‌ನ ಹುಡುಕಾಟ ಸಂಪೂರ್ಣವಾಗಿ ಬ್ರೌಸರ್‌ನಲ್ಲಿ ಚಾಲಿಸುತ್ತದೆ — ಸರ್ವರ್ ಇಲ್ಲ, ಡೇಟಾಬೇಸ್ ಇಲ್ಲ, ಮೊದಲ ಪೇಜ್ ಲೋಡ್ ನಂತರ API calls ಇಲ್ಲ. ಇದು ಹೇಗೆ ನಿರ್ಮಿಸಲಾಗಿದೆ ಎಂಬ ವಿವರ ಇಲ್ಲಿದೆ.

## ಸ್ಟ್ಯಾಟಿಕ್ ಸೈಟ್‌ಗಳಲ್ಲಿ ಹುಡುಕಾಟ ಸಮಸ್ಯೆ

ಈ ಸೈಟ್‌ನಂತಹ ಸ್ಟ್ಯಾಟಿಕ್ ಸೈಟ್‌ಗಳು GitHub Pages ನಿಂದ ಒದಗಿಸಲಾದ ಕೇವಲ HTML, CSS ಮತ್ತು JavaScript ಫೈಲ್‌ಗಳು. ಒಂದು query ಸ್ವೀಕರಿಸಿ, ಡೇಟಾಬೇಸ್‌ನಲ್ಲಿ ನೋಡಿ, ಫಲಿತಾಂಶ ಹಿಂತಿರುಗಿಸಲು ಚಾಲನೆ ಸರ್ವರ್ ಇಲ್ಲ. ಪ್ರತಿ ಸಾಂಪ್ರದಾಯಿಕ ಹುಡುಕಾಟ ಪರಿಹಾರ ಒಂದು backend ಇದೆ ಎಂದು ಊಹಿಸುತ್ತದೆ.

ಸ್ಟ್ಯಾಟಿಕ್ ಸೈಟ್‌ಗಳಿಗೆ ಎರಡು ಸಾಮಾನ್ಯ ವಿಧಾನಗಳಿವೆ:

1. **ಬಾಹ್ಯ ಹುಡುಕಾಟ ಸೇವೆಗಳು** — Algolia, Typesense ನಂತಹ ಸೇವೆಗಳಿಗೆ ವಿಷಯ ಕಳಿಸಿ. ವೇಗ ಮತ್ತು ಶಕ್ತಿಶಾಲಿ, ಆದರೆ ಮೂರನೇ ವ್ಯಕ್ತಿ ಅವಲಂಬನೆ ಮತ್ತು ಹೆಚ್ಚಾಗಿ ವೆಚ್ಚ ಸೇರಿಸುತ್ತದೆ.
2. **Client-side ಹುಡುಕಾಟ** — ಬಿಲ್ಡ್ ಸಮಯದಲ್ಲಿ ಹುಡುಕಾಟ index ನಿರ್ಮಿಸಿ, JSON ಫೈಲ್ ಆಗಿ ಕಳಿಸಿ, JavaScript ಬಳಸಿ ಬ್ರೌಸರ್‌ನಲ್ಲಿ ಹೊಂದಿಸಿ.

ನಾವು 2ನೇ ವಿಧಾನ ಬಳಸುತ್ತೇವೆ. ಇದು ಈ ಗಾತ್ರದ ಸೈಟ್‌ಗೆ ಚೆನ್ನಾಗಿ ಕೆಲಸ ಮಾಡುತ್ತದೆ ಮತ್ತು ಎಲ್ಲ ಸ್ವ-ಸಮಾವಿಷ್ಟ ಆಗಿ ಇರಿಸುತ್ತದೆ.

## ಹಂತ 1 — ಹುಡುಕಾಟ Index

ಪ್ರತಿ Hugo ಬಿಲ್ಡ್ ಸಮಯದಲ್ಲಿ, ಸೈಟ್‌ನ root ನಲ್ಲಿ `index.json` ಫೈಲ್ ರಚಿಸಲಾಗುತ್ತದೆ. ಇದು ಪ್ರತಿ article, news item ಮತ್ತು event ನ flat array ಒಳಗೊಂಡಿದೆ:

```json
[
  {
    "title": "ಕಾವೇರಿ ಜಲಾಶಯ ಮಟ್ಟ ಬೇಸಿಗೆಯಲ್ಲಿ 62%",
    "description": "ಕರ್ನಾಟಕದ ನಾಲ್ಕು ಪ್ರಮುಖ ಕಾವೇರಿ ಜಲಾಶಯಗಳಲ್ಲಿ ನೀರಿನ ಮಟ್ಟ...",
    "content": "ನಾಲ್ಕು ಪ್ರಮುಖ ಕಾವೇರಿ ಜಲಾಶಯಗಳಲ್ಲಿ ನೀರಿನ ಮಟ್ಟ...",
    "url": "/news/cauvery-water-levels/",
    "section": "news",
    "date": "Apr 7, 2026"
  }
]
```

ಇದು Hugo ಕಾನ್ಫಿಗರೇಶನ್‌ನ ಎರಡು ಬದಲಾವಣೆಗಳಿಂದ ಶಕ್ತಿ ಪಡೆಯುತ್ತದೆ:

**`hugo.toml`** — Hugo ಗೆ home route ಗಾಗಿ JSON ಔಟ್‌ಪುಟ್ ನೀಡಲು ಹೇಳುತ್ತದೆ:

```toml
[outputs]
  home = ["HTML", "RSS", "JSON"]
```

**`layouts/index.json`** — ಎಲ್ಲ regular pages ಮೇಲೆ loop ಮಾಡಿ serialize ಮಾಡುವ Hugo template:

```go-html-template
{{- range where site.RegularPages "Section" "ne" "" -}}
  {{- $entry := dict
    "title"       .Title
    "description" .Description
    "content"     (.Plain | truncate 500)
    "url"         .RelPermalink
    "section"     .Section
    "date"        (.Date.Format "Jan 2, 2006")
  -}}
{{- end -}}
```

Index ಪ್ರತಿ ಬಿಲ್ಡ್‌ನಲ್ಲಿ ಮರು-ರಚನೆ ಆಗುತ್ತದೆ, ಆದ್ದರಿಂದ ಹೊಸ ವಿಷಯ ಮುಂದಿನ deploy ನಂತರ ಹುಡುಕಾಟ ಫಲಿತಾಂಶಗಳಲ್ಲಿ ಸ್ವಯಂಚಾಲಿತವಾಗಿ ಕಾಣಿಸಿಕೊಳ್ಳುತ್ತದೆ.

## ಹಂತ 2 — ಹುಡುಕಾಟ ಪುಟ

`content/search.md` ನಲ್ಲಿ ಒಂದು stub ವಿಷಯ ಫೈಲ್ `/search/` route ರಚಿಸುತ್ತದೆ:

```yaml
---
title: "Search"
layout: "search"
sitemap:
  disable: true
---
```

`layout: "search"` frontmatter Hugo ಗೆ ಈ ಪುಟಕ್ಕೆ default single template ಬದಲು `layouts/_default/search.html` render ಮಾಡಲು ಹೇಳುತ್ತದೆ. ಪುಟ sitemap ನಿಂದ ಹೊರಗಿಡಲಾಗಿದೆ ಏಕೆಂದರೆ ಇದು ತನ್ನದೇ indexable ವಿಷಯ ಹೊಂದಿಲ್ಲ.

## ಹಂತ 3 — ಬ್ರೌಸರ್‌ನಲ್ಲಿ Fuse.js

[Fuse.js](https://www.fusejs.io/) ಒಂದು ಹಗುರ (~24 KB) fuzzy-search library. ಇದು ಸಂಪೂರ್ಣವಾಗಿ ಬ್ರೌಸರ್‌ನಲ್ಲಿ ಚಾಲಿಸುತ್ತದೆ. ನೀವು `/search/` ಲೋಡ್ ಮಾಡಿದಾಗ:

1. ಪುಟ `/index.json` fetch ಮಾಡುತ್ತದೆ — ಒಂದು HTTP ವಿನಂತಿ, ಮೊದಲ ಭೇಟಿ ನಂತರ ಬ್ರೌಸರ್ cache ಮಾಡುತ್ತದೆ.
2. Fuse JSON ಡೇಟಾದಿಂದ in-memory index ನಿರ್ಮಿಸುತ್ತದೆ.
3. URL ನಲ್ಲಿ `?q=` parameter ಇದ್ದರೆ, ಯಾವ interaction ಇಲ್ಲದೆ ಹುಡುಕಾಟ ತಕ್ಷಣ ಚಾಲಿಸಿ ಫಲಿತಾಂಶ ಕಾಣಿಸಿಕೊಳ್ಳುತ್ತದೆ.

ಮೂರು ಕ್ಷೇತ್ರಗಳಲ್ಲಿ ಹೊಂದಿಸುವಿಕೆ ತೂಕ ಆಧರಿಸಿ:

| ಕ್ಷೇತ್ರ | ತೂಕ | ಕಾರಣ |
|---|---|---|
| `title` | 60% | ಅತ್ಯಂತ ಬಲವಾದ relevance signal |
| `description` | 30% | ಸಂಪಾದಕೀಯ ಸಾರಾಂಶ |
| `content` | 10% | ಸಂಪೂರ್ಣ-ಪಠ್ಯ fallback |

`threshold: 0.35` ಸೆಟ್ಟಿಂಗ್ ಎಂದರೆ Fuse ಸಣ್ಣ ತಪ್ಪುಗಳನ್ನು ಸಹಿಸುತ್ತದೆ — "coffe" ಹುಡುಕಿದರೂ ಕೂಡ Coorg coffee ಲೇಖನ ಕಾಣಿಸಿಕೊಳ್ಳುತ್ತದೆ.

## ಹಂತ 4 — ಟೈಪ್ ಮಾಡುತ್ತಿರುವಾಗ ಲೈವ್ ಹುಡುಕಾಟ

Index memory ನಲ್ಲಿ ಇದ್ದ ನಂತರ, ಪ್ರತಿ keystroke ಹೊಸ `fuse.search()` call ಚಾಲಿಸುತ್ತದೆ. ಇದು synchronous ಮತ್ತು ತಕ್ಷಣ — ಈ ಗಾತ್ರದಲ್ಲಿ debouncing ಅಗತ್ಯ ಇಲ್ಲ. URL `history.replaceState()` ಮೂಲಕ ಪೇಜ್ reload ಇಲ್ಲದೆ ಸ್ಥಳದಲ್ಲೇ ನವೀಕರಣ ಆಗುತ್ತದೆ, ಆದ್ದರಿಂದ ಪ್ರತಿ ಹುಡುಕಾಟ ಸ್ಥಿತಿ share ಮಾಡಬಹುದಾಗಿದೆ ಮತ್ತು bookmark ಮಾಡಬಹುದು.

## ಹಂತ 5 — Sidebar Widget

Homepage sidebar ನಲ್ಲಿ ಹುಡುಕಾಟ input ಒಂದು ಸರಳ HTML form ಅದು `GET` ಮೂಲಕ `/search/?q=...` ಗೆ submit ಮಾಡುತ್ತದೆ. ಹುಡುಕಾಟ ಪುಟ ಲೋಡ್‌ನಲ್ಲಿ `?q=` parameter ಓದಿ ಸ್ವಯಂಚಾಲಿತವಾಗಿ ಚಾಲಿಸುತ್ತದೆ. Homepage ನಲ್ಲಿ ಹುಡುಕಾಟ widget ಕೆಲಸ ಮಾಡಲು ಯಾವ JavaScript ಅಗತ್ಯ ಇಲ್ಲ.

## ಇದು ಏನು ಮಾಡುವುದಿಲ್ಲ

- **Real-time index ನವೀಕರಣ ಇಲ್ಲ** — CMS ಗೆ ಸೇರಿಸಿದ ವಿಷಯ ಮುಂದಿನ GitHub Actions ಬಿಲ್ಡ್ ಮತ್ತು deploy ನಂತರ ಮಾತ್ರ ಹುಡುಕಾಟದಲ್ಲಿ ಕಾಣಿಸಿಕೊಳ್ಳುತ್ತದೆ. ಸಾಮಾನ್ಯವಾಗಿ ಎರಡು ನಿಮಿಷಗಳಿಗಿಂತ ಕಡಿಮೆ.
- **Phrase matching ಇಲ್ಲ** — Fuse.js ಪ್ರತ್ಯೇಕ tokens ಹೊಂದಿಸುತ್ತದೆ, ನಿಖರ phrases ಅಲ್ಲ. ಹೆಚ್ಚಿನ ಸಂಪಾದಕೀಯ ಬಳಕೆ ಸಂದರ್ಭಗಳಿಗೆ ಇದು ಸಾಕು.
- **Pagination ಇಲ್ಲ** — ಎಲ್ಲ ಫಲಿತಾಂಶ ಒಮ್ಮೆಗೆ render ಆಗುತ್ತದೆ. ಸಣ್ಣದರಿಂದ ಮಧ್ಯಮ ಸೈಟ್‌ಗೆ ಇದು ಸಾಕು.

## ಸಾರಾಂಶ

| ಅಂಶ | ತಂತ್ರಜ್ಞಾನ |
|---|---|
| Index ರಚನೆ | Hugo Pipes + Go templates |
| Index ಸ್ವರೂಪ | JSON (flat array) |
| ಹುಡುಕಾಟ library | Fuse.js v7 (CDN) |
| ಹೋಸ್ಟಿಂಗ್ | GitHub Pages (static) |
| Backend ಅಗತ್ಯ | ಇಲ್ಲ |

ಸಂಪೂರ್ಣ ಹುಡುಕಾಟ ವೈಶಿಷ್ಟ್ಯ ಶೂನ್ಯ ನಡೆಯುತ್ತಿರುವ ವೆಚ್ಚ, ನಿರ್ವಹಿಸಲು ಶೂನ್ಯ infrastructure, ಮತ್ತು index cache ಆದ ನಂತರ offline ಕೆಲಸ ಮಾಡುತ್ತದೆ.
