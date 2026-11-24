# The Accord ended an insurgency. The hills still wait for peace.

The Daily Star, by Mir Rownak. Design & development: Mir Rownak.

**Provisional:** goes live at https://campaign.thedailystar.net/cht-peace-accord on 2 December 2026, the Accord's anniversary. The address and the date are placeholders until the desk confirms them; the page's canonical URL and publish date use them too.

This folder is the finished website: plain static files, no server code, no calls to outside services (fonts and scripts are self-hosted). Every path inside it is relative, so it works at the root of a domain (a Vercel preview) and inside a `/cht-peace-accord/` folder on the live server.

Don't edit these files by hand. They are written by the story's build from the project folder (`story/` → `dist/`).

---

## 1. Put it live

1. Copy **everything in this folder** (except `.git`) into a folder named `cht-peace-accord` at the web root of `campaign.thedailystar.net`, so that `index.html` is served at https://campaign.thedailystar.net/cht-peace-accord/.
   - `vercel.json` and this file aren't needed on the live server. They're harmless if copied.
2. **Keep the trailing slash.** `.../cht-peace-accord` (no slash) must redirect to `.../cht-peace-accord/`. nginx and Apache do this for real folders by default.
3. If the site sits behind Cloudflare, purge the cache for `/cht-peace-accord/` after each upload.

Make sure older servers know `.woff2` as `font/woff2`. Serve HTML, JS, CSS and SVG with gzip or Brotli: the page is measured with compression on.

### Caching

The JavaScript bundle has a content hash in its file name (`app.<hash>.js`), so it never changes under the same name. The CSS is inlined in `index.html`, so there is no CSS file. Fonts, images and the map sprite keep their names and are cached for a week.

| Files | Header |
|---|---|
| hashed files in `js/` | `public, max-age=31536000, immutable` |
| `fonts/ img/ assets/` (names not hashed) | `public, max-age=604800` |
| `index.html` and everything else | `public, max-age=0, must-revalidate` (or `no-cache`) |

**nginx:**

```nginx
location /cht-peace-accord/ {
    index index.html;
    add_header Cache-Control "public, max-age=0, must-revalidate";
}
location ~ ^/cht-peace-accord/(fonts|img|assets)/ {
    add_header Cache-Control "public, max-age=604800";
}
location ~ ^/cht-peace-accord/js/.*[.-][0-9a-f]{8,}\.[a-z0-9]+$ {
    add_header Cache-Control "public, max-age=31536000, immutable";
}
```

**Apache** (a `.htaccess` inside `cht-peace-accord/`, if `AllowOverride` permits it):

```apache
DirectoryIndex index.html
AddType font/woff2 .woff2
<IfModule mod_headers.c>
  Header set X-Content-Type-Options "nosniff"
  <FilesMatch "\.html$">
    Header set Cache-Control "public, max-age=0, must-revalidate"
  </FilesMatch>
  <FilesMatch "\.(woff2|jpg|png|svg|ico)$">
    Header set Cache-Control "public, max-age=604800"
  </FilesMatch>
  <FilesMatch "[.-][0-9a-f]{8,}\.js$">
    Header set Cache-Control "public, max-age=31536000, immutable"
  </FilesMatch>
</IfModule>
```

---

## 2. Search engines and share cards

Already in the page:
- Title, description, author, robots directives (large image previews allowed) and the canonical URL (provisional, above).
- Open Graph and X (Twitter) cards with `img/share.jpg`, a 1200 x 630 headline card.
- `NewsArticle` structured data: headline, description, author, contributor (Mir Rownak), publisher (The Daily Star) and publish date (provisional).

Not in this folder yet: `sitemap.xml` and `robots.txt`. They wait for the final address.

**After it's live:**
1. Google Search Console: submit the sitemap and request indexing of the page.
2. Check the article data at https://search.google.com/test/rich-results.
3. Refresh the share card at https://developers.facebook.com/tools/debug/ ("Scrape Again").

A Vercel preview (`*.vercel.app`) sends `X-Robots-Tag: noindex`, so it never competes with the live page in search. The header is added only for hosts ending in `.vercel.app`; a custom domain on Vercel is indexable.

---

## Folder contents

| Path | What |
|---|---|
| `index.html` | The whole story, every paragraph in the HTML (readable with JavaScript off), with all of its CSS inlined |
| `js/` | One minified, hashed bundle: scroll steps, charts, tooltips |
| `fonts/` | Gelasio (OFL), a Georgia-metric open font for phones without Georgia, cut to the characters the page uses. The page is set in Georgia; devices that have it never load these. The Newsreader and IBM Plex files come with the design kit and are not used by this page. Licences included |
| `assets/map/` | The map outlines (SVG sprite) |
| `img/` | Logo, favicon, share card |
| `vercel.json` | Vercel preview settings only (caching, noindex on `*.vercel.app`) |
