# luigivis.com

Personal site. Single hand-written `index.html` — no build step, no framework, no runtime dependencies.
Deployed to GitHub Pages from `main`; pushing publishes it live.

## Files

| File | Purpose |
|---|---|
| `index.html` | The whole site. Inline CSS + JS, ES/EN i18n, JSON-LD. |
| `og.html` | Source template for the social preview image. |
| `og.png` | 1200×630 social card (WhatsApp / X / LinkedIn). |
| `CNAME` | Custom domain. |

## Regenerating og.png

`og.html` is rendered headless at 2x, then downscaled for crisp text:

```bash
python3 -m http.server 8080 &
google-chrome --headless=new --disable-gpu --hide-scrollbars \
  --force-device-scale-factor=2 --window-size=1200,630 \
  --virtual-time-budget=6000 --screenshot=og@2x.png \
  "http://localhost:8080/og.html"
magick og@2x.png -filter Lanczos -resize 1200x630 -strip \
  -define png:compression-level=9 og.png
rm og@2x.png
```

After changing `og.png`, bump the `?v=` query in the `og:image` meta tags in
`index.html`. WhatsApp, X and LinkedIn cache scraped previews aggressively and
will keep serving the old card otherwise.

## Keeping content accurate

Package versions on the site are hardcoded and drift silently. Verify before editing:

```bash
curl -s https://repo1.maven.org/maven2/com/luigivismara/<artifact>/maven-metadata.xml
curl -s https://registry.npmjs.org/<package> | jq -r '."dist-tags".latest'
```

## Local preview

```bash
python3 -m http.server 8899
```
