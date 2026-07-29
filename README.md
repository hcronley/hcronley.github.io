# hcronley.github.io

My portfolio, served at <https://hcronley.github.io>.

Jekyll on GitHub Pages with a hand-rolled layout — no remote theme, so the styling is
entirely in `assets/css/main.css` and nothing fights a theme's defaults.

## Layout

```
_config.yml           site metadata, nav, analytics toggle
_layouts/default.html the only layout — header, content, footer
_includes/            analytics.html (no-ops unless google_analytics is set)
assets/css/main.css   all styling, light + dark
assets/imgs/          photos and screenshots
assets/documents/     PDFs to embed or link
index.md              home — intro, project cards, contact
aboutme.md            bio
archive.md            project directory
par-summit.md         case study
nixtla-forecasting.md case study
flash.md              case study
```

## Editing

Pages are plain Markdown with a small front-matter block. To add a case study, copy an
existing one, change the front matter, and add a card to `index.md`.

Nav links live in `_config.yml` under `nav:` — add an entry there rather than editing the
layout.

## Local preview

```bash
bundle install
bundle exec jekyll serve --livereload
```

Then open <http://localhost:4000>.

## Outstanding

Search the repo for `TODO` — the About page, the résumé, LinkedIn, and the forecasting
results are all waiting on real content.
