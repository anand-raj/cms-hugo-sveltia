---
title: "Cloudflare Workers ಪರಿಚಯ: Edge ನಲ್ಲಿ Serverless"
date: 2026-04-12T10:40:00+05:30
lastmod: 2026-04-12T10:40:00+05:30
draft: true
description: "Cloudflare Workers ಒಂದು serverless ವಾತಾವರಣ ಅದು JavaScript ಅನ್ನು edge ನಲ್ಲಿ ಯಾವ ಸರ್ವರ್ ನಿರ್ವಹಣೆ ಇಲ್ಲದೆ ಚಾಲಿಸಲು ಅನುವು ಮಾಡಿಕೊಡುತ್ತದೆ. JAMstack ಸೈಟ್‌ಗಳಿಗೆ ಇದು ಹೇಗೆ ಮೌಲ್ಯಯುತ ಎಂದು ತಿಳಿಯಿರಿ."
author: Admin
categories:
  - Tech
tags:
  - cloudflare
  - workers
  - serverless
  - jamstack
  - kannada
image: "https://images.unsplash.com/photo-1569428034239-f9565e32e224?w=1200&fm=webp&q=80"
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

[Cloudflare Workers](https://workers.cloudflare.com/) ಒಂದು serverless ಚಾಲನಾ ವಾತಾವರಣ ಅದು JavaScript ಅನ್ನು edge ನಲ್ಲಿ — Cloudflare ನ 300+ ಡೇಟಾ ಕೇಂದ್ರಗಳ ಜಾಗತಿಕ ನೆಟ್‌ವರ್ಕ್‌ನಲ್ಲಿ — ಯಾವ ಸರ್ವರ್ ನಿರ್ವಹಣೆ ಇಲ್ಲದೆ ಚಾಲಿಸಲು ಅನುಮತಿ ನೀಡುತ್ತದೆ.

JAMstack ಸೈಟ್‌ಗಳಿಗಾಗಿ, Workers ಶುದ್ಧ ಸ್ಟ್ಯಾಟಿಕ್ ಹೋಸ್ಟಿಂಗ್ ತುಂಬಲಾಗದ ಒಂದು ಕೊರತೆ ತುಂಬಿಸುತ್ತದೆ: ಸರ್ವರ್ ಭಾಗದ ತರ್ಕ. OAuth proxies, API middleware, redirects ಮತ್ತು edge caching ಎಲ್ಲ ಸಹಜ ಬಳಕೆ ಸಂದರ್ಭಗಳು.

<!--more-->

## Cloudflare Workers ಹೇಗೆ ಕೆಲಸ ಮಾಡುತ್ತದೆ?

ಸಾಂಪ್ರದಾಯಿಕ serverless functions (AWS Lambda, Google Cloud Functions) ಗಳಿಗಿಂತ ಭಿನ್ನವಾಗಿ, Workers ಬಳಕೆದಾರನಿಗೆ **ಅತ್ಯಂತ ಹತ್ತಿರದ** edge node ನಲ್ಲಿ ಚಾಲಿಸುತ್ತದೆ:

```
ಮುಂಬೈನಲ್ಲಿ ಬಳಕೆದಾರ
      ↓
ವಿನಂತಿ ಮುಂಬೈ Cloudflare edge ಅನ್ನು ತಲುಪುತ್ತದೆ (US-East-1 ಅಲ್ಲ)
      ↓
Worker < 1ms cold start ನಲ್ಲಿ ಚಾಲಿಸುತ್ತದೆ
      ↓
ಅದೇ edge node ನಿಂದ response ಹಿಂತಿರುಗಿಸಲಾಗುತ್ತದೆ
```

Workers **V8 isolate model** ಬಳಸುತ್ತದೆ — Chrome ನಂತೆಯೇ engine — container ಅನ್ನು spin up ಮಾಡುವ ಬದಲು. ಇದರಿಂದ ಸಾಂಪ್ರದಾಯಿಕ serverless functions ಗಿಂತ ಬಹಳ ಕಡಿಮೆ cold start ಸಮಯ ಸಿಗುತ್ತದೆ.

## ಉಚಿತ ಶ್ರೇಣಿ

Cloudflare Workers ಉಚಿತ ಶ್ರೇಣಿ ಹೆಚ್ಚಿನ side projects ಗಿಗೆ ಸಾಕು:

| ಮಿತಿ | ಉಚಿತ ಶ್ರೇಣಿ |
| --- | --- |
| ವಿನಂತಿಗಳು | 1,00,000 / ದಿನ |
| CPU ಸಮಯ | ವಿನಂತಿಗೆ 10ms |
| Workers | ಅಮಿತ |
| ಕಸ್ಟಮ್ ಡೊಮೈನ್‌ಗಳು | 1 |
| Cron ಚಾಲಕಗಳು | 5 |

OAuth proxy ಗಾಗಿ, 1,00,000 ವಿನಂತಿಗಳು/ದಿನ ಪ್ರಾಯೋಗಿಕವಾಗಿ ಅಮಿತ — CMS logins ಅಷ್ಟು ಆಗಿಂದಾಗ್ಗೆ ಇರುವುದಿಲ್ಲ.

## ನೈಜ-ಜಗತ್ತು ಉದಾಹರಣೆ: OAuth Proxy

ಈ ಸೈಟ್ Sveltia CMS ಗಾಗಿ OAuth proxy ಆಗಿ Cloudflare Worker ಬಳಸುತ್ತದೆ. ಬ್ರೌಸರ್ GitHub OAuth client secret ಅನ್ನು ಸುರಕ್ಷಿತವಾಗಿ ಇರಿಸಲಾಗದ ಕಾರಣ, Worker ಸರ್ವರ್ ಭಾಗದಲ್ಲಿ token exchange ನಿರ್ವಹಿಸುತ್ತದೆ:

```javascript
export default {
  async fetch(request, env) {
    const { pathname } = new URL(request.url);

    if (pathname === '/auth') {
      // GitHub OAuth ಗೆ Redirect
      const params = new URLSearchParams({
        client_id: env.GITHUB_CLIENT_ID,
        scope: 'repo,user',
      });
      return Response.redirect(
        `https://github.com/login/oauth/authorize?${params}`
      );
    }

    if (pathname === '/callback') {
      // code ಅನ್ನು token ಗೆ ಬದಲಾಯಿಸಿ
      const { code } = Object.fromEntries(new URL(request.url).searchParams);
      const res = await fetch('https://github.com/login/oauth/access_token', {
        method: 'POST',
        headers: { Accept: 'application/json' },
        body: JSON.stringify({
          code,
          client_id: env.GITHUB_CLIENT_ID,
          client_secret: env.GITHUB_CLIENT_SECRET,
        }),
      });
      const { access_token } = await res.json();
      // postMessage ಮೂಲಕ CMS ಗೆ token ಹಿಂತಿರುಗಿಸಿ
    }
  },
};
```

Client secret **Cloudflare ಎನ್‌ಕ್ರಿಪ್ಟ್ ಪರಿಸರ ಚರಗಳಲ್ಲಿ** ಇರುತ್ತದೆ — ಬ್ರೌಸರ್‌ಗೆ ಎಂದಿಗೂ ಅನಾವರಣ ಆಗುವುದಿಲ್ಲ.

## Worker Deploy ಮಾಡುವುದು

ಸರಳ ಮಾರ್ಗ Cloudflare dashboard ನೇರ:

1. **Workers & Pages → Create → Create Worker** ಗೆ ಹೋಗಿ
2. ಸಂಪಾದಕದಲ್ಲಿ ನಿಮ್ಮ JavaScript ಅಂಟಿಸಿ
3. **Deploy** ಕ್ಲಿಕ್ ಮಾಡಿ
4. **Settings → Variables** ಅಡಿ secrets ಸೇರಿಸಿ

ಸರಳ workers ಗಾಗಿ CLI ಇಲ್ಲ, build step ಇಲ್ಲ, `package.json` ಅಗತ್ಯ ಇಲ್ಲ.

## Workers ಯಾವಾಗ ಬಳಸಬೇಕು?

| ಬಳಕೆ ಸಂದರ್ಭ | ಅತ್ಯುತ್ತಮ ಆಯ್ಕೆ |
| --- | --- |
| OAuth proxy | Cloudflare Worker |
| ಸಂಪೂರ್ಣ API backend | Cloudflare Workers + D1 (SQLite) |
| ಚಿತ್ರ ಮರು-ಗಾತ್ರ | Cloudflare Images ಅಥವಾ Hugo Pipes |
| Form ನಿರ್ವಹಣೆ | Netlify Forms ಅಥವಾ Formspree |
| ನಿಗದಿತ ಕೆಲಸಗಳು | Workers Cron Triggers |

## ತೀರ್ಮಾನ

Cloudflare Workers ಸಂಪೂರ್ಣ ಸ್ಟ್ಯಾಟಿಕ್ JAMstack ಸೈಟ್‌ಗೆ ಯಾವ infrastructure ನಿರ್ವಹಣೆ ಇಲ್ಲದೆ ಸರ್ವರ್ ಭಾಗದ ಸಾಮರ್ಥ್ಯಗಳನ್ನು ತರುತ್ತದೆ. ಉಚಿತ ಶ್ರೇಣಿ ಪ್ರಾಯೋಗಿಕವಾಗಿ ಯಾವ ಕಡಿಮೆ-ದಟ್ಟಣೆ ಬಳಕೆ ಸಂದರ್ಭವನ್ನೂ ಆವರಿಸುತ್ತದೆ, ಮತ್ತು edge-ಮೊದಲ ವಾಸ್ತುಶಿಲ್ಪ ನಿಮ್ಮ ತರ್ಕ ಸಾಧ್ಯವಾದಷ್ಟು ಬಳಕೆದಾರನ ಹತ್ತಿರ ಚಾಲಿಸುತ್ತದೆ.

Hugo ಸೈಟ್‌ಗಳಿಗಾಗಿ, Workers ಅನ್ನು GitHub Actions ಮತ್ತು GitHub Pages ನೊಂದಿಗೆ ಜೋಡಿಸಿ ಸರಿಯಾದ ಸಂಪಾದಕೀಯ CMS ಸೇರಿದ ಸಂಪೂರ್ಣ, ಶೂನ್ಯ-ವೆಚ್ಚ ಪ್ರಕಟೀಕರಣ ಪ್ಲಾಟ್‌ಫಾರ್ಮ್ ಪಡೆಯಬಹುದು.

---

**ಮೂಲಗಳು:**

- [Cloudflare Workers ದಸ್ತಾವೇಜು](https://developers.cloudflare.com/workers/)
- [Workers ಬೆಲೆ ಮತ್ತು ಮಿತಿಗಳು](https://developers.cloudflare.com/workers/platform/limits/)
- [sveltia-cms-auth Worker](https://github.com/sveltia/sveltia-cms-auth)
