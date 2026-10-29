# Project Spica

The website at **<https://projectspica.org>**, based on the ceramic mosaic design from
[`CtrlVGustavo/spica-website`](https://github.com/CtrlVGustavo/spica-website) at
`1d26186`. Mission, Roadmap, Research and People are static pages that work without
JavaScript; the theme toggle and client-side navigation are progressive enhancements.

## Development

Install Bun 1.4.0, then:

```sh
bun run dev             # Development server, http://localhost:3000
bun test _source        # Tests
bun run preview         # Preview the static export, http://localhost:3000
```

- `_source/public/`: page template, styles, browser scripts, images and PDFs.
- `_source/articles/`: research article bodies ([notes](_source/articles/README.md)).
- `_source/server.ts`: rendering and the explicit asset allowlist.
- `_source/scripts/build.ts`: static export to `_build/`.

## Publishing

GitHub Pages publishes `main` from the repository root with Jekyll. Bun never runs
on GitHub: the site is built locally and the generated files are committed.

```sh
bun test _source
bun run publish:prepare # Build and copy the export into the repository root
bun run build:check     # Ensure committed output matches the source
# Commit source and generated files together, then push main.
```

- **Edit `_source/`, never the generated root files.**
- `publish:prepare` does not delete files. When removing an asset, delete its root
  copy too, or the `Check website` workflow fails on the Jekyll diff.
- **Do not add `.nojekyll`**: Jekyll's exclusions (`_config.yml`, underscore
  directories, dotfiles) keep tooling out of the deployed site.
- The `Check website` workflow only verifies; deployment is separate and happens
  even when checks fail.

## Compatibility

- Old anchors and `?page=` links (`/#about`, `/#people`, `/?page=roadmap`, …) are
  redirected by `legacy.js`; `/about/` redirects to `/mission/`.
- No analytics or third-party requests. Pages include a meta CSP, since Pages
  cannot set response headers.

## Rollback

The pre-migration site is tagged `pre-mosaic-migration-66d5f26`. To restore it,
revert the commit "Replace website with static ceramic mosaic design" on `main` and
push. History was squashed on 2026-10-28; the previous history is tagged
`pre-squash-a3f852c`.

## Artwork

The header is a ceramic mosaic: hand-cut tesserae around a golden sun over amber
fields, with a night mode (the default) of blue sky, a tiled crescent moon and
sparse dimmed stars. Spica is the dot of the i in the title: one bright tile with a
halo. Click the moon or sun to switch themes.

Generated artwork is committed; `spica-mosaic.svg` and `spica-mosaic-night.svg` in
`_source/public/images/` are the editable sources and are not published. The banner shows the artwork's full height and
picks one of five compositions (1400–6300 px wide) by viewport width;
`constellation.js` adds the Spica star over the title. Breakpoints live in
`_source/public/mosaic-layout.js` and `styles.css`.

Regenerating artwork needs `rsvg-convert` and ImageMagick:

```sh
bun run images:export   # Re-export wider compositions
bun run images:generate # Regenerate all artwork
bun run favicon:export  # Rasterize favicon.svg into favicon.ico (needs Pillow)
```

Optional browser checks (Playwright + Chromium, against `bun run preview`):
`_source/scripts/check-mosaics.mjs` and `_source/scripts/check-navigation.mjs`.
