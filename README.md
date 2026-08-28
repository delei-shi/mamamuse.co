# mamamuse.co

Marketing site for Mama Muse — adult jazz dance classes in Palo Alto.

The site is a static export from a Claude Design project ("Mama Muse Mobile
Preview"). `site/index.html` is a Design Component: markup lives inside
`<x-dc>`, page-level styles inside `<helmet>`, and `support.js` hydrates it
in the browser.

## Layout

```
site/
  index.html            page source (Design Component)
  support.js            Design Components runtime
  image-slot.js         image slot helper
  vendor/               React 18.3.1 + React DOM, served locally
  assets/               photographs and route images
wrangler.toml           Cloudflare Workers deploy config
```

## Running it locally

It is plain static files, so any static server works:

```sh
python3 -m http.server 8000 --directory site
```

To exercise the real Cloudflare asset pipeline instead:

```sh
npx wrangler dev
```

## Deploying

Pushes to `main` trigger a Cloudflare Workers build that runs
`npx wrangler deploy` from the repository root. `wrangler.toml` declares an
assets-only Worker — there is no Worker script and no build step, so
Cloudflare just serves `site/` from its asset store.

## Why React is vendored

`support.js` fetches React and React DOM from unpkg.com by default, which
would make every page load depend on a third-party CDN. `index.html` sets
`window.__resources` ahead of the `support.js` tag to redirect those two
URLs to `site/vendor/`. The vendored files come from the npm tarballs for
the same pinned versions, and their SHA-384 digests match the SRI hashes
`support.js` carries for the unpkg URLs, so they are byte-identical to what
it would otherwise have fetched.

`@babel/standalone` is deliberately not vendored. `support.js` loads it only
for JSX passed through `x-import`, and this page has no `x-import` or
`dc-import` tags, so it never loads.
