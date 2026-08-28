# Design canvas working files

Source for the editable canvas of the site, published as a Claude Artifact.
Not part of the deployed site — `wrangler.toml` serves `../site` only.

`Main.dc.html` is a transformed copy of `../site/index.html`:

- the twelve `<image-slot>` elements became plain `<img>` tags, because the
  canvas cannot load `image-slot.js`. Their `shape`/`radius` became
  `border-radius`, `fit` became `object-fit`, and the arch masks stayed
  bound to `{{ heroMask }}` / `{{ portraitMask }}` so the arches tweak still
  works. Inline styles are also what the canvas properties panel edits.

  They lay out in normal flow (`width`/`height: 100%`, `display: block`),
  never `position: absolute`. Not every container is positioned — the WeChat
  QR's is not, and it carries padding — so absolute positioning escapes to a
  distant ancestor and renders the image enormous.
- the `window.__resources` vendoring shim was dropped; the canvas supplies
  its own runtime.
- `./assets/NAME` references became bare `NAME`, matching the image keys.

The images are WebP copies of `../site/assets`, each under 70 KB, because the
canvas embeds every image in the published document (`hero-group7.png` alone
is 1.93 MB, and compresses to 60 KB at quality 86). Only assets the page
actually references are carried here, so unreferenced ones in `../site/assets`
are absent by design.

Note the hero is a plain `<img>` in the export itself, not an `image-slot`, so
ten elements get converted rather than twelve.

Edits made on the canvas do NOT flow back here automatically. Port them into
`../site/index.html` by hand, restoring the real `<image-slot>` elements and
the original PNG references.
